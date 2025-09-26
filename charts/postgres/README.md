<p align="center">
    <a href="https://artifacthub.io/packages/search?repo=cloudpirates-postgres"><img src="https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/cloudpirates-postgres" /></a>
</p>

# PostgreSQL

A Helm chart for PostgreSQL - The World's Most Advanced Open Source Relational Database. PostgreSQL is a powerful, open source object-relational database system with over 35 years of active development that has earned it a strong reputation for reliability, feature robustness, and performance.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)

## Installing the Chart

To install the chart with the release name `my-postgres`:

```bash
helm install my-postgres oci://registry-1.docker.io/cloudpirates/postgres
```

Or install directly from the local chart:

```bash
helm install my-postgres ./charts/postgres
```

The command deploys PostgreSQL on the Kubernetes cluster in the default configuration. The [Configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-postgres` deployment:

```bash
helm uninstall my-postgres
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Security & Signature Verification

This Helm chart is cryptographically signed with Cosign to ensure authenticity and prevent tampering.

**Public Key:**

```
-----BEGIN PUBLIC KEY-----
MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE7BgqFgKdPtHdXz6OfYBklYwJgGWQ
mZzYz8qJ9r6QhF3NxK8rD2oG7Bk6nHJz7qWXhQoU2JvJdI3Zx9HGpLfKvw==
-----END PUBLIC KEY-----
```

To verify the helm chart before installation, copy the public key to the file `cosign.pub` and run cosign:

```bash
cosign verify --key cosign.pub registry-1.docker.io/cloudpirates/postgres:<version>
```

## Configuration

The following table lists the configurable parameters of the PostgreSQL chart and their default values.

### Global parameters

| Parameter                 | Description                                     | Default |
| ------------------------- | ----------------------------------------------- | ------- |
| `global.imageRegistry`    | Global Docker image registry                    | `""`    |
| `global.imagePullSecrets` | Global Docker registry secret names as an array | `[]`    |

### PostgreSQL image configuration

| Parameter               | Description                                           | Default                                                                          |
| ----------------------- | ----------------------------------------------------- | -------------------------------------------------------------------------------- |
| `image.registry`        | PostgreSQL image registry                             | `docker.io`                                                                      |
| `image.repository`      | PostgreSQL image repository                           | `postgres`                                                                       |
| `image.tag`             | PostgreSQL image tag (immutable tags are recommended) | `"17.6@sha256:feff5b24fedd610975a1f5e743c51a4b360437f4dc3a11acf740dcd708f413f6"` |
| `image.imagePullPolicy` | PostgreSQL image pull policy                          | `Always`                                                                         |

### Deployment configuration

| Parameter           | Description                                                                                                    | Default |
| ------------------- | -------------------------------------------------------------------------------------------------------------- | ------- |
| `replicaCount`      | Number of PostgreSQL replicas to deploy (Note: PostgreSQL doesn't support multi-master replication by default) | `1`     |
| `nameOverride`      | String to partially override postgres.fullname                                                                 | `""`    |
| `fullnameOverride`  | String to fully override postgres.fullname                                                                     | `""`    |
| `commonLabels`      | Labels to add to all deployed objects                                                                          | `{}`    |
| `commonAnnotations` | Annotations to add to all deployed objects                                                                     | `{}`    |

### Pod annotations and labels

| Parameter        | Description                           | Default |
| ---------------- | ------------------------------------- | ------- |
| `podAnnotations` | Map of annotations to add to the pods | `{}`    |
| `podLabels`      | Map of labels to add to the pods      | `{}`    |

### Security Context

| Parameter                                  | Description                                       | Default   |
| ------------------------------------------ | ------------------------------------------------- | --------- |
| `podSecurityContext.fsGroup`               | Group ID for the volumes of the pod               | `999`     |
| `securityContext.allowPrivilegeEscalation` | Enable container privilege escalation             | `false`   |
| `securityContext.runAsNonRoot`             | Configure the container to run as a non-root user | `true`    |
| `securityContext.runAsUser`                | User ID for the PostgreSQL container              | `999`     |
| `securityContext.runAsGroup`               | Group ID for the PostgreSQL container             | `999`     |
| `securityContext.readOnlyRootFilesystem`   | Mount container root filesystem as read-only      | `false`   |
| `securityContext.capabilities.drop`        | Linux capabilities to be dropped                  | `["ALL"]` |

### PostgreSQL Authentication

| Parameter                     | Description                                                                                                    | Default               |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------- | --------------------- |
| `auth.username`               | Name for a custom superuser to create at initialisation. (This will also create a database with the same name) | `"openfga"`           |
| `auth.password`               | Password for the custom user to create                                                                         | `""`                  |
| `auth.database`               | Alternative name for the default database to be created at initialisation                                      | `""`                  |
| `auth.existingSecret`         | Name of existing secret to use for PostgreSQL credentials                                                      | `""`                  |
| `auth.secretKeys.passwordKey` | Name of key in existing secret to use for PostgreSQL credentials                                               | `"postgres-password"` |

### PostgreSQL Configuration

| Parameter                                     | Description                                                                             | Default |
| --------------------------------------------- | --------------------------------------------------------------------------------------- | ------- |
| `config.postgresqlSharedPreloadLibraries`     | Shared preload libraries (comma-separated list)                                         | `""`    |
| `config.postgresqlMaxConnections`             | Maximum number of connections                                                           | `100`   |
| `config.postgresqlSharedBuffers`              | Amount of memory the database server uses for shared memory buffers                     | `""`    |
| `config.postgresqlEffectiveCacheSize`         | Effective cache size                                                                    | `""`    |
| `config.postgresqlWorkMem`                    | Amount of memory to be used by internal sort operations and hash tables                 | `""`    |
| `config.postgresqlMaintenanceWorkMem`         | Maximum amount of memory to be used by maintenance operations                           | `""`    |
| `config.postgresqlWalBuffers`                 | Amount of memory used in shared memory for WAL data                                     | `""`    |
| `config.postgresqlCheckpointCompletionTarget` | Time spent flushing dirty buffers during checkpoint, as fraction of checkpoint interval | `""`    |
| `config.postgresqlRandomPageCost`             | Sets the planner's estimate of the cost of a non-sequentially-fetched disk page         | `""`    |
| `config.postgresqlLogStatement`               | Sets the type of statements logged                                                      | `""`    |
| `config.postgresqlLogMinDurationStatement`    | Sets the minimum execution time above which statements will be logged                   | `""`    |
| `config.extraConfig`                          | Additional PostgreSQL configuration parameters                                          | `[]`    |
| `config.existingConfigmap`                    | Name of existing ConfigMap with PostgreSQL configuration                                | `""`    |
| `config.pgHbaConfig`                          | Content of a custom pg_hba.conf file to be used instead of the default config           | `""`    |

### Custom User Configuration
| Parameter                   | Description                                                                        | Default                                  |
| --------------------------- | ---------------------------------------------------------------------------------- | ---------------------------------------- |
| `customUser`                | Optional user to be created at initialisation with a custom password and database  | `{}`                                     |
| `customUser.name`           | Name of the custom user to be created                                              | `""`                                     |
| `customUser.database`       | Name of the database to be created                                                 | `""`                                     |
| `customUser.password`       | Password to be used for the custom user                                            | `""`                                     |
| `customUser.existingSecret` | Existing secret, in which username, password and database name are saved           | `""`                                     |
| `customUser.secretKeys`     | Name of keys in existing secret to use the custom user name, password and database | `{name: "", database: "", password: ""}` |

### PostgreSQL Initdb Configuration

| Parameter                 | Description                                                                      | Default |
| ------------------------- | -------------------------------------------------------------------------------- | ------- |
| `initdb.args`             | Send arguments to postgres initdb. This is a space separated string of arguments | `""`    |
| `initdb.scripts`          | Dictionary of initdb scripts                                                     | `{}`    |
| `initdb.scriptsConfigMap` | ConfigMap with scripts to be run at first boot                                   | `""`    |

### Service configuration

| Parameter             | Description               | Default     |
| --------------------- | ------------------------- | ----------- |
| `service.type`        | PostgreSQL service type   | `ClusterIP` |
| `service.port`        | PostgreSQL service port   | `5432`      |
| `service.targetPort`  | PostgreSQL container port | `5432`      |
| `service.annotations` | Service annotations       | `{}`        |

### Ingress configuration

| Parameter                            | Description                                             | Default          |
| ------------------------------------ | ------------------------------------------------------- | ---------------- |
| `ingress.enabled`                    | Enable ingress record generation for PostgreSQL         | `false`          |
| `ingress.className`                  | IngressClass that will be used to implement the Ingress | `""`             |
| `ingress.annotations`                | Additional annotations for the Ingress resource         | `{}`             |
| `ingress.hosts[0].host`              | Hostname for PostgreSQL ingress                         | `postgres.local` |
| `ingress.hosts[0].paths[0].path`     | Path for PostgreSQL ingress                             | `/`              |
| `ingress.hosts[0].paths[0].pathType` | Path type for PostgreSQL ingress                        | `Prefix`         |
| `ingress.tls`                        | TLS configuration for PostgreSQL ingress                | `[]`             |

### Resources

| Parameter   | Description                                 | Default |
| ----------- | ------------------------------------------- | ------- |
| `resources` | The resources to allocate for the container | `{}`    |

### Persistence

| Parameter                   | Description                                        | Default             |
| --------------------------- | -------------------------------------------------- | ------------------- |
| `persistence.enabled`       | Enable persistence using Persistent Volume Claims  | `true`              |
| `persistence.storageClass`  | Persistent Volume storage class                    | `""`                |
| `persistence.annotations`   | Persistent Volume Claim annotations                | `{}`                |
| `persistence.size`          | Persistent Volume size                             | `8Gi`               |
| `persistence.accessModes`   | Persistent Volume access modes                     | `["ReadWriteOnce"]` |
| `persistence.existingClaim` | The name of an existing PVC to use for persistence | `""`                |

### Persistent Volume Claim Retention Policy

| Parameter                                          | Description                                                                    | Default    |
| -------------------------------------------------- | ------------------------------------------------------------------------------ | ---------- |
| `persistentVolumeClaimRetentionPolicy.enabled`     | Enable Persistent volume retention policy for the Statefulset                  | `false`    |
| `persistentVolumeClaimRetentionPolicy.whenDeleted` | Volume retention behavior that applies when the StatefulSet is deleted         | `"Retain"` |
| `persistentVolumeClaimRetentionPolicy.whenScaled`  | Volume retention behavior when the replica count of the StatefulSet is reduced | `"Retain"` |

### Liveness and readiness probes

| Parameter                            | Description                                    | Default |
| ------------------------------------ | ---------------------------------------------- | ------- |
| `livenessProbe.enabled`              | Enable livenessProbe on PostgreSQL containers  | `true`  |
| `livenessProbe.initialDelaySeconds`  | Initial delay seconds for livenessProbe        | `30`    |
| `livenessProbe.periodSeconds`        | Period seconds for livenessProbe               | `10`    |
| `livenessProbe.timeoutSeconds`       | Timeout seconds for livenessProbe              | `5`     |
| `livenessProbe.failureThreshold`     | Failure threshold for livenessProbe            | `3`     |
| `livenessProbe.successThreshold`     | Success threshold for livenessProbe            | `1`     |
| `readinessProbe.enabled`             | Enable readinessProbe on PostgreSQL containers | `true`  |
| `readinessProbe.initialDelaySeconds` | Initial delay seconds for readinessProbe       | `5`     |
| `readinessProbe.periodSeconds`       | Period seconds for readinessProbe              | `5`     |
| `readinessProbe.timeoutSeconds`      | Timeout seconds for readinessProbe             | `5`     |
| `readinessProbe.failureThreshold`    | Failure threshold for readinessProbe           | `3`     |
| `readinessProbe.successThreshold`    | Success threshold for readinessProbe           | `1`     |
| `startupProbe.enabled`               | Enable startupProbe on PostgreSQL containers   | `true`  |
| `startupProbe.initialDelaySeconds`   | Initial delay seconds for startupProbe         | `30`    |
| `startupProbe.periodSeconds`         | Period seconds for startupProbe                | `10`    |
| `startupProbe.timeoutSeconds`        | Timeout seconds for startupProbe               | `5`     |
| `startupProbe.failureThreshold`      | Failure threshold for startupProbe             | `30`    |
| `startupProbe.successThreshold`      | Success threshold for startupProbe             | `1`     |

### Node Selection

| Parameter      | Description                          | Default |
| -------------- | ------------------------------------ | ------- |
| `nodeSelector` | Node labels for pod assignment       | `{}`    |
| `tolerations`  | Toleration labels for pod assignment | `[]`    |
| `affinity`     | Affinity settings for pod assignment | `{}`    |

### Service Account

| Parameter                                     | Description                                                                                                               | Default |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ------- |
| `serviceAccount.create`                       | Specifies whether a service account should be created                                                                     | `false` |
| `serviceAccount.annotations`                  | Annotations to add to the service account                                                                                 | `{}`    |
| `serviceAccount.name`                         | The name of the service account to use. If not set and create is true, a name is generated using the `fullname` template. | `""`    |
| `serviceAccount.automountServiceAccountToken` | Whether to automount the SA token inside the pod                                                                          | `false` |

### Extra Environment

| Parameter  | Description                                           | Default |
| ---------- | ----------------------------------------------------- | ------- |
| `extraEnv` | Additional environment variables from key-value pairs | `{}`    |

### Extra Configuration Parameters

| Parameter            | Description                                                            | Default |
| -------------------- | ---------------------------------------------------------------------- | ------- |
| `extraObjects`       | Array of extra objects to deploy with the release                      | `[]`    |
| `extraEnvVarsSecret` | Name of an existing Secret containing additional environment variables | ``      |

#### Extra Objects

You can use the `extraObjects` array to deploy additional Kubernetes resources (such as NetworkPolicies, ConfigMaps, etc.) alongside the release. This is useful for customizing your deployment with extra manifests that are not covered by the default chart options.

**Helm templating is supported in any field, but all template expressions must be quoted.** For example, to use the release namespace, write `namespace: "{{ .Release.Namespace }}"`.

**Example: Deploy a NetworkPolicy with templating**

```yaml
extraObjects:
  - apiVersion: networking.k8s.io/v1
    kind: NetworkPolicy
    metadata:
      name: allow-dns
      namespace: "{{ .Release.Namespace }}"
    spec:
      podSelector: {}
      policyTypes:
        - Egress
      egress:
        - to:
            - namespaceSelector:
                matchLabels:
                  kubernetes.io/metadata.name: kube-system
              podSelector:
                matchLabels:
                  k8s-app: kube-dns
        - ports:
            - port: 53
              protocol: UDP
            - port: 53
              protocol: TCP
```

All objects in `extraObjects` will be rendered and deployed with the release. You can use any valid Kubernetes manifest, and reference Helm values or built-in objects as needed (just remember to quote template expressions).

## Examples

### Basic Deployment

Deploy PostgreSQL with default configuration:

```bash
helm install my-postgres ./charts/postgres
```

### Production Setup with Persistence and custom user

```yaml
# values-production.yaml
persistence:
  enabled: true
  storageClass: "fast-ssd"
  size: 100Gi

resources:
  requests:
    memory: "1Gi"
    cpu: "500m"
  limits:
    memory: "2Gi"
    cpu: "1000m"

auth:
  username: "myapp"
  password: "your-secure-app-password"

config:
  postgresqlMaxConnections: 200
  postgresqlSharedBuffers: "256MB"
  postgresqlEffectiveCacheSize: "1GB"
  postgresqlWorkMem: "8MB"
  postgresqlMaintenanceWorkMem: "128MB"

customUser:
  existingSecret: "postgres-custom-user"
  secretKeys:
    name: "username"
    password: "password"
    database: "mydatabase"

ingress:
  enabled: true
  className: "nginx"
  hosts:
    - host: postgres.yourdomain.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: postgres-tls
      hosts:
        - postgres.yourdomain.com
```

Deploy with production values:

```bash
helm install my-postgres ./charts/postgres -f values-production.yaml
```

### High Performance Configuration

```yaml
# values-performance.yaml
resources:
  requests:
    memory: "4Gi"
    cpu: "2000m"
  limits:
    memory: "8Gi"
    cpu: "4000m"

config:
  postgresqlMaxConnections: 500
  postgresqlSharedBuffers: "2GB"
  postgresqlEffectiveCacheSize: "6GB"
  postgresqlWorkMem: "16MB"
  postgresqlMaintenanceWorkMem: "512MB"
  postgresqlWalBuffers: "32MB"
  postgresqlCheckpointCompletionTarget: "0.9"
  postgresqlRandomPageCost: "1.0"
  extraConfig:
    - "wal_level = replica"
    - "max_wal_senders = 3"
    - "archive_mode = on"
    - "archive_command = 'test ! -f /backup/%f && cp %p /backup/%f'"

affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
              - key: app.kubernetes.io/name
                operator: In
                values:
                  - postgres
          topologyKey: kubernetes.io/hostname
```

### Using Existing Secret for Authentication

```yaml
# values-external-secret.yaml
auth:
  existingSecret: "postgres-credentials"
  secretKeys:
    adminPasswordKey: "postgres-password"
    userPasswordKey: "app-password"
```

Create the secret first:

```bash
kubectl create secret generic postgres-credentials \
  --from-literal=postgres-password=your-admin-password \
  --from-literal=app-password=your-app-password
```

### Custom Configuration with ConfigMap

```yaml
# values-custom-config.yaml
config:
  existingConfigmap: "postgres-custom-config"
```

Create the ConfigMap first:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: postgres-custom-config
data:
  postgresql.conf: |
    # Custom PostgreSQL configuration
    max_connections = 300
    shared_buffers = 512MB
    effective_cache_size = 2GB
    work_mem = 16MB
    maintenance_work_mem = 256MB
    # Add your custom configuration here
```

## Access PostgreSQL

### Via kubectl port-forward

```bash
kubectl port-forward service/my-postgres 5432:5432
```

### Connect using psql

```bash
# Connect as postgres user
PGPASSWORD=your-password psql -h localhost -U postgres -d postgres

# Connect as custom user
PGPASSWORD=your-password psql -h localhost -U myapp -d myappdb
```

### Default Credentials

- **Admin User**: `postgres` (if enabled)
- **Admin Password**: Auto-generated (check secret) or configured value
- **Custom User**: Configured username
- **Custom Password**: Auto-generated or configured value

Get the auto-generated passwords:

```bash
# Admin password
kubectl get secret my-postgres -o jsonpath="{.data.postgres-password}" | base64 --decode

# Custom user password
kubectl get secret my-postgres -o jsonpath="{.data.password}" | base64 --decode
```

## Troubleshooting

### Common Issues

1. **Pod fails to start with permission errors**

   - Ensure your storage class supports the required access modes
   - Check if security contexts are compatible with your cluster policies
   - Verify the PostgreSQL data directory permissions

2. **Cannot connect to PostgreSQL**

   - Verify the service is running: `kubectl get svc`
   - Check if authentication is properly configured
   - Ensure firewall rules allow access to port 5432
   - Check PostgreSQL logs: `kubectl logs <pod-name>`

3. **Database initialization fails**

   - Check if persistent volume has enough space
   - Verify environment variables are set correctly
   - Review pod events: `kubectl describe pod <pod-name>`

4. **Performance issues**
   - Check configured memory settings
   - Monitor resource usage with `kubectl top pod`
   - Adjust PostgreSQL configuration parameters
   - Consider increasing resources

### Performance Tuning

1. **Memory Configuration**

   ```yaml
   config:
     postgresqlSharedBuffers: "256MB" # 25% of RAM
     postgresqlEffectiveCacheSize: "1GB" # 75% of RAM
     postgresqlWorkMem: "8MB" # RAM / max_connections
     postgresqlMaintenanceWorkMem: "128MB"
   ```

2. **Connection Settings**

   ```yaml
   config:
     postgresqlMaxConnections: 200
   ```

3. **WAL and Checkpoints**

   ```yaml
   config:
     postgresqlWalBuffers: "16MB"
     postgresqlCheckpointCompletionTarget: "0.7"
     extraConfig:
       - "wal_level = replica"
       - "max_wal_size = 2GB"
       - "min_wal_size = 1GB"
   ```

4. **Resource Limits**
   ```yaml
   resources:
     requests:
       memory: "2Gi"
       cpu: "1000m"
     limits:
       memory: "4Gi"
       cpu: "2000m"
   ```

### Backup and Recovery

**Manual Backup**

```bash
kubectl exec -it <pod-name> -- pg_dump -U postgres -d mydb > backup.sql
```

### Getting Support

For issues related to this Helm chart, please check:

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- Chart repository issues
