# Kubernetes CNI Quick Reference Guide

> **Version:** 1.0 (May 2025)
>
> This is a simplified quick reference companion to the comprehensive [Kubernetes CNI Comparison Guide](k8s-cni-comparison.md).
>
> **Methodology Note:** The ratings and recommendations in this guide are based on data from official documentation, CNCF surveys (2024), independent benchmarks, user testimonials, expert interviews, and hands-on testing. See the [full guide](k8s-cni-comparison.md) for detailed methodology.

## CNI Solutions at a Glance

| CNI | Key Strength | Best For | Avoid If | Key Differentiator |
|-----|--------------|----------|----------|-------------------|
| **Cilium** | Advanced security & performance | Security-focused, high-performance workloads | You need simplicity or have older kernels | eBPF-based networking with L7 visibility |
| **Calico** | Mature, flexible networking | Enterprise deployments, BGP integration | You need L7 policies or simplicity | BGP routing with strong network policies |
| **Flannel** | Simplicity & ease of use | Dev/test, learning, simple deployments | You need network policies or advanced features | Lightweight with minimal configuration |
| **Weave Net** | Encryption & multicast | Multicast workloads, hybrid deployments | You need high performance or advanced features | Mesh topology with multicast support |
| **Canal** | Balanced features & simplicity | Basic security needs without complexity | You need advanced features or high scalability | Flannel networking + Calico policies |

## Feature Comparison Snapshot

| Feature | Cilium | Calico | Flannel | Weave Net | Canal |
|:--------|:-------|:-------|:--------|:----------|:------|
| **Network Policies** | ✅✅✅ (L3-L7) | ✅✅ (L3-L4) | ❌ | ✅ | ✅✅ |
| **Performance** | ✅✅✅ | ✅✅ | ✅ | ✅ | ✅ |
| **Simplicity** | ✅ | ✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Encryption** | ✅✅ | ✅✅ | ❌ | ✅✅ | ❌ |
| **Observability** | ✅✅✅ | ✅✅ | ✅ | ✅ | ✅✅ |
| **Multi-cluster** | ✅✅✅ | ✅✅ | ❌ | ✅ | ❌ |
| **Resource Usage** | ✅✅ | ✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Maturity** | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ | ✅✅ |
| **Windows Node Support** | ✅ | ✅✅✅ | ✅✅ | ❌ | ✅ |

Legend: ✅✅✅ Excellent, ✅✅ Good, ✅ Basic, ❌ Not supported

*Note: Ratings are based on comparative analysis of features, performance benchmarks, and real-world usage data as of May 2025. Ratings prioritize production readiness, feature completeness, and documented performance in typical deployment scenarios.*

## Quick Decision Guide

1. **Need advanced security with application-layer (L7) visibility?**
   - Yes → Cilium
   - No → Continue

2. **Need network policies but want simplicity?**
   - Yes → Canal
   - No → Continue

3. **Need the simplest possible solution?**
   - Yes → Flannel
   - No → Continue

4. **Need BGP integration or support for Windows worker nodes?**
   - Yes → Calico
   - No → Continue

5. **Need multicast support?**
   - Yes → Weave Net
   - No → Continue

6. **Need multi-cluster networking?**
   - Yes → Cilium or Calico
   - No → Continue

7. **Need the best performance?**
   - Yes → Cilium
   - No → Consider other factors like maturity, community support, and familiarity

## CNI Selection by Use Case

| Use Case | Recommended CNI | Alternative |
|----------|-----------------|------------|
| **Production Enterprise** | Cilium or Calico | Canal |
| **Small/Medium Business** | Calico or Canal | Flannel |
| **Development/Testing** | Flannel | Canal |
| **Edge/IoT** | Flannel | Cilium |
| **Multi-cloud/Hybrid** | Cilium or Calico | - |
| **High Security** | Cilium | Calico |
| **High Performance** | Cilium | Calico |
| **Managed K8s** | Provider default | Cilium |
| **Learning Environment** | Flannel | Canal |

*Note: These recommendations are derived from analysis of production deployments, user testimonials, and performance characteristics in specific environments. They represent common patterns of adoption rather than absolute rules. Your specific requirements may lead to different optimal choices. The recommendations were developed based on documented use cases from the CNCF ecosystem, vendor case studies, and community feedback from production users.*

## Cilium in Managed Kubernetes Services

<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiMwMDk2MzkiPkNpbGl1bTwvdGV4dD48L3N2Zz4=" width="150" alt="Cilium Logo">

| Cloud Provider | Logo | Default CNI | Cilium Advantages | Cilium Challenges |
|:---------------|:-----|:------------|:------------------|:------------------|
| **AWS EKS** | <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiNGRjk5MDAiPkFXUyBFS1M8L3RleHQ+PC9zdmc+" width="100" alt="AWS EKS Logo"> | Amazon VPC CNI | • Better performance<br>• Advanced network policies<br>• Enhanced observability<br>• IP address efficiency<br>• Multi-cluster support | • ENI limits<br>• AWS integration complexity<br>• Operational overhead<br>• Upgrade coordination |
| **Azure AKS** | <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiMwMDc4RDQiPkF6dXJlIEFLUzwvdGV4dD48L3N2Zz4=" width="100" alt="Azure AKS Logo"> | Azure CNI | • Better performance<br>• IP address efficiency<br>• L7 visibility & policies<br>• Enhanced observability<br>• Consistent multi-cloud | • Loss of native Azure policies<br>• Support limitations<br>• Upgrade complexity<br>• Resource requirements |
| **GCP GKE** | <img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIHZpZXdCb3g9IjAgMCAyMDAgODAiPjxyZWN0IHdpZHRoPSIyMDAiIGhlaWdodD0iODAiIGZpbGw9IiNmZmYiLz48dGV4dCB4PSIyMCIgeT0iNTAiIGZvbnQtZmFtaWx5PSJBcmlhbCIgZm9udC1zaXplPSIzMCIgZm9udC13ZWlnaHQ9ImJvbGQiIGZpbGw9IiM0Mjg1RjQiPkdDUCBHS0U8L3RleHQ+PC9zdmc+" width="100" alt="GCP GKE Logo"> | Kubenet/Calico | • Better performance<br>• Advanced network policies<br>• Enhanced observability<br>• Service mesh integration<br>• Multi-cluster support | • GKE Dataplane V2 overlap<br>• Autopilot compatibility<br>• Support limitations<br>• Upgrade complexity |

**When to Choose Cilium over Default CNI:**
- You need advanced L7 network policies
- Network observability is a priority
- You're experiencing IP address exhaustion
- You need multi-cluster networking
- Performance is critical, especially for cross-node traffic

**When to Stick with Default CNI:**
- Operational simplicity is the top priority
- You heavily rely on cloud-provider specific features
- Your team lacks eBPF/Cilium expertise
- You need guaranteed cloud provider support

*For detailed deployment guidance, configuration examples, and migration strategies, see the [full guide](k8s-cni-comparison.md).*

## Migration Quick Reference

### General Migration Steps

1. Install new CNI alongside existing CNI
2. Configure new CNI to use a separate CIDR
3. Migrate nodes one by one
4. Remove old CNI components

### Flannel to Cilium

- **Key Considerations**:
  - Use different VXLAN port (8473 vs 8472)
  - Use different CIDR during migration
  - Implement network policies after migration

### Calico to Cilium

- **Key Considerations**:
  - Convert Calico network policies to Cilium format
  - Reconfigure BGP if using Calico's BGP capabilities
  - Plan for IP address management differences

### Other Migrations

- **Weave Net to Cilium**: Address encryption and multicast needs
- **Canal to Cilium**: Convert network policies and address dual components
- **Flannel to Calico**: Simpler migration for adding network policies without eBPF

## Resource Requirements Snapshot

| CNI | Control Plane Memory | Control Plane CPU | Minimum K8s Version | Kernel Requirements |
|-----|----------------------|-------------------|---------------------|---------------------|
| **Cilium** | 500MB-1GB | 0.5-1.0 CPU | v1.16+ | 4.9+ (5.3+ for full features) |
| **Calico** | 300-600MB | 0.3-0.6 CPU | v1.16+ | 3.10+ |
| **Flannel** | 50-100MB | 0.1-0.2 CPU | v1.9+ | 3.10+ |
| **Weave Net** | 200-400MB | 0.2-0.4 CPU | v1.9+ | 3.10+ |
| **Canal** | 350-700MB | 0.4-0.8 CPU | v1.16+ | 3.10+ |

*Note: Resource requirements are approximate and based on official documentation, community benchmarks, and observed values in medium-sized clusters (20-50 nodes). Actual requirements may vary based on cluster size, workload characteristics, and specific configuration options. Data was collected from official documentation, GitHub issues, and community testing as of May 2025.*

## Key Best Practices

1. **All CNIs**:
   - Implement network policies
   - Plan IP address management carefully
   - Monitor network performance
   - Test upgrades in non-production first

2. **Cilium**:
   - Ensure kernel version compatibility (5.3+ ideal)
   - Enable Hubble for observability
   - Use eBPF NodePort for better performance

3. **Calico**:
   - Choose the right dataplane (standard Linux, eBPF, Windows)
   - Configure BGP properly if using it
   - Use hierarchical approach to network policies

4. **Flannel**:
   - Select appropriate backend (VXLAN, host-gw)
   - Consider complementary tools for network policies
   - Configure MTU settings properly

5. **Weave Net**:
   - Enable fast datapath when possible
   - Only use encryption when needed
   - Be aware of mesh topology scalability limits

## Future Trends

- **eBPF Adoption**: Becoming dominant technology for CNI solutions
- **Multi-cluster Networking**: Seamless connectivity across clusters
- **Zero Trust Security**: Identity-based policies and encryption by default
- **CNI/Service Mesh Convergence**: Integration or consolidation of functionality
- **Enhanced Observability**: Deeper visibility into network traffic

*Note: These trends are based on project roadmaps, KubeCon presentations, CNCF TAG Network discussions, and interviews with Kubernetes networking experts as of May 2025. See the [full guide](k8s-cni-comparison.md) for detailed analysis of these trends.*
