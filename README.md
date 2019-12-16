# tubee Helm Chart

## Prerequisites Details

* Kubernetes 1.9+
* PV support on the underlying infrastructure

## Chart Details

This chart implements [tubee](https://github.com/gyselroth/tubee) with all necessary and optional features as sub charts.
This charts includes the following sub charts:

* tubee server
* tubee server proxy
* tubee jobs

The following sub charts get installed as dependencies from helm stable if enabled (which is the default):

* [MongoDB replicaSet](https://github.com/helm/charts/tree/master/stable/mongodb-replicaset)

tubee sub charts do not require persistent volumes however the tubee server is dependent on MongoDB and therefore a pv infrastructure is required
which also applies to the optional elasticsearch integration.

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm repo add tubee-stable https://gyselroth.github.io/tubee-helm/stable
helm install tubee-stable/tubee --name my-release --namespace mynamespace
```

Example deployment with ingress/tls enabled and 50GB MongoDB storage:

```console
helm install tubee-stable/tubee --name my-release --namespace mynamespace \
    --set tubee-proxy.ingress.enabled=true \
    --set tubee-proxy.ingress.host=tubee.local \
    --set tubee-proxy.ingress.tls[0].secretName=tls-tubee.local \
    --set tubee.url=https://tubee.local \
    --set mongodb.persistentVolume.size=50Gi \
```

The tubee should now be installed and available. You may authenticate using the default admin user:

Username: admin<br/>
Password: admin<br/>

## Configuration

The following table lists the configurable parameters of the tubee sub charts and their default values.

(Note: Only the configuration for the tubee server sub chart is documented so far)


| Parameter                           | Description                                                               | Default                                             |
| ----------------------------------- | ------------------------------------------------------------------------- | --------------------------------------------------- |
| `tubee.replicaCount`              | Number of replicas in the replica set                                     | `2`                                                 |
| `tubee.fullnameOverrride`         | Override deployment name                                                  | `""`                                                |
| `tubee.nameOverride`              | Override deployment name                                                  | `""`                                                |
| `tubee.url`                       | The URL of tubee under which the server is reachable from outside       | `https://tubee.local`                             |
| `tubee.image.repository`          | Image repository and name                                                 | `gyselroth/tubee`                                 |
| `tubee.image.tag`                 | Image tag for the install container                                       | `2.6.7`                                             |
| `tubee.image.pullPolicy`          | Image pull policy for the init container that establishes the replica set | `IfNotPresent`                                      |
| `tubee.service.type`              | Service Type                                                              | `ClusterIP`                                         |
| `tubee.service.port`              | Service Port (tubee < v3 is executed using PHP-FPM)                     | `9000`                                              |
| `tubee.extraLabels`               | Extra labels                                                              | `{}`                                                |
| `tubee.extraVars`                 | Extra env variables                                                       | `{}`                                                |
| `tubee.nodeSelector`              | Custom node selector                                                      | `""`                                                |
| `tubee.tolerations`               | Tolerations                                                               | `[]`                                                |
| `tubee.resources`                 | Pod resource requests and limits                                          | `{}`                                                |
| `tubee.affinity`                  | Affinitiy                                                                 | `{}`                                                |


## Installing pre releases

Besides the stable repository there is a repository which holds unstable tubee charts. 
Unstable charts also include all tubee pre-releases (Usually alpha and beta versions).

To install an unstable chart with the release name `my-release`:

```console
helm repo add tubee-unstable https://gyselroth.github.io/tubee-helm/unstable
helm install tubee-unstable/tubee --name my-release --namespace mynamespace
```

## Release new charts

Upgrade the sub charts first which have changes.

Pack chart and update dependencies:
```
helm package -d stable stable/tubee
```

Update repository index:
```
helm repo index stable/
```
