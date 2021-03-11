# Using Devspace with Openshift Internal Registry
1. During devspace initialization, a prompt will appear asking you to choose between a dockerhub image registry, a Github image registry, or an other registry.
Input the route to your openshift-image-registry namespace.
    The route can be retrieved with "oc get route -n openshift-image-registry


