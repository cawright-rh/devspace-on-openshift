# Using Devspace with Openshift Internal Registry

## Devspace Initialization
During devspace initialization, a prompt will appear asking you to choose between a dockerhub image registry, a Github image registry, or an other registry.

Input the route to your openshift-image-registry namespace.

    The route can be retrieved with "oc get route -n openshift-image-registry
    '''
    Warning: running devspace init --reconfigure will recreate the existing devspace.yaml file
    '''

## Modifying the Devspace Yaml
Within the generated dev file, modify the lines after image: to be in the format of <openshift registry url>/<target namespace>/<application name>


