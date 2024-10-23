# Requirement
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

# Import
Create the following [archive DataVolume](https://github.com/kubevirt/containerized-data-importer/blob/main/doc/datavolumes.md#content-type) in your Namespace
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
The data importer can be picky in how it uses [tar](https://github.com/kubevirt/containerized-data-importer/blob/7b330eb75575bfcf06644e0b274547de0f9c125e/pkg/util/util.go#L114)