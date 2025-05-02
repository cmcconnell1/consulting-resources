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

- [Kubernetes CNI Comparison Guide](k8s-cni-comparison.md) - Detailed analysis of Kubernetes Container Network Interface (CNI) solutions, including:
  - Top CNI options (Cilium, Calico, Flannel, Weave Net, Canal)
  - Feature and performance comparisons
  - Decision-making flowchart based on requirements
  - Migration paths between CNI providers
  - Best practices and future trends
  - Specialized documents:
    - [CNI Solutions in Detail](k8s-cni-solutions-detail.md) - Detailed information about each CNI solution
    - [Comparison Tables](k8s-cni-comparison-tables.md) - Feature, performance, resource, and community comparisons
    - [Decision Framework](k8s-cni-decision-framework.md) - Flowchart and guidance for selecting a CNI
    - [Managed Kubernetes Services](k8s-cni-managed-services.md) - CNI considerations for AWS EKS, Azure AKS, and GCP GKE
    - [Migration Paths](k8s-cni-migration-paths.md) - Guidance for migrating between CNI solutions
    - [Best Practices](k8s-cni-best-practices.md) - Comprehensive guidance for deploying and operating CNI solutions
    - [Future Trends](k8s-cni-future-trends.md) - Emerging trends in Kubernetes networking
    - [Quick Reference](k8s-cni-comparison-quick-reference.md) - Condensed guide for rapid decision-making

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
├── k8s-cni-comparison.md                    # Kubernetes CNI comparison guide (main document)
├── k8s-cni-solutions-detail.md              # Kubernetes CNI solutions in detail
├── k8s-cni-comparison-tables.md             # Kubernetes CNI comparison tables
├── k8s-cni-decision-framework.md            # Kubernetes CNI decision framework
├── k8s-cni-managed-services.md              # Kubernetes CNI in managed services
├── k8s-cni-migration-paths.md               # Kubernetes CNI migration paths
├── k8s-cni-best-practices.md                # Kubernetes CNI best practices
├── k8s-cni-future-trends.md                 # Kubernetes CNI future trends
├── k8s-cni-comparison-quick-reference.md    # Kubernetes CNI quick reference guide
├── api-gateway-service-mesh-comparison.md   # API Gateway and Service Mesh comparison guide
└── .gitignore                               # Git ignore rules for documentation project
```

## Document Status

| Document | Version | Last Updated | Status |
|----------|---------|--------------|--------|
| [Kubernetes Management Ecosystem](k8s-control-plane-manager.md) | 1.0 | April 2025 | Active |
| [Kubernetes CNI Comparison](k8s-cni-comparison.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Solutions Detail](k8s-cni-solutions-detail.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Comparison Tables](k8s-cni-comparison-tables.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Decision Framework](k8s-cni-decision-framework.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Managed Services](k8s-cni-managed-services.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Migration Paths](k8s-cni-migration-paths.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Best Practices](k8s-cni-best-practices.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Future Trends](k8s-cni-future-trends.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Quick Reference](k8s-cni-comparison-quick-reference.md) | 1.0 | May 2025 | Active |
| [API Gateway and Service Mesh Comparison](api-gateway-service-mesh-comparison.md) | 1.0 | April 2025 | Active |

## Disclaimer

These documents represent personal research notes and assessments. They are not official guidance from any vendor, the CNCF, or Kubernetes project. Always verify information against official documentation and conduct your own testing for specific use cases.

## License

This documentation is provided "as is" without warranty of any kind. Use at your own risk.