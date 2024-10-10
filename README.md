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

2. Switch to the create Namespace.

3. Create the following DataVolume in your Namespace
```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
  name: assets
spec:
  source:
      http:
         url: "https://github.com/RedHatNordicsSA/from-0-to-virt-hero-2024-assets/raw/refs/heads/main/pdf.tar"
  contentType: archive
  pvc:
    accessModes:
      - ReadWriteMany
    storageClassName: managed-nfs-storage
    resources:
      requests:
        storage: 50Mi
```

X. Create VirtualMachineInstance with [ConfigMap mounted as a disk](https://kubevirt.io/user-guide/storage/disks_and_volumes/#as-a-disk)


