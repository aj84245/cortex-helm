# Cortex XDR Helm Chart

## Installing Cortex XDR helm chart

### Add the Cortex XDR helm repository
```
helm repo add paloaltonetworks https://paloaltonetworks.github.io/cortex-helm
```

### Verify that the repository was added to helm repo cache
```
helm repo list | grep paloaltonetworks
```

### List all available charts in the repo
```
helm search repo --versions paloaltonetworks
```

### Install/Upgrade the helm chart

* Create a new namespace (The name `cortex-xdr` is not mandatory and can be changed):
```
kubectl create namespace cortex-xdr
```

* Parameters **required** for the chart installation (not for upgrade):
    - docker config json value or name of the secret that contains the value.
    - image repository url.
    - distribution id.

**Below examples are also retrievable from the private cortex tenant with all the above parameters already supplied.**

#### Notes

- If no namespace is set in the installation command, the namespace will be `default`.
- New docker pull secret is created by the chart so the chart expects to receive its value,
this can be canceled by setting the parameters `secret.dockerPullSecret.create` to false (then the name of the secret will need to be supplied).
- On upgrade, the only required parameter is `namespace`. Specifying other parameters will override the existing values.

Classic installation command:
```
helm upgrade --install <release_name> <helm_chart> \
  --namespace=cortex-xdr \
  --set daemonset.image.repository=<repository_url> \
  --set agent.distributionId=<distribution_id> \
  --set dockerPullSecret.create=true \
  --set dockerPullSecret.value=<docker_config_json_secret>
```

Note: to pick a specific version, use the `--version` flag.

If the secret was created seperately then you can just supply the secret name (make sure the secret and the agent are in the same namespace):
```
helm upgrade --install <release_name> <helm chart> \
  --namespace=<namespace> \
  --set daemonset.image.repository=<repository_url> \
  --set agent.distributionId=<distribution_id> \
  --set dockerPullSecret.name=<docker_config_json_secret>
```

Upgrade example:
```
helm repo update
```

```
helm upgrade --install <release_name> <helm_chart> --reuse-values \
  --namespace=<namespace>
```

Even when using `--reuse-values` (which uses the values of the previous installation) you can still override any value that you want with the `--set` option.

### More installation Parameters
|Parameter                               | Description                                                                                                |
|----------------------------------------|------------------------------------------------------------------------------------------------------------|
| `daemonset.image.repository`           | Cortex image Repository URL (Required)                                                                     |
| `dockerPullSecret.create`              | Create/Don't create docker config json pull secret and insert the value in it                              |
| `dockerPullSecret.value`               | Docker config json value for the docker pull secret (Required)                                             |
| `dockerPullSecret.name`                | Docker config json secret name (Required if value isn't supplied)                                          |
| `agent.distributionId`                 | Distribution id of the tenant (Required)                                                                   |
| `agent.distributionServer`             | Distribution server URL (set by default in the image)                                                      |
| `agent.proxyList`                      | List of proxies that the agent will use (e.g `--set daemonset.proxyList="10.0.0.1:8000,10.0.0.2:9000"`)    |
| `agent.nodeSelector`                   | Node selector (e.g `--set daemonset.nodeSelector.<key=value>`, each key+value will need their own `--set`) |
| `serviceAccount.openshift.scc.create`  | Enable `SecurityConstraintsContext` for openshift platform (Required when installing on openshift          |

## Uninstalling Cortex XDR helm chart

```
helm uninstall <release_name> --namespace <namespace>
```
