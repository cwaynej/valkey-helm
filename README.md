# valkey Helm Chart

A lightweight Helm chart for deploying Valkey (a Redis alternative) to Kubernetes.

---

## TL;DR

```bash
helm repo add valkey https://valkey.io/valkey-helm/
helm install valkey valkey/valkey
```

---

## Introduction

This chart bootstraps a [Valkey](https://valkey.io) deployment using the Helm package manager. It supports three deployment modes:

- **standalone** - a single Valkey instance (Deployment)
- **replication** - static master + replicas (StatefulSet), pod-0 is always master
- **sentinel** - master + replicas with automatic failover via Sentinel (StatefulSet)

---

## Prerequisites

* Kubernetes 1.20+
* Helm 3.5+

---

## Installing the Chart

### Standalone (default)

```bash
helm install valkey valkey/valkey
```

### Replication

```bash
helm install valkey valkey/valkey \
  --set mode=replication \
  --set replicas=3 \
  --set persistence.size=5Gi
```

### Sentinel (automatic failover)

```bash
helm install valkey valkey/valkey \
  --set mode=sentinel \
  --set replicas=3 \
  --set persistence.size=5Gi
```

---

## Configuration

### Deployment mode

| Parameter | Description | Default |
|-----------|-------------|---------|
| `mode` | Deployment mode: `standalone`, `replication`, or `sentinel` | `standalone` |
| `replicas` | Total number of pods (replication/sentinel only) | `3` |

### Services

All services are grouped under `services`:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `services.write.type` | Write service type | `ClusterIP` |
| `services.write.port` | Write service port | `6379` |
| `services.read.enabled` | Create a read-replica service | `true` |
| `services.read.type` | Read service type | `ClusterIP` |
| `services.read.port` | Read service port | `6379` |
| `services.sentinel.enabled` | Create a sentinel service | `true` |
| `services.sentinel.type` | Sentinel service type | `ClusterIP` |
| `services.sentinel.port` | Sentinel service port | `26379` |

### Persistence

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.enabled` | Enable persistent storage (standalone) | `false` |
| `persistence.size` | PVC size (required for replication/sentinel) | `""` |
| `persistence.storageClass` | Storage class name | `""` |
| `persistence.accessModes` | PVC access modes | `[ReadWriteOnce]` |
| `persistence.existingClaim` | Existing PVC name (standalone only) | `""` |

### Authentication

| Parameter | Description | Default |
|-----------|-------------|---------|
| `auth.enabled` | Enable ACL-based authentication | `false` |
| `auth.aclUsers` | Map of users with ACL permissions | `{}` |
| `auth.aclConfig` | Inline ACL configuration | `""` |
| `auth.usersExistingSecret` | Existing secret for user passwords | `""` |

### Replication

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replication.replicationUser` | User for replica authentication | `default` |
| `replication.disklessSync` | Use diskless replication | `false` |
| `replication.minReplicasToWrite` | Minimum healthy replicas for writes | `0` |
| `replication.minReplicasMaxLag` | Max replication lag in seconds | `10` |

### Sentinel

| Parameter | Description | Default |
|-----------|-------------|---------|
| `sentinel.port` | Sentinel port | `26379` |
| `sentinel.masterName` | Sentinel master set name | `mymaster` |
| `sentinel.quorum` | Quorum for failover | `2` |
| `sentinel.downAfterMilliseconds` | Master down detection timeout | `5000` |
| `sentinel.failoverTimeout` | Failover timeout | `60000` |
| `sentinel.reconcileInterval` | Label reconciler check interval (seconds) | `5` |

### TLS

| Parameter | Description | Default |
|-----------|-------------|---------|
| `tls.enabled` | Enable TLS | `false` |
| `tls.existingSecret` | Secret containing TLS certificates | `""` |

### Metrics

| Parameter | Description | Default |
|-----------|-------------|---------|
| `metrics.enabled` | Enable Prometheus exporter sidecar | `false` |
| `metrics.serviceMonitor.enabled` | Create ServiceMonitor resource | `false` |
| `metrics.podMonitor.enabled` | Create PodMonitor resource | `false` |

For the complete list of configurable parameters, see [values.yaml](valkey/values.yaml).

---

## Maintainers

| Name     | Email         |
| -------- | ------------- |
| mk-raven | maikebit at gmail.com |
| sgissi   | silvio at gissilabs.com |

---

## Slack

You can also provide feedback or join the conversation with other developers, users, and contributors on the #[Valkey-helm](https://valkey-oss-developer.slack.com/archives/C09JZ6N2AAV) Slack channel.

---

## License

BSD 3-Clause License
