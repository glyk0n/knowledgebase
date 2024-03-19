PVC stuck in terminating state while deletion
====

## Environment

Red Hat OpenShift Container Platform 4.x

## Issue

PVC is stuck in a terminating state after deleting it using the command `oc delete pvc <pvc-name>`.

## Resolution

Remove the finalizer 'kubernetes.io/pvc-protection' from the PVC

```
# Update a PVC stuck in terminating status
oc patch pvc <pvc_name> -p '{"metadata":{"finalizers":null}}'
```

## Root cause

The finalizer 'kubernetes.io/pvc-protection' set in the object. This means that as long as there is a pod backing this PVC, the controller will not be able to delete the object. This is working as intended

## Diagnostic Steps

Check the yaml file of the pvc if the finalizer 'kubernetes.io/pvc-protection' is set in the object.

```
$ oc get pvc <pvc_name> -o yaml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim 
  finalizers:
  - kubernetes.io/pvc-protection
```
