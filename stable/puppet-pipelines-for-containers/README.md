## Prerequisites Details

* Puppet Pipelines for Containers trial license [get a trial one from here](https://licenses.puppet.com)

## Chart Details
This chart will do the following:

* Deploy Puppet Pipelines for Containers (PPFC)
* Deploy a MySQL database using the stable/mysql chart
* Optionally expose PPFC with Ingress [Ingress documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)

## Installing the Chart

To install the chart with the release name `ppfc`:

```bash
$ helm install --name ppfc stable/puppet-pipelines-for-containers
```

### Accessing PPFC
**NOTE:** It might take a few minutes for PPFC's public IP to become available.
Follow the instructions outputted by the install command to get the PPFC IP to access it.

### Updating PPFC
Once you have a new chart version, you can update your deployment with
```bash
$ helm upgrade ppfc --namespace ppfc stable/puppet-pipelines-for-containers
```

This will apply any configuration changes on your existing deployment.

### PPFC memory and CPU resources
The PPFC Helm chart comes with support for configured resource requests and limits to PPFC and MySQL. By default, these settings are commented out.
It is **highly** recommended to set these so you have full control of the allocated resources and limits.

```bash
# Example of setting resource requests and limits to all pods (including passing java memory settings to PPFC)
$ helm install --name ppfc \
               --set ppfc.resources.requests.cpu="500m" \
               --set ppfc.resources.limits.cpu="2" \
               --set ppfc.resources.requests.memory="1Gi" \
               --set ppfc.resources.limits.memory="4Gi" \
               stable/puppet-pipelines-for-containers
```
Get more details on configuring PPFC in the [official documentation](https://puppet.com/docs/pipelines-for-containers/enterprise/).


### Customizing Database password
You can override the specified database password (set in [values.yaml](values.yaml)), by passing it as a parameter in the install command line
```bash
$ helm install --name ppfc --namespace ppfc --set mysql.mysqlPassword=12_hX34qwerQ2 stable/puppet-pipelines-for-containers
```

You can customize other parameters in the same way, by passing them on `helm install` command line.

### Deleting PPFC
```bash
$ helm delete --purge ppfc
```
This will completely delete your PPFC deployment.

**IMPORTANT:** This will also delete your data volumes. You will lose all data!

### Custom Docker registry for your images
If you need to pull your Docker images from a private registry, you need to create a
[Kubernetes Docker registry secret](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/) and pass it to helm
```bash
# Create a Docker registry secret called 'regsecret'
$ kubectl create secret docker-registry regsecret --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
```
Once created, you pass it to `helm`
```bash
$ helm install --name artifactory --set imagePullSecrets=regsecret stable/artifactory
```

## Configuration

The following table lists the configurable parameters of the artifactory chart and their default values.

|         Parameter         |           Description             |                         Default                          |
|---------------------------|-----------------------------------|----------------------------------------------------------|
| `imagePullSecrets`        | Docker registry pull secret       |                                                          |
| `serviceAccount.create`   | Specifies whether a ServiceAccount should be created | `true`                               |
| `serviceAccount.name`     | The name of the ServiceAccount to create             | Generated using the fullname template |
| `rbac.create`             | Specifies whether RBAC resources should be created   | `true`                               |
| `rbac.role.rules`         | Rules to create                   | `[]`                                                     |
| `ppfc.name`        | PPFC name                  | `artifactory`                                            |
| `ppfc.image.pullPolicy`         | Container pull policy             | `IfNotPresent`                              |
| `ppfc.image.repository`    | Container image                   | `puppet/pipelines-for-containers`        |
| `ppfc.image.version`       | Container tag                     |  `3.3.10-516353`                                        |
| `ppfc.service.name`| PPFC service name to be set in Nginx configuration | `ppfc`                    |
| `ppfc.service.type`| PPFC service type | `ClusterIP`                                                       |
| `ppfc.externalConsolePort` | PPFC external console port | `8080`                                                  |
| `ppfc.internalConsolePort` | PPFC service internal console port | `8080`                                                  |
| `ppfc.externalBackendPort` | PPFC service external backend port | `8000`                                                  |
| `ppfc.internalBackendPort` | PPFC service internal backend port | `8000`                                                  |
| `ppfc.externalAgentServicePort` | PPFC service external agent port | `7000`                                                  |
| `ppfc.internalAgentServicePort` | PPFC service internal agent port | `7000`                                                  |
| `ppfc.livenessProbe.enabled`              | Enable liveness probe                     | `false`                    |
| `ppfc.livenessProbe.initialDelaySeconds`  | Delay before liveness probe is initiated  | 180                       |
| `ppfc.livenessProbe.periodSeconds`        | How often to perform the probe            | 10                        |
| `ppfc.livenessProbe.timeoutSeconds`       | When the probe times out                  | 10                        |
| `ppfc.livenessProbe.successThreshold`     | Minimum consecutive successes for the probe to be considered successful after having failed. | 1 |
| `ppfc.livenessProbe.failureThreshold`     | Minimum consecutive failures for the probe to be considered failed after having succeeded.   | 10 |
| `ppfc.readinessProbe.enabled`             | would you like a readinessProbe to be enabled           |  `false`     |
| `ppfc.readinessProbe.initialDelaySeconds` | Delay before readiness probe is initiated | 60                        |
| `ppfc.readinessProbe.periodSeconds`       | How often to perform the probe            | 10                        |
| `ppfc.readinessProbe.timeoutSeconds`      | When the probe times out                  | 10                        |
| `ppfc.readinessProbe.successThreshold`    | Minimum consecutive successes for the probe to be considered successful after having failed. | 1 |
| `ppfc.readinessProbe.failureThreshold`    | Minimum consecutive failures for the probe to be considered failed after having succeeded.   | 10 |
| `ppfc.resources.requests.memory` | PPFC initial memory request                  |                          |
| `ppfc.resources.requests.cpu`    | PPFC initial cpu request     |                                          |
| `ppfc.resources.limits.memory`   | PPFC memory limit            |                                          |
| `ppfc.resources.limits.cpu`      | PPFC cpu limit               |                                          |
| `ingress.enabled`           | If true, PPFC Ingress will be created | `false`                                     |
| `ingress.annotations`       | PPFC Ingress annotations     | `{}`                                                 |
| `ingress.hosts`             | PPFC Ingress hostnames       | `[]`                                                 |
| `ingress.tls`               | PPFC Ingress TLS configuration (YAML) | `[]`                                        |
| `ingressAgent.enabled`           | If true, PPFC Ingress for agent will be created | `false`                                     |
| `ingressAgent.annotations`       | PPFC Ingress agent annotations     | `{}`                                                 |
| `ingressAgent.hosts`             | PPFC Ingress agent hostnames       | `[]`                                                 |
| `ingressAgent.tls`               | PPFC Ingress agent TLS configuration (YAML) | `[]`                                        |
| `ingressBackend.enabled`           | If true, PPFC Ingress for backend will be created | `false`                                     |
| `ingressBackend.annotations`       | PPFC Ingress backend annotations     | `{}`                                                 |
| `ingressBackend.hosts`             | PPFC Ingress backend hostnames       | `[]`                                                 |
| `ingressBackend.tls`               | PPFC Ingress backend TLS configuration (YAML) | `[]`                                        |
| `mysql.persistence.enabled`                        | Create a volume to store data                                                                | true                                                 |
| `mysql.mysqlRootPassword`                          | Password for the `root` user. Ignored if existing secret is provided                         | Random 10 characters                                 |
| `mysql.mysqlUser`                                  | Username of new user to create.                                                              | `ppfc`                                                |
| `mysql.mysqlPassword`                              | Password for the new user. Ignored if existing secret is provided                            | `password1`                                 |
| `mysql.mysqlDatabase`                              | Name for new database to create.                                                             | `ppfc`                                                |
| `mysql.persistence.enabled`                          | Is the default datbase used                         | `true`                                 |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

### Ingress and TLS
To get Helm to create an ingress object(s) with a hostname(s), add these lines to your Helm command:
```
helm install --name ppfc \
  --set ingress.enabled=true \
  --set ingress.hosts[0]="ppfc.company.com" \
  --set ingressBackend.enabled=true \
  --set ingressBackend.hosts[0]="ppfc-backend.company.com" \
  --set ingressAgent.enabled=true \
  --set ingressAgent.hosts[0]="ppfc-agent.company.com" \
  --set ppfc.service.type=NodePort \
  stable/puppet-pipelines-for-containers
```

If your cluster allows automatic creation/retrieval of TLS certificates (e.g. [cert-manager](https://github.com/jetstack/cert-manager)), please refer to the documentation for that mechanism.

To manually configure TLS, first create/retrieve a key & certificate pair for the address(es) you wish to protect. Then create a TLS secret in the namespace:

```console
kubectl create secret tls ppfc-tls --cert=path/to/tls.cert --key=path/to/tls.key
```

Include the secret's name, along with the desired hostnames, in the PPFC Ingress TLS section of your custom `values.yaml` file:

```
  ingress:
    ## If true, PPFC Ingress will be created
    ##
    enabled: true

    ## PPFC Ingress hostnames
    ## Must be provided if Ingress is enabled
    ##
    hosts:
      - ppfc.domain.com
    annotations:
      kubernetes.io/tls-acme: "true"
    ## PPFC Ingress TLS configuration
    ## Secrets must be manually created in the namespace
    ##
    tls:
      - secretName: ppfc-tls
        hosts:
          - ppfc.domain.com
```
