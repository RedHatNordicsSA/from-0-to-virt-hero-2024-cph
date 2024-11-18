# from-0-to-virt-hero-2024-cph
Artifacts for the 2024 Summit Connect CPH Virt demo


## Demo environment

### Requirement
- The oc CLI.

### Optional
- The kubevirt CLI. If running from macOS, you will likely need to approve the virtctl binary `xattr -dr com.apple.quarantine virtctl`.

### Steps

1. Create a Namespace, say `webserver`
```shell
oc new-project webserver
```
Switch to the newly created Namespace (if not already switched to it).
```shell
oc project webserver
```

2. Grant privileges to import template data volume
You'll likely need to modify the Namespace value in the `RoleBinding`.
```shell
oc create -f clusterrole-dv-clone.yaml
oc create -f rolebinding-dv-clone.yaml
```

3. Create resoure to run NGINX as a Pod and VM from Helm
You can specify the VM DataSource and template via Values under `.vm`.
```shell
helm template nginx | oc create -f - 
```
You might need to create the Secret containing PDF files manually if Helm packaging/release process is used and respects `.helmignore` which exceludes that Secret for size concerns.
At this point you should have a working Nginx Pod and VM capable of serving the static files but only accessible via a Service, the Route will be created later.
A randomly generated password for the VM was created and can be found in the `nginx-vm-cloudinit` Secret.

4. Create Route
Get default Ingress domain
```shell
oc get ingresses.config/cluster -o jsonpath={.spec.domain}
```
Edit `route.yaml` to use this domain in `spec.host` then
```shell
oc create -f route.yaml
```

5. Generate some traffic
```shell
export APP_URL=$(oc get route -o jsonpath='{.items[0].spec.host}')
for n in {1..100}; do curl -I https://$APP_URL; done
```


