# Cilium in Managed Kubernetes Services

> **Version:** 1.0 (May 2025)
>
> This document is part of the [Kubernetes CNI Comparison Guide](k8s-cni-comparison.md) series.

## Table of Contents
- [Introduction](#introduction)
- [Overview of Cloud Provider Default CNIs](#overview-of-cloud-provider-default-cnis)
- [Comparison Chart: Cilium vs. Default CNIs](#comparison-chart-cilium-vs-default-cnis-in-managed-kubernetes)
- [AWS EKS with Cilium](#aws-eks-with-cilium)
- [Azure AKS with Cilium](#azure-aks-with-cilium)
- [GCP GKE with Cilium](#gcp-gke-with-cilium)
- [Common Issues and Solutions](#common-issues-and-solutions)
- [Decision Framework for Managed Kubernetes](#decision-framework-for-managed-kubernetes)
- [Data Sources](#data-sources-for-managed-kubernetes-recommendations)

## Introduction

Deploying Cilium in managed Kubernetes services (AWS EKS, Azure AKS, and GCP GKE) requires special considerations due to the unique networking architecture of each cloud provider. This document provides detailed guidance on using Cilium in these environments, including common issues, advantages, and disadvantages compared to the default CNI providers.

## Overview of Cloud Provider Default CNIs

Before discussing Cilium deployment, it's important to understand the default CNI solutions used by major cloud providers:

| Cloud Provider | Logo | Default CNI | Key Characteristics |
|:---------------|:-----|:------------|:-------------------|
| **AWS EKS** | <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiNGRjk5MDAiPkFXUyBFS1M8L3RleHQ+PC9zdmc+" width="120" alt="AWS EKS Logo"> | Amazon VPC CNI | Uses EC2 networking, assigns pod IPs from VPC CIDR, native AWS integration |
| **Azure AKS** | <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiMwMDc4RDQiPkF6dXJlIEFLUzwvdGV4dD48L3N2Zz4=" width="120" alt="Azure AKS Logo"> | Azure CNI | Assigns pod IPs from VNet, integrates with Azure networking features |
| **GCP GKE** | <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiM0Mjg1RjQiPkdDUCBHS0U8L3RleHQ+PC9zdmc+" width="120" alt="GCP GKE Logo"> | Kubenet/Calico | Kubenet for basic networking, Calico for network policies |

## Comparison Chart: Cilium vs. Default CNIs in Managed Kubernetes

<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiMwMDk2MzkiPkNpbGl1bTwvdGV4dD48L3N2Zz4=" width="150" alt="Cilium Logo">

The following chart compares Cilium with the default CNIs across key dimensions:

| Feature/Consideration | Cilium | AWS VPC CNI | Azure CNI | GCP Kubenet/Calico |
|:----------------------|:-------|:------------|:----------|:-------------------|
| **Performance** | <span style="color:green">**★★★★★**</span> | <span style="color:green">**★★★★**</span>☆ | <span style="color:orange">**★★★**</span>☆☆ | <span style="color:green">**★★★★**</span>☆ |
| **Security Features** | <span style="color:green">**★★★★★**</span> | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:green">**★★★★**</span>☆ (with Calico) |
| **Observability** | <span style="color:green">**★★★★★**</span> | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:orange">**★★★**</span>☆☆ |
| **Cloud Integration** | <span style="color:orange">**★★★**</span>☆☆ | <span style="color:green">**★★★★★**</span> | <span style="color:green">**★★★★★**</span> | <span style="color:green">**★★★★★**</span> |
| **Operational Complexity** | <span style="color:orange">**★★★**</span>☆☆ | <span style="color:green">**★★★★★**</span> | <span style="color:green">**★★★★**</span>☆ | <span style="color:green">**★★★★**</span>☆ |
| **IP Address Efficiency** | <span style="color:green">**★★★★★**</span> | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:green">**★★★★**</span>☆ |
| **Multi-cluster Support** | <span style="color:green">**★★★★★**</span> | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:orange">**★★**</span>☆☆☆ | <span style="color:orange">**★★★**</span>☆☆ |
| **L7 Visibility** | <span style="color:green">**★★★★★**</span> | <span style="color:red">**☆☆☆☆☆**</span> | <span style="color:red">**☆☆☆☆☆**</span> | <span style="color:orange">**★★**</span>☆☆☆ |
| **Upgrade Complexity** | <span style="color:orange">**★★★**</span>☆☆ | <span style="color:green">**★★★★★**</span> | <span style="color:green">**★★★★**</span>☆ | <span style="color:green">**★★★★**</span>☆ |

*Legend: <span style="color:green">**★★★★★**</span> Excellent, <span style="color:green">**★★★★**</span>☆ Very Good, <span style="color:orange">**★★★**</span>☆☆ Good, <span style="color:orange">**★★**</span>☆☆☆ Fair, <span style="color:red">**☆☆☆☆☆**</span> Poor/Not Available*

## AWS EKS with Cilium

<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiNGRjk5MDAiPkFXUyBFS1M8L3RleHQ+PC9zdmc+" width="150" alt="AWS EKS Logo">

### Deployment Options

1. **Replace Amazon VPC CNI**: Completely replace the default CNI with Cilium
2. **Chained CNI**: Run Cilium alongside Amazon VPC CNI in chaining mode
3. **ENI Mode**: Run Cilium in ENI mode, which uses Amazon VPC CNI for IP allocation

### Advantages of Cilium in EKS

- **Performance**: eBPF-based networking provides lower latency and higher throughput
- **Advanced Network Policies**: L3-L7 policies vs. basic L3-L4 policies with VPC CNI
- **Observability**: Hubble provides detailed visibility into network flows
- **IP Address Efficiency**: Better utilization of IP address space compared to VPC CNI
- **Multi-cluster Support**: ClusterMesh enables seamless multi-cluster connectivity
- **Service Mesh Capabilities**: Can replace or complement service mesh functionality

### Challenges and Considerations

- **ENI Limits**: AWS imposes limits on the number of ENIs per instance type, which can impact pod density
- **AWS Integration**: Some AWS-specific features may require additional configuration
- **Operational Complexity**: Requires more expertise to operate compared to the default CNI
- **Upgrade Coordination**: Need to coordinate Cilium upgrades with EKS version upgrades
- **Load Balancer Integration**: May require additional configuration for AWS Load Balancer Controller

### Implementation Example

```yaml
# EKS-specific Cilium Helm values
apiVersion: v1
kind: ConfigMap
metadata:
  name: cilium-config
  namespace: kube-system
data:
  # EKS-specific settings
  enable-endpoint-routes: "true"
  auto-create-cilium-node-resource: "true"
  
  # ENI mode configuration (if using)
  ipam: "eni"
  eni-tags: "{\"cluster\": \"eks-cluster-name\"}"
  
  # AWS Load Balancer integration
  enable-aws-lb-controller: "true"
  
  # Performance tuning
  enable-bpf-masquerade: "true"
  enable-local-redirect-policy: "true"
  
  # Observability
  enable-hubble: "true"
  hubble-metrics-server: ":9091"
  hubble-metrics: "drop,tcp,flow,icmp,http"
```

## Azure AKS with Cilium

<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiMwMDc4RDQiPkF6dXJlIEFLUzwvdGV4dD48L3N2Zz4=" width="150" alt="Azure AKS Logo">

### Deployment Options

1. **Replace Azure CNI**: Completely replace the default CNI with Cilium
2. **Azure IPAM Mode**: Use Cilium with Azure IPAM for IP allocation
3. **Overlay Mode**: Use Cilium in overlay mode (VXLAN/Geneve)

### Advantages of Cilium in AKS

- **Performance**: Significant performance improvements, especially with eBPF
- **IP Address Efficiency**: Better utilization of IP address space compared to Azure CNI
- **Advanced Security**: L7 visibility and policies not available with Azure CNI
- **Observability**: Detailed network flow visibility with Hubble
- **Consistent Multi-cloud**: Same networking solution across different cloud providers

### Challenges and Considerations

- **Azure Network Policy Integration**: Replacing Azure CNI means losing native Azure Network Policy
- **AKS-specific Features**: Some AKS features may be tightly coupled with Azure CNI
- **Support Considerations**: Microsoft support may be limited for custom CNI deployments
- **Upgrade Complexity**: Requires careful planning when upgrading AKS clusters
- **Resource Requirements**: Higher control plane resource requirements

### Implementation Example

```yaml
# AKS-specific Cilium Helm values
apiVersion: v1
kind: ConfigMap
metadata:
  name: cilium-config
  namespace: kube-system
data:
  # AKS-specific settings
  enable-endpoint-routes: "true"
  
  # Azure integration
  ipam: "azure"
  azure-resource-group: "aks-resource-group"
  azure-subscription-id: "subscription-id"
  
  # Performance tuning
  enable-bpf-masquerade: "true"
  enable-local-redirect-policy: "true"
  
  # Observability
  enable-hubble: "true"
  hubble-metrics-server: ":9091"
  hubble-metrics: "drop,tcp,flow,icmp,http"
```

## GCP GKE with Cilium

<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiM0Mjg1RjQiPkdDUCBHS0U8L3RleHQ+PC9zdmc+" width="150" alt="GCP GKE Logo">

### Deployment Options

1. **Replace Kubenet/Calico**: Completely replace the default CNI with Cilium
2. **GKE Dataplane V2**: Use GKE Dataplane V2, which is based on eBPF (similar to Cilium)
3. **Overlay Mode**: Use Cilium in overlay mode (VXLAN/Geneve)

### Advantages of Cilium in GKE

- **Performance**: Better performance compared to Kubenet, especially for cross-node traffic
- **Advanced Network Policies**: More sophisticated policies than standard Calico in GKE
- **Observability**: Detailed network flow visibility with Hubble
- **Service Mesh Integration**: Can replace or complement Istio/Anthos Service Mesh
- **Multi-cluster Support**: ClusterMesh for GKE multi-cluster networking

### Challenges and Considerations

- **GKE Dataplane V2**: GKE already offers an eBPF-based dataplane, reducing the advantage gap
- **GKE Autopilot Compatibility**: Limited compatibility with GKE Autopilot
- **Google Cloud Integration**: Some GCP-specific features may require additional configuration
- **Upgrade Complexity**: Requires careful planning when upgrading GKE clusters
- **Support Considerations**: Google support may be limited for custom CNI deployments

### Implementation Example

```yaml
# GKE-specific Cilium Helm values
apiVersion: v1
kind: ConfigMap
metadata:
  name: cilium-config
  namespace: kube-system
data:
  # GKE-specific settings
  enable-endpoint-routes: "true"
  
  # GCP integration
  ipam: "kubernetes"
  
  # Performance tuning
  enable-bpf-masquerade: "true"
  enable-local-redirect-policy: "true"
  
  # Observability
  enable-hubble: "true"
  hubble-metrics-server: ":9091"
  hubble-metrics: "drop,tcp,flow,icmp,http"
```

## Common Issues and Solutions

| Issue | Description | Solution |
|:------|:------------|:---------|
| **IP Exhaustion** | Default CNIs often allocate entire subnet ranges to nodes | Use Cilium's more efficient IPAM to increase pod density |
| **Cross-AZ Latency** | High latency for cross-AZ traffic with default CNIs | Enable Cilium's optimized routing for cross-AZ traffic |
| **Limited Observability** | Default CNIs provide minimal visibility into network flows | Enable Hubble for comprehensive network observability |
| **Network Policy Limitations** | Default CNIs often have limited policy capabilities | Use Cilium's advanced L3-L7 network policies |
| **Multi-cluster Complexity** | Complex setup for multi-cluster networking | Implement Cilium ClusterMesh for simplified multi-cluster connectivity |
| **Service Mesh Overhead** | Service mesh implementations add significant overhead | Consider Cilium's built-in service mesh capabilities |
| **Kernel Compatibility** | Cilium requires specific kernel versions | Ensure managed service node pools use compatible kernel versions |
| **Upgrade Coordination** | Coordinating CNI and cluster upgrades | Develop a clear upgrade strategy and testing process |

## Decision Framework for Managed Kubernetes

Consider the following decision tree when evaluating Cilium for managed Kubernetes services:

```
Start
│
├─ Do you need advanced network policies (L7)?
│  ├─ Yes ──► Cilium is strongly recommended
│  └─ No ───► Continue
│
├─ Is network observability a priority?
│  ├─ Yes ──► Cilium is strongly recommended
│  └─ No ───► Continue
│
├─ Are you experiencing IP address exhaustion?
│  ├─ Yes ──► Cilium can help
│  └─ No ───► Continue
│
├─ Do you need multi-cluster networking?
│  ├─ Yes ──► Cilium is recommended
│  └─ No ───► Continue
│
├─ Is operational simplicity the top priority?
│  ├─ Yes ──► Default CNI may be better
│  └─ No ───► Cilium may be worth the complexity
│
├─ Do you heavily rely on cloud-provider specific features?
│  ├─ Yes ──► Default CNI may be better
│  └─ No ───► Cilium is a good option
│
└─ Consider other factors:
   - Team expertise with eBPF/Cilium
   - Support requirements
   - Performance needs
   - Future networking requirements
```

## Data Sources for Managed Kubernetes Recommendations

The recommendations and comparisons in this section are based on:

1. **Performance Benchmarks**: Independent testing of Cilium vs. default CNIs in managed Kubernetes environments
2. **User Testimonials**: Feedback from organizations running Cilium in production on EKS, AKS, and GKE
3. **Official Documentation**: Cilium and cloud provider documentation
4. **Community Feedback**: Issues and discussions in Cilium and Kubernetes community forums
5. **Expert Interviews**: Insights from cloud platform specialists and Cilium maintainers
6. **Case Studies**: Published case studies of Cilium deployments in managed Kubernetes environments

The ratings and comparisons represent a synthesis of these sources, with particular emphasis on real-world production deployments and documented performance characteristics.

## Document Information

**Version:** 1.0
**Last Updated:** May 2025
**Author:** Personal research compilation
**Purpose:** Self-reference and knowledge sharing
**Feedback:** Corrections, updates, and feedback are welcome

This document is part of the [Kubernetes CNI Comparison Guide](k8s-cni-comparison.md) series.
