# Using Devspace with Openshift Internal Registry

## Devspace Initialization
During devspace initialization, a prompt will appear asking you to choose between a dockerhub image registry, a Github image registry, or an other registry.

Input the route to your openshift-image-registry namespace.

    The route can be retrieved with "oc get route -n openshift-image-registry
    '''
    Warning: running devspace init --reconfigure will recreate the existing devspace.yaml file
    '''

## Modifying the Devspace Yaml
Within the generated dev file, modify the lines after image: to be in the format of *openshift registry url*/*target namespace*/*application name*

Example
'''
version: v1beta9
images:
  app:
    image: **default-route-openshift-image-registry.apps.cluster-cc14.cc14.sandbox1490.opentlc.com/devspace/quickstartpython**
'''

If an unknown certificate authority error occurs on the application pod after running devspace dev, the image url under deployments in the devspace yaml needs to be changed to the internal path to your application image.

This will most likely be image-registry.openshift-image-registry.svc:5000/*namespace*/*application name*

Example
'''
deployments:
- name: quickstart-python
  helm:
    componentChart: true
    values:
      containers:
      - image: **image-registry.openshift-image-registry.svc:5000/devspace/quickstartpython**
'''
