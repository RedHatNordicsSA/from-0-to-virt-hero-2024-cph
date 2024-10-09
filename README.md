# from-0-to-virt-hero-2024-cph
Artifacts for the 2024 Summit Connect CPH Virt demo


## Demo environment

### Requirement
- StorageClass supporting FileSystem mode and RWX.
- If checking the box further down to use opimized settings, ensure your StorageProfile has matching features, it should contain:
```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: StorageProfile
spec:
  claimPropertySets:
    - accessModes:
        - ReadWriteMany
      volumeMode: Filesystem
```

### Steps

1. Create Namespace

2. Create the DataVolume resource from this repo.

3. Use the following command to upload your qcow2 file to the DataVolume
```shell
virtctl image-upload dv web-assets --size=50Mi --image-path=data_disk.qcow2
```
(If running from macOS, you will likely need to approve the virtctl binary `xattr -dr com.apple.quarantine`)

