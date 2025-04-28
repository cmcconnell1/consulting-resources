# Personal research notes (docs, guides, cheatsheets)
_which may contain errors, omissions, etc._

A collection of guides, research notes, comparisons, reference materials, etc. for cloud-native technologies.

## Available Documentation

### Kubernetes Management
- [Kubernetes Management Ecosystem Guide](k8s-control-plane-manager.md) - Comprehensive analysis of hosted control plane solutions and multi-cluster configuration managers, including:
  - Top 5 hosted control plane managers (Kamaji, vCluster, Gardener, K3v, CAPI)
  - Top 5 multi-cluster configuration managers (Sveltos, Karmada, KubeVela, Argo CD, Flux CD)
  - Detailed comparison tables and metrics
  - Integration patterns and best practices
  - Future trends and recommendations

### API Management & Service Communication
- [API Gateway and Service Mesh Comparison Guide](api-gateway-service-mesh-comparison.md) - Detailed analysis of API Gateway and Service Mesh solutions, including:
  - Top 5 API Gateway solutions (Kong, Ambassador/Emissary, NGINX, Gloo Edge, Tyk)
  - Top 5 Service Mesh implementations (Istio, Linkerd, Consul Connect, Cilium, Kuma)
  - Comprehensive feature and performance comparisons
  - Integration patterns between API Gateways and Service Meshes
  - Decision frameworks and best practices

## Repository Structure

```
.
├── README.md                                # This file
├── k8s-control-plane-manager.md             # Kubernetes management ecosystem guide
├── api-gateway-service-mesh-comparison.md   # API Gateway and Service Mesh comparison guide
└── .gitignore                               # Git ignore rules for documentation project
```

## Document Status

| Document | Version | Last Updated | Status |
|----------|---------|--------------|--------|
| [Kubernetes Management Ecosystem](k8s-control-plane-manager.md) | 1.0 | April 2025 | Active |
| [API Gateway and Service Mesh Comparison](api-gateway-service-mesh-comparison.md) | 1.0 | April 2025 | Active |

## Disclaimer

These documents represent personal research notes and assessments. They are not official guidance from any vendor, the CNCF, or Kubernetes project. Always verify information against official documentation and conduct your own testing for specific use cases.

## License

This documentation is provided "as is" without warranty of any kind. Use at your own risk.