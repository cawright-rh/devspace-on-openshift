# Using Devspace with Openshift Internal Registry
This repository uses the python example application from [devspace](https://devspace.sh/cli/docs/introduction)

This repository already contains the devspace.yaml, therefor *devspace init* does not need to be run and the devspace.yaml file can be modified instead.

If you choose to use the provided devspace.yaml file you will need to make the following changes
1. The image url's will need to be changed to your cluster/namespace/application-name
    1. Follow steps under **Configuring devspace to use Openshift's image registry.**
1. The steps under **Updating the devspace secret** will need to be followed
    1. The secret may not appear until devspace dev has been run at least once. the initial run will likely fail. The failed pod can be deleted with devspace purge.


## Devspace Initialization
Initialization is not necessary if the devspace file already exists, the purpose of initializing is to generate the devspace file.

During devspace initialization, a prompt will appear asking you to choose between a dockerhub image registry, a Github image registry, or an other registry.

Input the route to your openshift-image-registry namespace.

The route can be retrieved with "oc get route -n openshift-image-registry

```   
Warning: running devspace init --reconfigure will recreate the existing devspace.yaml file.
```

## Configuring devspace to use OpenShift's image registry
Within the generated dev file, modify the lines after image: to be in the format of *openshift registry url*/*target namespace*/*application name*

Example

```
version: v1beta9
images:
  app:
    image: default-route-openshift-image-registry.apps.cluster-cc14.cc14.sandbox1490.opentlc.com/devspace/quickstartpython
```

If an unknown certificate authority error occurs in the application pod after running devspace dev, the image url under deployments in the devspace yaml needs to be changed to the internal path to your application image.

This will most likely be image-registry.openshift-image-registry.svc:5000/*namespace*/*application name*

Example

```
deployments:
- name: quickstart-python
  helm:
    componentChart: true
    values:
      containers:
      - image: image-registry.openshift-image-registry.svc:5000/devspace/quickstartpython
```

## Updating the devspace secret
While running devspace dev you will likely run into a permissions error when the image is being pushed to OpenShift's image registry.

To fix this, you will need to modify the secret generated on the devspace targeted namespace. 

1. Navigate to the Secrets sidebar menu within the openshift UI.
1. Select the namespace you are deploying to from the project dropdown menu.
1. Find the devspace secret which will be similar to *devspace-auth-default-route-openshift-image-registry-#########*
1. Click on the secret name to view the details. Once viewing the details select *Edit Secret* from the *Actions* dropdown menu at the top right.
1. Input the desired user into the Username field.
1. For the password, an API token is required. 
    1. This can be retrieved by clicking on your username in the top right of the Openshift UI and selecting *Copy Login Command*.
    1. After authenticating, copy the token under *Your API token is*
1. paste the API token into the password field in the pull secret.
1. click save.

If you are using Kaniko as your building tool. You will need to add build configuration information to the devfile which can be found [Here](https://devspace.sh/cli/docs/configuration/images/kaniko) under the pullSecret section

Example
```
images:
  app:
    image: default-route-openshift-image-registry.apps.cluster.com/devspace/quickstartpython
    ...
    build:
      kaniko:
        insecure: true
        pullSecret: devspace-auth-default-route-openshift-image-registry-#######
```

## Specifying Tags
If the application is deployed successfuly, but devspace cannot find the pod in order to establish a connection you may need to specify the image tag.

Example:
```
images:
  app:
    image: default-route-openshift-image-registry.apps.cluster.com/devspace/quickstartpython
    tags:
    - latest
```
Depending on the tagging method specified, it may also be necessary to include the tag at the end of the deployment image url.
Such as image-registry.openshift-image-registry.svc:5000/*namespace*/*application name*:*tag*

Example:
```
deployments:
- name: quickstart-python
  helm:
    componentChart: true
    values:
      containers:
      - image: image-registry.openshift-image-registry.svc:5000/devspace/quickstartpython:tag
```
