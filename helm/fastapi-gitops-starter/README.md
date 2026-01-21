# FastAPI GitOps Starter Helm Chart

This Helm chart deploys the FastAPI GitOps Starter application to Kubernetes with support for standard Deployments and advanced deployment strategies using Argo Rollouts.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- A container image of the application (build using the Dockerfile in the repository)
- (Optional) Argo Rollouts installed for canary and blue-green deployments

## Installing the Chart

Install the chart:

```bash
helm install my-release ./helm/fastapi-gitops-starter
```

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
helm uninstall my-release
```

## Configuration

The following table lists the configurable parameters of the chart and their default values.

### Common Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `2` |
| `image.repository` | Container image repository | `fastapi-gitops-starter` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `image.tag` | Image tag (defaults to chart appVersion) | `""` |
| `imagePullSecrets` | Image pull secrets | `[]` |
| `nameOverride` | String to partially override the fullname | `""` |
| `fullnameOverride` | String to fully override the fullname | `""` |

### Service Account

| Parameter | Description | Default |
|-----------|-------------|---------|
| `serviceAccount.create` | Create service account | `true` |
| `serviceAccount.automount` | Automount service account token | `true` |
| `serviceAccount.annotations` | Service account annotations | `{}` |
| `serviceAccount.name` | Service account name | `""` |

### Pod Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `podAnnotations` | Pod annotations | `{}` |
| `podLabels` | Pod labels | `{}` |
| `podSecurityContext.runAsNonRoot` | Run as non-root user | `true` |
| `podSecurityContext.runAsUser` | User ID | `1000` |
| `podSecurityContext.fsGroup` | Filesystem group | `1000` |

### Security Context

| Parameter | Description | Default |
|-----------|-------------|---------|
| `securityContext.allowPrivilegeEscalation` | Allow privilege escalation | `false` |
| `securityContext.capabilities.drop` | Drop capabilities | `["ALL"]` |
| `securityContext.readOnlyRootFilesystem` | Read-only root filesystem | `false` |

### Service

| Parameter | Description | Default |
|-----------|-------------|---------|
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `8000` |
| `service.annotations` | Service annotations | `{}` |

### Ingress

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable ingress | `true` |
| `ingress.className` | Ingress class name | `"nginx"` |
| `ingress.annotations` | Ingress annotations | `{}` |
| `ingress.hosts` | Ingress hosts configuration | See values.yaml |
| `ingress.tls` | Ingress TLS configuration | `[]` |

### Resources

| Parameter | Description | Default |
|-----------|-------------|---------|
| `resources.limits.cpu` | CPU limit | `500m` |
| `resources.limits.memory` | Memory limit | `512Mi` |
| `resources.requests.cpu` | CPU request | `100m` |
| `resources.requests.memory` | Memory request | `128Mi` |

### Probes

| Parameter | Description | Default |
|-----------|-------------|---------|
| `livenessProbe.httpGet.path` | Liveness probe path | `/GitOps-Starter/health` |
| `livenessProbe.initialDelaySeconds` | Initial delay | `30` |
| `livenessProbe.periodSeconds` | Period | `10` |
| `readinessProbe.httpGet.path` | Readiness probe path | `/GitOps-Starter/health` |
| `readinessProbe.initialDelaySeconds` | Initial delay | `10` |
| `readinessProbe.periodSeconds` | Period | `5` |

### Autoscaling

| Parameter | Description | Default |
|-----------|-------------|---------|
| `autoscaling.enabled` | Enable HPA | `false` |
| `autoscaling.minReplicas` | Minimum replicas | `2` |
| `autoscaling.maxReplicas` | Maximum replicas | `10` |
| `autoscaling.targetCPUUtilizationPercentage` | Target CPU utilization | `80` |
| `autoscaling.targetMemoryUtilizationPercentage` | Target memory utilization | `80` |

### Argo Rollouts

| Parameter | Description | Default |
|-----------|-------------|---------|
| `rollout.enabled` | Enable Argo Rollouts (requires argo-rollouts to be installed) | `false` |
| `rollout.strategy` | Deployment strategy: `canary` or `blueGreen` | `canary` |
| `rollout.canary.steps` | Canary deployment steps (weight and pause configurations) | See values.yaml |
| `rollout.canary.maxSurge` | Maximum surge for canary rollout | `"25%"` |
| `rollout.canary.maxUnavailable` | Maximum unavailable pods during rollout | `0` |
| `rollout.blueGreen.activeService` | Service name for active (production) traffic | `""` (defaults to main service) |
| `rollout.blueGreen.previewService` | Service name for preview (staging) traffic | `""` (defaults to `{{fullname}}-preview`) |
| `rollout.blueGreen.autoPromotionEnabled` | Enable automatic promotion | `true` |
| `rollout.blueGreen.autoPromotionSeconds` | Seconds to wait before auto-promotion | `30` |
| `rollout.blueGreen.scaleDownDelaySeconds` | Seconds to wait before scaling down old version | `30` |
| `rollout.analysis.enabled` | Enable automated analysis during rollouts | `false` |
| `rollout.analysis.trafficGenerator.enabled` | Enable traffic generation job during analysis | `false` |
| `rollout.analysis.trafficGenerator.image` | Container image for traffic generation | `"curlimages/curl:latest"` |
| `rollout.analysis.trafficGenerator.initialDelay` | Initial delay before starting traffic generation | `"5s"` |
| `rollout.analysis.trafficGenerator.interval` | How often to run the traffic generation job | `"10s"` |
| `rollout.analysis.trafficGenerator.count` | Number of times to run the traffic generation job | `1` |
| `rollout.analysis.trafficGenerator.duration` | Duration of traffic generation in seconds | `60` |
| `rollout.analysis.trafficGenerator.requestsPerSecond` | Number of requests per second to generate | `10` |
| `rollout.analysis.trafficGenerator.serviceUrl` | Service URL (defaults to internal cluster service) | `""` |
| `rollout.analysis.trafficGenerator.backoffLimit` | Backoff limit for the job | `1` |
| `rollout.analysis.trafficGenerator.script` | Custom script for traffic generation (overrides default) | `""` |
| `rollout.analysis.prometheus.address` | Prometheus server address for metrics | `""` (required when analysis is enabled) |
| `rollout.analysis.metrics.successRate.enabled` | Enable success rate metric (non-5xx responses) | `true` |
| `rollout.analysis.metrics.successRate.interval` | How often to check the metric | `"1m"` |
| `rollout.analysis.metrics.successRate.count` | Number of measurements to perform | `5` |
| `rollout.analysis.metrics.successRate.successCondition` | Success condition expression | `"result >= 0.95"` |
| `rollout.analysis.metrics.successRate.failureLimit` | Failed measurements before marking as failed | `3` |
| `rollout.analysis.metrics.responseTime.enabled` | Enable response time metric (95th percentile) | `true` |
| `rollout.analysis.metrics.responseTime.successCondition` | Response time success condition | `"result <= 1.0"` (1 second) |
| `rollout.analysis.metrics.errorRate.enabled` | Enable error rate metric (5xx responses) | `true` |
| `rollout.analysis.metrics.errorRate.successCondition` | Error rate success condition | `"result <= 0.05"` (5%) |
| `rollout.analysis.metrics.custom` | Custom metrics configuration | `{}` |
| `rollout.analysis.args` | Arguments to pass to analysis templates | `[]` |

### Application Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `app.rootPath` | FastAPI root path | `/GitOps-Starter` |

### Additional Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `nodeSelector` | Node selector | `{}` |
| `tolerations` | Tolerations | `[]` |
| `affinity` | Affinity rules | `{}` |
| `volumes` | Additional volumes | `[]` |
| `volumeMounts` | Additional volume mounts | `[]` |

## Examples

### Basic Installation

```bash
helm install my-app ./helm/fastapi-gitops-starter
```

### With Custom Values

```bash
helm install my-app ./helm/fastapi-gitops-starter \
  --set replicaCount=3 \
  --set image.repository=myregistry/fastapi-gitops-starter \
  --set image.tag=2.0.0
```

### Enable Ingress

```bash
helm install my-app ./helm/fastapi-gitops-starter \
  --set ingress.enabled=true \
  --set ingress.hosts[0].host=myapp.example.com \
  --set ingress.hosts[0].paths[0].path=/ \
  --set ingress.hosts[0].paths[0].pathType=Prefix
```

### Enable Autoscaling

```bash
helm install my-app ./helm/fastapi-gitops-starter \
  --set autoscaling.enabled=true \
  --set autoscaling.minReplicas=2 \
  --set autoscaling.maxReplicas=5
```

### Using a Values File

Create a `custom-values.yaml` file:

```yaml
replicaCount: 3

image:
  repository: myregistry/fastapi-gitops-starter
  tag: "2.0.0"

ingress:
  enabled: true
  className: nginx
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
  hosts:
    - host: myapp.example.com
      paths:
        - path: /GitOps-Starter
          pathType: Prefix

resources:
  limits:
    cpu: 1000m
    memory: 1Gi
  requests:
    cpu: 250m
    memory: 256Mi

app:
  rootPath: "/GitOps-Starter"
```

Install with:

```bash
helm install my-app ./helm/fastapi-gitops-starter -f custom-values.yaml
```

### Canary Deployment with Argo Rollouts

**Prerequisites:** Install Argo Rollouts in your cluster:

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install argo-rollouts argo/argo-rollouts -f external-services-values/argo-rollouts-values.yaml --namespace argo --create-namespace
```

To install the Kubectl Plugin Installation flow, follow the instructions at: https://argo-rollouts.readthedocs.io/en/stable/installation/#kubectl-plugin-installation

Add the Argo Rollouts Grafana dashboards for monitoring from:

Install with canary deployment strategy:

```bash
helm install my-app ./helm/fastapi-gitops-starter \
  -f ./helm/fastapi-gitops-starter/example-canary-values.yaml
```

Or create a custom canary configuration:

```bash
helm install my-app ./helm/fastapi-gitops-starter \
  --set rollout.enabled=true \
  --set rollout.strategy=canary \
  --set replicaCount=3
```

Monitor the rollout:

```bash
kubectl argo rollouts get rollout my-app-fastapi-gitops-starter --watch
```

Promote the canary to production:

```bash
kubectl argo rollouts promote my-app-fastapi-gitops-starter
```


### Automated Analysis with Metrics

The chart supports automated analysis using AnalysisTemplates to make data-driven rollout decisions based on metrics from Prometheus or other monitoring systems.

#### Prerequisites for Analysis

- Prometheus installed and accessible in your cluster
- Nginx Ingress Controller with metrics enabled
- Service Monitor configured to scrape nginx ingress metrics

#### Traffic Generation for Metrics

When using analysis during rollouts, metrics like success rate, error rate, and response time require actual HTTP traffic to the application. Without traffic, these metrics may return no data (NaN) causing the analysis to fail.

The chart includes a **traffic generator** feature that creates a Kubernetes Job during the analysis phase to generate HTTP requests to your application endpoints. This ensures that metrics have data to measure.

Enable traffic generation:

```yaml
rollout:
  analysis:
    enabled: true
    trafficGenerator:
      enabled: true
      duration: 120            # Generate traffic for 120 seconds
      requestsPerSecond: 5     # 5 requests per second to each endpoint
      initialDelay: "5s"       # Start generating traffic after 5s
```

The traffic generator will:
- Send HTTP requests to all application endpoints (health, root, items)
- Run for the specified duration
- Maintain a steady request rate
- Provide traffic data for Prometheus metrics

#### Enable Analysis in Canary Deployments

Create a values file with analysis and traffic generation enabled:

```yaml
rollout:
  enabled: true
  strategy: canary
  canary:
    steps:
      - setWeight: 20
      - pause: {duration: 1m}
      - setWeight: 50
      - pause: {duration: 1m}
  analysis:
    enabled: true
    # Enable traffic generation
    trafficGenerator:
      enabled: true
      duration: 120
      requestsPerSecond: 5
    prometheus:
      address: "http://prometheus.monitoring:9090"
    metrics:
      # Monitor success rate (non-5xx responses)
      successRate:
        enabled: true
        interval: "1m"
        count: 5
        successCondition: "result >= 0.95"  # 95% success rate
        failureLimit: 2
      # Monitor response time (95th percentile)
      responseTime:
        enabled: true
        interval: "1m"
        count: 5
        successCondition: "result <= 1.0"  # Max 1 second
        failureLimit: 2
      # Monitor error rate (5xx responses)
      errorRate:
        enabled: true
        interval: "1m"
        count: 5
        successCondition: "result <= 0.05"  # Max 5% error rate
        failureLimit: 2
```

Install with:

```bash
helm install my-app ./helm/fastapi-gitops-starter -f example-canary-with-analysis-values.yaml
```

During a rollout, the analysis will:
1. Wait for the initial delay (default: 30s) to let the new version stabilize
2. Start measuring metrics at the specified interval (default: 1m)
3. Perform the specified count of measurements (default: 5)
4. If metrics meet the success conditions, the rollout continues
5. If metrics fail more than the failure limit, the rollout is automatically aborted

#### Enable Analysis in Blue-Green Deployments

For blue-green deployments, analysis runs during pre-promotion and post-promotion phases:

```yaml
rollout:
  enabled: true
  strategy: blueGreen
  blueGreen:
    activeService: ""
    previewService: ""
    autoPromotionEnabled: true
    autoPromotionSeconds: 30
  analysis:
    enabled: true
    prometheus:
      address: "http://prometheus.monitoring:9090"
    metrics:
      successRate:
        enabled: true
      responseTime:
        enabled: true
```

The analysis will run:
- **Pre-promotion**: Before switching traffic to the green version
- **Post-promotion**: After traffic is switched to verify the new version

#### Custom Metrics

You can add custom metrics to analyze any Prometheus query:

```yaml
rollout:
  analysis:
    enabled: true
    prometheus:
      address: "http://prometheus.monitoring:9090"
    metrics:
      custom:
        cpu-usage:
          interval: "1m"
          count: 5
          successCondition: "result <= 0.80"  # Max 80% CPU
          failureLimit: 2
          prometheus:
            query: |
              avg(rate(container_cpu_usage_seconds_total{
                namespace="default",
                pod=~"my-app-.*"
              }[5m]))
        memory-usage:
          interval: "1m"
          count: 5
          successCondition: "result <= 1073741824"  # Max 1GB
          failureLimit: 2
          prometheus:
            query: |
              avg(container_memory_working_set_bytes{
                namespace="default",
                pod=~"my-app-.*"
              })
```

#### Override Default Queries

The default queries assume nginx ingress controller metrics. You can override them:

```yaml
rollout:
  analysis:
    metrics:
      successRate:
        enabled: true
        query: |
          # Your custom Prometheus query
          sum(rate(http_requests_total{status!~"5..",app="my-app"}[5m]))
          /
          sum(rate(http_requests_total{app="my-app"}[5m]))
```

#### Monitor Analysis

View the analysis status during a rollout:

```bash
# Watch the rollout with analysis
kubectl argo rollouts get rollout my-app-fastapi-gitops-starter --watch

# Get analysis run details
kubectl get analysisrun -l rollout=my-app-fastapi-gitops-starter

# View analysis run details
kubectl describe analysisrun <analysis-run-name>
```

## Upgrading

To upgrade an existing release:

```bash
helm upgrade my-release ./helm/fastapi-gitops-starter \
  --set image.tag=2.0.0
```

## Testing the Installation

After installation, get the application URL:

### Via Ingress (default)

If you have an nginx ingress controller installed in your cluster:

```bash
# Get the ingress address
kubectl get ingress -l "app.kubernetes.io/name=fastapi-gitops-starter"
```

## Troubleshooting

### Check Pod Status

```bash
kubectl get pods -l app.kubernetes.io/name=fastapi-gitops-starter
```

### View Pod Logs

```bash
kubectl logs -l app.kubernetes.io/name=fastapi-gitops-starter
```

### Describe Deployment

```bash
kubectl describe deployment -l app.kubernetes.io/name=fastapi-gitops-starter
```

### Check Service

```bash
kubectl get svc -l app.kubernetes.io/name=fastapi-gitops-starter
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This chart follows the same license as the main project.
