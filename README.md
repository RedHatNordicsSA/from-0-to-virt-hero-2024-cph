# from-0-to-virt-hero-2024-cph
Artifacts for the 2024 Summit Connect CPH Virt demo


## Demo environment

### Requirement
- The oc CLI.

### Optional
- The kubevirt CLI. If running from macOS, you will likely need to approve the virtctl binary `xattr -dr com.apple.quarantine virtctl`.

### Steps

1. Create a Namespace, say `webserver`

2. Switch to the newly created Namespace.
```shell
oc project webserver
```

3. Create NGINX Deployment, ConfigMap, Secret and Services from Helm
```shell
helm template nginx | oc create -f - 
```

4. Grant privileges to import template data volume
```shell
oc create -f clusterrole-dv-clone.yaml
oc create -f rolebinding-dv-clone.yaml
```

5. Create VirtualMachineInstance with [ConfigMap mounted as a disk](https://kubevirt.io/user-guide/storage/disks_and_volumes/#as-a-disk)
Name it `nginx-vm`.
Use cloud-init script from `secret-nginx-vm-cloudinit.yaml`, the device UID for the ConfigMap (NGINC conf) and Secret (PDF files) must match between the volumes mounted in the VM and the mount command passed to cloud-init

6. Create Route
Get default Ingress domain
```shell
oc get ingresses.config/cluster -o jsonpath={.spec.domain}
```
Edit `route.yaml` to use this domain in `spec.host` then
```shell
oc create -f route.yaml
```

7. Generate some traffic
```shell
export APP_URL=$(oc get route -o jsonpath='{.items[0].spec.host}')
for n in {1..100}; do curl -I https://$APP_URL; done
```


