[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/rocketadmin)](https://artifacthub.io/packages/search?repo=rocketadmin)

## Installation

To install the Helm chart, use the following command:

```bash
helm install my-release rocketadmin/rocketadmin
```

## Configuration

The following table lists the configurable parameters of the RocketAdmin chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | Image repository | `rocketadmin/rocketadmin` |
| `image.tag` | Image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `ingress.enabled` | Enable ingress | `false` |
| `ingress.annotations` | Ingress annotations | `{}` |
| `ingress.hosts` | Ingress hosts | `[]` |
| `ingress.tls` | Ingress TLS configuration | `[]` |
| `resources` | Resource requests and limits | `{}` |
| `nodeSelector` | Node labels for pod assignment | `{}` |
| `tolerations` | Toleration labels for pod assignment | `[]` |
| `affinity` | Affinity settings for pod assignment | `{}` |

For more information on how to configure the RocketAdmin Helm chart, refer to the [documentation](https://github.com/rocketadmin/rocketadmin-chart).
