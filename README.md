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

2. In the Console, under Storage, choose PersistentVolumeClaims. On the drop down menu for PersistentVolumeClaim, choose `With Data upload form`. Select the .qcow2 file. Choose a StorageClass that supports FileSystem mode with RWX access such as CephFS or NFS.

