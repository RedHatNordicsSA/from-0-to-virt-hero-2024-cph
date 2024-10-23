# from-0-to-virt-hero-2024-cph
Artifacts for the 2024 Summit Connect CPH Virt demo


## Demo environment

### Requirement
- StorageClass supporting FileSystem mode and RWX such as CephFS or NFS.
- To ensure that the CDI uses the most appropriate settings for that StorageClass, ensure your StorageProfile has matching features, it should contain:
```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: StorageProfile
spec:
  claimPropertySets:
    - accessModes:
        - ReadWriteMany
      volumeMode: Filesystem
```
- Modify the HyperConverged CR named `kubevirt-hyperconverged` in the `openshift-cnv` Namespace to specify a `spec.scratchSpaceStorageClass` value with the StorageClass choosen in the previous steps.
- The oc CLI.

### Optional
- The kubevirt CLI. If running from macOS, you will likely need to approve the virtctl binary `xattr -dr com.apple.quarantine virtctl`.

### Steps

1. Create a Namespace, say `webserver`

2. Switch to the newly created Namespace.

3. Create Nginx configuration ConfigMap
```shell
oc create -f configmap-nginx-conf.yaml
```

4. Create data assets from Secret
```shell
oc create -f secret-pdf-files.yaml
```

5. Create NGINX Deployment and Services from Helm
```shell
helm template nginx | oc create -f - 
```

6. Grant privileges to import template data volume
```shell
oc create -f clusterrole-dv-clone.yaml
oc create -f rolebinding-dv-clone.yaml
```

7. Create VirtualMachineInstance with [ConfigMap mounted as a disk](https://kubevirt.io/user-guide/storage/disks_and_volumes/#as-a-disk)
Name it `nginx-vm`.
Use cloud-init script from `secret-nginx-vm-cloudinit.yaml`, the device UID for the ConfigMap (NGINC conf) and Secret (PDF files) must match between the volumes mounted in the VM and the mount command passed to cloud-init

8. Create Route
Get default Ingress domain
```shell
oc get ingresses.config/cluster -o jsonpath={.spec.domain}
```
Edit `route.yaml` to use this domain in `spec.host` then
```shell
oc create -f route.yaml
```


