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
- The oc CLI.
- The kubevirt CLI. If running from macOS, you will likely need to approve the virtctl binary `xattr -dr com.apple.quarantine virtctl`.

### Steps

1. Create a Namespace, say `webserver`

2. Switch to the create Namespace.

3. Create the DataVolume, named `web-assets`, from this repo.

4. Use the following command to upload your qcow2 file to the DataVolume
```shell
virtctl image-upload dv web-assets --size=50Mi --image-path=data_disk.qcow2
```


