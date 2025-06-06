# Personal research notes (docs, guides, cheatsheets)
_which may contain errors, omissions, etc._

A collection of guides, research notes, comparisons, reference materials, etc. for cloud-native technologies.

## Available Documentation

### Kubernetes Management
- [Kubernetes Management Ecosystem Guide](k8s-control-plane-manager.md) - Comprehensive analysis of hosted control plane solutions and multi-cluster configuration managers, including:
  - Top 5 hosted control plane managers (Kamaji, vCluster, Gardener, K3v, CAPI)
  - Top 5 multi-cluster configuration managers (Sveltos by Cisco Systems, Karmada, KubeVela, Argo CD, Flux CD)
  - Detailed comparison tables and metrics
  - Integration patterns and best practices
  - Future trends and recommendations

- [Kubernetes for Hybrid Cloud and On-Premises Environments](k8s-hybrid-env-recommendations.md) - Detailed guide for implementing Kubernetes across hybrid infrastructure, including:
  - Enterprise Kubernetes distributions comparison (Rancher, OpenShift, Tanzu, EKS Anywhere, Anthos)
  - Critical infrastructure components for hybrid deployments
  - Service mesh selection for hybrid environments
  - Real-world implementation examples
  - Strategic recommendations for different use cases
  - Emerging technologies including Cisco Systems' Sveltos project

- [Kubernetes FinOps and Cost Optimization Guide](k8s-finops-cost-optimization.md) - Comprehensive guide to financial operations and cost management for Kubernetes, including:
  - Cost visibility and allocation strategies
  - Build vs. Buy decision framework with TCO analysis
  - Hidden costs of DIY solutions vs. commercial offerings
  - Cost optimization techniques for clusters, workloads, and storage
  - Comparison of FinOps tools and platforms
  - Implementation roadmap and case studies
  - Future trends in cloud-native cost management

- [Kubernetes Stateful Workloads: Multi-Region and High Availability Guide](k8s-stateful-workloads-ha-guide.md) - Detailed guide for implementing highly available stateful workloads across multiple regions and availability zones, including:
  - Cloud provider architecture comparison (AWS, Azure, GCP)
  - Stateful workload operator implementations (Kafka, Elasticsearch, PostgreSQL, MongoDB, Redis)
  - Implementation patterns for multi-AZ and multi-region deployments
  - Cloud-specific implementation guides
  - Performance benchmarks and cost considerations
  - Real-world case studies and implementation recommendations

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
    - [CNI Migration Tool](k8s-cni-migration-tool.md) - Tool for assessing and facilitating migration to Cilium (relocated to [dedicated repository](https://github.com/cmcconnell1/k8s-cni-migration-tool))
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

- [Kubernetes Ingress Controllers Comparison](k8s-ingress-controllers-comparison.md) - Detailed comparison of standard vs. cloud provider ingress controllers, including:
  - Analysis of standard controllers (NGINX, Traefik, HAProxy, etc.)
  - Cloud provider options (AWS ALB, GKE Ingress, Azure Application Gateway)
  - Feature and performance comparisons
  - Cost considerations and operational trade-offs
  - Decision framework and implementation guidance
  - Real-world case studies

## Repository Structure

```
.
├── README.md                                # This file
├── k8s-control-plane-manager.md             # Kubernetes management ecosystem guide
├── k8s-hybrid-env-recommendations.md        # Kubernetes for hybrid cloud environments
├── k8s-finops-cost-optimization.md          # Kubernetes FinOps and cost optimization guide
├── k8s-stateful-workloads-ha-guide.md       # Kubernetes stateful workloads HA guide
├── k8s-cni-comparison.md                    # Kubernetes CNI comparison guide (main document)
├── k8s-cni-solutions-detail.md              # Kubernetes CNI solutions in detail
├── k8s-cni-comparison-tables.md             # Kubernetes CNI comparison tables
├── k8s-cni-decision-framework.md            # Kubernetes CNI decision framework
├── k8s-cni-managed-services.md              # Kubernetes CNI in managed services
├── k8s-cni-migration-paths.md               # Kubernetes CNI migration paths
├── k8s-cni-migration-tool.md                # Kubernetes CNI migration tool (relocated)
├── k8s-cni-best-practices.md                # Kubernetes CNI best practices
├── k8s-cni-future-trends.md                 # Kubernetes CNI future trends
├── k8s-cni-comparison-quick-reference.md    # Kubernetes CNI quick reference guide
├── api-gateway-service-mesh-comparison.md   # API Gateway and Service Mesh comparison guide
├── k8s-ingress-controllers-comparison.md    # Kubernetes Ingress Controllers comparison
└── .gitignore                               # Git ignore rules for documentation project
```

## Document Status

| Document | Version | Last Updated | Status |
|----------|---------|--------------|--------|
| [Kubernetes Management Ecosystem](k8s-control-plane-manager.md) | 1.0 | May 2025 | Active |
| [Kubernetes for Hybrid Cloud and On-Premises](k8s-hybrid-env-recommendations.md) | 1.0 | July 2024 | Active |
| [Kubernetes FinOps and Cost Optimization](k8s-finops-cost-optimization.md) | 1.0 | May 2025 | Active |
| [Kubernetes Stateful Workloads: Multi-Region and HA Guide](k8s-stateful-workloads-ha-guide.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Comparison](k8s-cni-comparison.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Solutions Detail](k8s-cni-solutions-detail.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Comparison Tables](k8s-cni-comparison-tables.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Decision Framework](k8s-cni-decision-framework.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Managed Services](k8s-cni-managed-services.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Migration Paths](k8s-cni-migration-paths.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Migration Tool](k8s-cni-migration-tool.md) | 1.0 | May 2025 | Relocated |
| [Kubernetes CNI Best Practices](k8s-cni-best-practices.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Future Trends](k8s-cni-future-trends.md) | 1.0 | May 2025 | Active |
| [Kubernetes CNI Quick Reference](k8s-cni-comparison-quick-reference.md) | 1.0 | May 2025 | Active |
| [API Gateway and Service Mesh Comparison](api-gateway-service-mesh-comparison.md) | 1.0 | April 2025 | Active |
| [Kubernetes Ingress Controllers Comparison](k8s-ingress-controllers-comparison.md) | 1.0 | May 2025 | Active |

## Disclaimer

These documents represent personal research notes and assessments. They are not official guidance from any vendor, the CNCF, or Kubernetes project. Always verify information against official documentation and conduct your own testing for specific use cases.

## License

This documentation is provided "as is" without warranty of any kind. Use at your own risk.