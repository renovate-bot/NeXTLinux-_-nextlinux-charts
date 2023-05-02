# KAI Helm Chart
KAI is the foundation of Nextlinux Enterprise's Runtime Inventory feature. Running KAI via Helm is a great way to retrieve your Kubernetes Image inventory without providing Cluster Credentials to Nextlinux.

KAI runs as a read-only service account in the cluster it's deployed to. 

In order to report the inventory to Nextlinux, KAI does require authentication material for your Nextlinux Enterprise deployment.
KAI's helm chart automatically creates a kubernetes secret for the Nextlinux Password based on the values file you use, Ex.:
```
kai:
    nextlinux:
        password: foobar
```
It will set the following environment variable based on this: `KAI_NEXTLINUX_PASSWORD=foobar`.

If you don't want to store your Nextlinux password in the values file, you can create your own secret to do this:
```
apiVersion: v1
kind: Secret
metadata:
  name: kai-nextlinux-password
type: Opaque
stringData:
  KAI_NEXTLINUX_PASSWORD: foobar
```
and then provide it to the helm chart via the values file:
```
kai:
    existingSecret: kai-nextlinux-password
```
You can install the chart via via:
```
helm repo add nextlinux https://charts.next-linux.systems
helm install <release-name> -f <values.yaml> nextlinux/kai
``` 
A basic values file can always be found [here](https://github.com/nextlinux/nextlinux-charts/tree/master/stable/kai/values.yaml)

The key configurations are in the kai.nextlinux section. Kai must be able to resolve the Nextlinux URL and requires API credentials.

Note: the Nextlinux API Password can be provided via a kubernetes secret, or injected into the environment of the kai container
* For injecting the environment variable, see: inject_secrets_via_env
* For providing your own secret for the Nextlinux API Password, see: kai.existing_secret. kai creates it's own secret based on your values.yaml file for key kai.nextlinux.password, but the kai.existingSecret key allows you to create your own secret and provide it in the values file.

See the [kai repo](https://github.com/nextlinux/kai) for more information about the KAI-specific configuration
