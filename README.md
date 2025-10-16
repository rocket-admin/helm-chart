# RocketAdmin Helm Chart

[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/rocketadmin)](https://artifacthub.io/packages/search?repo=rocketadmin)

## UPGRADE NOTICE

Starting with version 1 default behaviour changes to pglite. If you need to keep previous behaviour you need to 

## Overview

This Helm chart is used to deploy RocketAdmin, a comprehensive administration tool for managing databases, on a Kubernetes cluster. This chart configures and manages the necessary Kubernetes resources such as Deployments, Services, and ConfigMaps.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+

## Installation

To install the chart with the release name `my-release`:

```bash
helm repo add rocketadmin https://charts.rocketadmin.com
helm install rocketadmin ./rocketadmin
```

The command deploys RocketAdmin on the Kubernetes cluster with the default configuration. The [Configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstallation

To uninstall/delete the `my-release` deployment:

```bash
helm uninstall rocketadmin
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the RocketAdmin chart and their default values. If you want to keep existing behaviour - set postgresql.enabled to true

| Parameter                          | Description                                                  | Default                       |
| ---------------------------------- | ------------------------------------------------------------ | ----------------------------- |
| `auth.password`                    | Password used between rocketadmin and internal postgres db   | `"changeme"`                  |
| `email.enabled`                    | Enable email notifications                                   | `false`                       |
| `email.host`                       | SMTP server hostname                                         | `""`                          |
| `email.port`                       | SMTP server port                                             | `587`                         |
| `email.username`                   | SMTP server username                                         | `""`                          |
| `email.password`                   | SMTP server password                                         | `""`                          |
| `email.from`                       | Email address to send from                                   | `""`                          |
| `config.openai_api_key`            | OpenAI API Key (optional)                                    | `""`                          |
| `replicaCount`                     | Number of RocketAdmin pods to deploy                         | `1`                           |
| `image.repository`                 | RocketAdmin image repository                                 | `rocketadmin/rocketadmin`     |
| `image.tag`                        | RocketAdmin image tag                                        | `latest`                      |
| `image.pullPolicy`                 | Image pull policy                                            | `IfNotPresent`                |
| `service.type`                     | Kubernetes service type                                      | `ClusterIP`                   |
| `service.port`                     | Service port for RocketAdmin                                 | `80`                          |
| `ingress.enabled`                  | Enable ingress controller resource                           | `false`                       |
| `ingress.annotations`              | Ingress annotations                                          | `{}`                          |
| `ingress.hosts`                    | Hostnames for the ingress resource                           | `[rocketadmin.local]`         |
| `ingress.tls`                      | TLS configuration for the ingress resource                   | `[]`                          |
| `resources`                        | CPU/Memory resource requests/limits                          | `{}`                          |
| `nodeSelector`                     | Node labels for pod assignment                               | `{}`                          |
| `tolerations`                      | Tolerations for pod assignment                               | `[]`                          |
| `affinity`                         | Map of node/pod affinities                                   | `{}`                          |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example:

```bash
helm install rocketadmin ./rocketadmin --set replicaCount=2
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example:

```bash
helm install rocketadmin -f values.yaml ./rocketadmin
```

## Persistence

The RocketAdmin chart does not include persistence by default. To enable persistent storage, you need to define the appropriate `PersistentVolume` and `PersistentVolumeClaim` resources in your cluster and then configure the chart to use those.

## Using Ingress

To use the ingress resource, set `ingress.enabled` to `true`, and configure the `ingress.hosts` parameter. An example values file might look like this:

```yaml
ingress:
  enabled: true
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  hosts:
    - host: rocketadmin.example.com
      paths: ["/"]
  tls:
    - secretName: rocketadmin-tls
      hosts:
        - rocketadmin.example.com
```

## Upgrading

To upgrade the RocketAdmin chart, use the following command:

```bash
helm upgrade my-release ./rocketadmin
```

## Support

If you encounter any issues or have questions, feel free to reach out through our [GitHub repository](https://github.com/rocketadmin/rocketadmin) or contact our support team.

## License

This Helm chart is released under the [MIT License](LICENSE.md).

---

This README provides an overview and detailed instructions for deploying RocketAdmin using Helm. Adjust the parameters and configurations as needed for your specific use case.
