# Helm Chart for DeepWiki-Open

This Helm chart is designed to deploy the DeepWiki-Open project within a Kubernetes cluster. It provides deployment templates, ingress configuration, and service definitions tailored for the DeepWiki-Open application, with comprehensive customization options via `values.yaml`.

## Key Features

- Deploy a multi-container application with persistent storage
- Configure separate ingress rules for frontend and backend API endpoints
- Customize environment variables, resource limits, and scaling options
- Health checks with configurable liveness and readiness probes
- Support for NodePort and ClusterIP service types
- Optional TLS/SSL configuration for secure connections
- Flexible storage configuration with automatic PVC creation
- Use pre-configured images from GitHub Container Registry

## Installation

### Prerequisites

- Kubernetes cluster (1.19+)
- Helm 3.x
- kubectl configured to communicate with your cluster
- NGINX Ingress Controller (if using ingress)

### Add Helm Repository

Add this chart repository to your Helm client:

```bash
helm repo add deepwiki https://neverlless.github.io/deepwiki-open-helmchart/
helm repo update
```

Verify the chart is available:

```bash
helm search repo deepwiki
```

### Quick Start

1. **Add the Helm repository:**

```bash
helm repo add deepwiki https://neverlless.github.io/deepwiki-open-helmchart/
helm repo update
```

1. **Install the chart with default values:**

```bash
helm install deepwiki deepwiki/deepwiki --namespace deepwiki --create-namespace
```

1. **Install with custom values:**

```bash
helm install deepwiki deepwiki/deepwiki \
  --set deepwiki.namespace=my-namespace \
  --set deepwiki.replicas=2 \
  --set deepwiki.ingress.frontend.host=deepwiki.yourdomain.com \
  --set deepwiki.ingress.backend.host=deepwiki-api.yourdomain.com
```

1. **Install using a custom values file:**

```bash
helm install deepwiki deepwiki/deepwiki -f my-values.yaml --namespace deepwiki
```

### Alternative: Clone and Install from Repository

If you prefer to install directly from the repository:

1. **Clone the repository:**

```bash
git clone https://github.com/neverlless/deepwiki-open-helmchart.git
cd deepwiki-open-helmchart
```

1. **Install from local chart:**

```bash
helm install deepwiki . --namespace deepwiki --create-namespace
```

### Upgrade an Existing Release

```bash
helm upgrade deepwiki . --namespace deepwiki
```

### Uninstall

```bash
helm uninstall deepwiki --namespace deepwiki
```

## Configuration

The following parameters can be customized in `values.yaml`:

### Core Settings

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.name` | Application name used for resource naming | `deepwiki` |
| `deepwiki.namespace` | Kubernetes namespace for deployment | `deepwiki` |
| `deepwiki.createNamespace` | Create namespace if it doesn't exist | `false` |
| `deepwiki.replicas` | Number of pod replicas | `1` |

### Image Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.image.repository` | Container image repository | `ghcr.io/asyncfuncai/deepwiki-open` |
| `deepwiki.image.tag` | Container image tag | `sha-bb3c67d` |
| `deepwiki.image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `deepwiki.imagePullSecrets` | Image pull secrets for private registries | `[]` |

### Ports

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.ports.web` | Frontend NextJS app port | `3000` |
| `deepwiki.ports.api` | Backend API port | `8001` |

### Environment Variables

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.env` | Array of environment variables | `[{"name": "OLLAMA_HOST", "value": "http://your-ollama-server:11434"}]` |
| `deepwiki.envFrom.configMapRef` | Load env variables from ConfigMap | `nil` |
| `deepwiki.envFrom.secretRef` | Load env variables from Secret | `nil` |

Example environment configuration:

```yaml
env:
  - name: OLLAMA_HOST
    value: "http://ollama.example.com:11434"
  - name: DEBUG
    value: "true"
```

### Storage

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.storage.enabled` | Enable persistent storage | `true` |
| `deepwiki.storage.create` | Create PVC automatically | `true` |
| `deepwiki.storage.name` | PVC name | `deepwiki-pvc` |
| `deepwiki.storage.size` | Storage size | `10Gi` |
| `deepwiki.storage.accessModes` | Access modes | `["ReadWriteOnce"]` |
| `deepwiki.storage.mountPath` | Mount path in container | `/root/.adalflow` |
| `deepwiki.storage.storageClassName` | Storage class name | `nil` (uses default) |

### Resources

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.resources.limits.cpu` | CPU limit | `1000m` |
| `deepwiki.resources.limits.memory` | Memory limit | `4Gi` |
| `deepwiki.resources.requests.cpu` | CPU request | `500m` |
| `deepwiki.resources.requests.memory` | Memory request | `1Gi` |

### Health Checks

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.livenessProbe` | Liveness probe configuration | HTTP check on `/` path |
| `deepwiki.readinessProbe` | Readiness probe configuration | HTTP check on `/` path |

### Service

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.service.type` | Service type (ClusterIP or NodePort) | `ClusterIP` |
| `deepwiki.service.nodePortWeb` | NodePort for web (if NodePort type) | `nil` |
| `deepwiki.service.nodePortApi` | NodePort for API (if NodePort type) | `nil` |
| `deepwiki.service.sessionAffinity` | Session affinity | `nil` |

### Ingress

#### Frontend Ingress

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.ingress.className` | Ingress class name | `nginx` |
| `deepwiki.ingress.frontend.enabled` | Enable frontend ingress | `true` |
| `deepwiki.ingress.frontend.host` | Frontend domain | `deepwiki.example.com` |
| `deepwiki.ingress.frontend.path` | Frontend path | `/` |
| `deepwiki.ingress.frontend.pathType` | Path type | `Prefix` |
| `deepwiki.ingress.frontend.tls` | TLS configuration | `nil` |
| `deepwiki.ingress.frontend.annotations` | Custom annotations | `{}` |

#### Backend API Ingress

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.ingress.backend.enabled` | Enable backend API ingress | `true` |
| `deepwiki.ingress.backend.host` | Backend API domain | `deepwiki-api.example.com` |
| `deepwiki.ingress.backend.path` | Backend API path with regex | `/api/v1(/\|$)(.*)` |
| `deepwiki.ingress.backend.pathType` | Path type | `ImplementationSpecific` |
| `deepwiki.ingress.backend.tls` | TLS configuration | `nil` |
| `deepwiki.ingress.backend.annotations` | Custom annotations | URL rewrite configuration |

### Deployment Strategy

| Parameter | Description | Default |
|-----------|-------------|---------|
| `deepwiki.strategy.type` | Deployment strategy type | `Recreate` |

Alternative rolling update example:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 0
```

### Advanced Configuration

Additional customization options:

- `deepwiki.labels` - Common labels for all resources
- `deepwiki.podLabels` - Pod-specific labels
- `deepwiki.podAnnotations` - Pod annotations (e.g., for Prometheus)
- `deepwiki.nodeSelector` - Node selection constraints
- `deepwiki.affinity` - Pod affinity/anti-affinity rules
- `deepwiki.tolerations` - Tolerations for node taints
- `deepwiki.securityContext` - Pod security context
- `deepwiki.containerSecurityContext` - Container security context

## Template Structure

### Deployments

The `templates/deployments.yaml` file defines:

- A Deployment for the main DeepWiki application
- Container configuration with dual ports (web: 3000, API: 8001)
- Persistent volume mounting for `/root/.adalflow`
- Configurable environment variables from `values.yaml`
- Health checks (liveness and readiness probes)
- Resource limits and requests
- Security contexts and image pull secrets

### Services

The `templates/services.yaml` file defines:

- A unified service exposing both web and API endpoints
- Support for ClusterIP and NodePort service types
- Optional session affinity configuration
- Custom service annotations

### Ingress

The `templates/ingress.yaml` file configures:

- **Frontend Ingress**: Routes traffic to the web interface (port 3000)
- **Backend API Ingress**: Routes API traffic with URL rewriting (port 8001)
- Separate ingress resources for frontend and backend
- Optional TLS/SSL termination
- Custom annotations for NGINX ingress controller
- Regex-based path matching for API endpoints

### Namespace

The `templates/namespace.yaml` file creates:

- Kubernetes namespace with configurable name
- Optional namespace annotations (e.g., for ArgoCD sync waves)
- Conditional creation based on `createNamespace` flag

### Persistent Volume Claim

The `templates/pvc.yaml` file creates:

- PersistentVolumeClaim for application data
- Configurable storage size and access modes
- Optional storage class specification
- Custom annotations support

## Examples

### Example 1: Basic Production Deployment

```yaml
# production-values.yaml
deepwiki:
  namespace: production
  replicas: 3
  
  image:
    tag: sha-bb3c67d
    pullPolicy: Always
  
  env:
    - name: OLLAMA_HOST
      value: "http://ollama.production.svc.cluster.local:11434"
    - name: NODE_ENV
      value: "production"
  
  storage:
    size: 50Gi
    storageClassName: fast-ssd
  
  resources:
    limits:
      cpu: 2000m
      memory: 8Gi
    requests:
      cpu: 1000m
      memory: 4Gi
  
  ingress:
    frontend:
      host: deepwiki.company.com
      tls:
        - secretName: deepwiki-tls
          hosts:
            - deepwiki.company.com
    backend:
      host: api.deepwiki.company.com
      tls:
        - secretName: deepwiki-api-tls
          hosts:
            - api.deepwiki.company.com
```

Install:

```bash
helm install deepwiki . -f production-values.yaml --namespace production --create-namespace
```

### Example 2: Development Environment

```yaml
# dev-values.yaml
deepwiki:
  namespace: dev
  replicas: 1
  
  env:
    - name: OLLAMA_HOST
      value: "http://localhost:11434"
    - name: DEBUG
      value: "true"
  
  service:
    type: NodePort
    nodePortWeb: 30000
    nodePortApi: 30001
  
  ingress:
    frontend:
      enabled: false
    backend:
      enabled: false
  
  resources:
    limits:
      cpu: 500m
      memory: 2Gi
    requests:
      cpu: 250m
      memory: 1Gi
```

### Example 3: Using Secrets for Environment Variables

1. Create a Kubernetes Secret:

```bash
kubectl create secret generic deepwiki-secrets \
  --from-literal=OLLAMA_HOST=http://ollama:11434 \
  --from-literal=API_KEY=your-secret-key \
  --namespace deepwiki
```

2. Configure `values.yaml`:

```yaml
deepwiki:
  envFrom:
    secretRef: deepwiki-secrets
```

## Dependencies

This chart has no external Helm chart dependencies. However, the following prerequisites must be in place:

1. **Kubernetes Cluster**: Version 1.19 or higher
2. **NGINX Ingress Controller**: Required if ingress is enabled
3. **Storage Provider**: For persistent volume provisioning (if `storage.enabled=true`)
4. **DNS Configuration**: Valid DNS entries for configured ingress domains
5. **Ollama Server**: External Ollama server for AI model inference

## Troubleshooting

### Check deployment status:

```bash
kubectl get pods -n deepwiki
kubectl describe pod <pod-name> -n deepwiki
```

### View logs:

```bash
kubectl logs -n deepwiki -l app.kubernetes.io/name=deepwiki --tail=100 -f
```

### Check ingress:

```bash
kubectl get ingress -n deepwiki
kubectl describe ingress -n deepwiki
```

### Verify service endpoints:

```bash
kubectl get endpoints -n deepwiki
```

### Common Issues

**Pod not starting:**
- Check if PVC is bound: `kubectl get pvc -n deepwiki`
- Verify image pull: `kubectl describe pod <pod-name> -n deepwiki`
- Check resource availability on nodes

**Ingress not working:**
- Ensure NGINX Ingress Controller is installed
- Verify DNS resolution
- Check ingress annotations and TLS configuration

**Storage issues:**
- Verify StorageClass exists: `kubectl get storageclass`
- Check PVC status: `kubectl get pvc -n deepwiki`
- Review PV provisioner logs

## Testing

To verify dependencies and configuration:

```bash
# Dry run to check generated templates
helm install deepwiki . --dry-run --debug

# Template validation
helm template deepwiki . > output.yaml
kubectl apply --dry-run=client -f output.yaml

# Dependency update (if adding dependencies in future)
helm dependency update
```

## Upgrading

When upgrading to a new version:

```bash
# Update with new values
helm upgrade deepwiki . --namespace deepwiki --reuse-values

# Or with new values file
helm upgrade deepwiki . -f new-values.yaml --namespace deepwiki

# View upgrade history
helm history deepwiki --namespace deepwiki

# Rollback if needed
helm rollback deepwiki <revision> --namespace deepwiki
```

## Contributing

We welcome improvements! Please:

- Add new configuration options with proper documentation
- Update this README with any new features
- Test changes thoroughly before submitting PRs
- Follow Kubernetes and Helm best practices
- Add examples for complex configurations

### Development Workflow

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with `helm lint` and `helm template`
5. Submit a pull request

## Support

For help and questions:

- Check the [Helm documentation](https://helm.sh/docs/)
- Review the [DeepWiki-Open project](https://github.com/AsyncFuncAI/deepwiki-open)
- Open an issue in this repository

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- DeepWiki-Open team for the application
- Kubernetes and Helm communities for excellent tools and documentation
