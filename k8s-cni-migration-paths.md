# Kubernetes CNI Migration Paths

> **Version:** 1.0 (May 2025)
>
> This document is part of the [Kubernetes CNI Comparison Guide](k8s-cni-comparison.md) series.

## Table of Contents
- [Introduction](#introduction)
- [General Migration Considerations](#general-migration-considerations)
- [Flannel to Cilium](#flannel-to-cilium)
- [Calico to Cilium](#calico-to-cilium)
- [Other Migration Paths](#other-migration-paths)
- [Migration Best Practices](#migration-best-practices)

## Introduction

Migrating from one Container Network Interface (CNI) solution to another in a Kubernetes cluster can be challenging, especially in production environments. This document provides guidance on migration paths between different CNI solutions, with a focus on common migration scenarios, considerations, and best practices.

The migration paths described in this document are based on real-world experiences, community feedback, and official documentation. While every cluster is unique, these guidelines provide a solid foundation for planning and executing a successful CNI migration.

## General Migration Considerations

Before diving into specific migration paths, there are several general considerations that apply to most CNI migrations:

### 1. Assess the Need for Migration

Before migrating, clearly identify the reasons for changing your CNI solution:
- Performance limitations with the current CNI
- Need for advanced features not available in the current CNI
- Security requirements that the current CNI cannot meet
- Operational challenges with the current CNI
- End of support or development for the current CNI

### 2. Understand the Impact

Migrating CNI solutions will impact:
- Network connectivity for all pods
- Network policies and security
- Service discovery and DNS
- Load balancing and service exposure
- Integration with other components (service mesh, ingress controllers, etc.)

### 3. Plan for Downtime

Most CNI migrations require some level of downtime. Plan accordingly:
- Schedule maintenance windows
- Notify stakeholders
- Prepare rollback procedures
- Consider the impact on stateful applications

### 4. Test Thoroughly

Before migrating production clusters:
- Create a test cluster that mirrors your production environment
- Test the migration process end-to-end
- Validate all applications and services after migration
- Test rollback procedures
- Document any issues encountered and their solutions

### 5. Prepare Your Environment

Before starting the migration:
- Ensure your cluster is running a supported Kubernetes version
- Verify that nodes meet the requirements for the new CNI (kernel version, etc.)
- Back up all network policies and CNI configurations
- Document the current network topology and IP address allocation

### 6. Common Migration Strategies

There are several strategies for CNI migration:

#### Blue-Green Deployment
- Create a new cluster with the desired CNI
- Gradually move workloads to the new cluster
- Decommission the old cluster once migration is complete

**Pros**: Minimal risk, no downtime for applications
**Cons**: Requires additional resources, more complex for stateful applications

#### In-Place Migration
- Remove the existing CNI
- Install the new CNI on the same cluster
- Restart all pods to pick up the new CNI

**Pros**: No need for a new cluster, simpler for stateful applications
**Cons**: Requires downtime, higher risk

#### Node Pool Rotation
- Create a new node pool with the desired CNI
- Gradually drain and delete nodes from the old pool
- Add nodes to the new pool

**Pros**: Minimizes risk, can be done gradually
**Cons**: Requires careful coordination, may not work with all CNIs

## Flannel to Cilium

Migrating from Flannel to Cilium is a common path for organizations looking to enhance their network security and performance capabilities.

### Key Differences

| Aspect | Flannel | Cilium |
|:-------|:--------|:-------|
| **Network Model** | Layer 3 overlay (VXLAN) | eBPF-based networking |
| **Network Policies** | Not supported natively | Advanced L3-L7 policies |
| **Performance** | Good basic performance | High performance with eBPF |
| **Observability** | Limited | Advanced with Hubble |
| **Resource Usage** | Very lightweight | Moderate resource usage |
| **Complexity** | Simple | More complex |

### Migration Steps

1. **Preparation**
   - Ensure all nodes are running a compatible kernel version (4.9+ for basic Cilium, 5.3+ for all features)
   - Document all existing network configurations and IP ranges
   - Back up all Kubernetes resources

2. **Install Cilium Alongside Flannel**
   - Deploy Cilium in "chaining" mode, which allows it to run alongside Flannel
   - Configure Cilium to use a different VXLAN port than Flannel (8472 for Flannel, 8473 for Cilium)
   - Verify that Cilium pods are running correctly

   ```bash
   helm install cilium cilium/cilium \
     --namespace kube-system \
     --set chainingMode=flannel \
     --set flannelMasterDevice=cni0 \
     --set tunnel=vxlan \
     --set tunnelPort=8473
   ```

3. **Test Network Connectivity**
   - Deploy test applications to verify that pods can communicate
   - Test service discovery and DNS resolution
   - Verify that existing applications continue to function correctly

4. **Implement Network Policies**
   - If you were using Kubernetes Network Policies with a separate controller, migrate these to Cilium Network Policies
   - Start with permissive policies and gradually tighten them
   - Test policy enforcement before applying to all namespaces

5. **Complete Migration**
   - Once you've verified that Cilium is working correctly, you can remove Flannel
   - Update the Cilium configuration to operate in standalone mode
   - Restart all pods to ensure they use Cilium exclusively

   ```bash
   helm upgrade cilium cilium/cilium \
     --namespace kube-system \
     --set chainingMode=false \
     --set tunnel=vxlan \
     --set tunnelPort=8472
   ```

6. **Cleanup**
   - Remove Flannel DaemonSet and ConfigMap
   - Remove any Flannel-specific configurations from nodes
   - Verify that all pods are now using Cilium for networking

### Common Issues and Solutions

| Issue | Solution |
|:------|:---------|
| **Kernel Version Incompatibility** | Upgrade node kernels to at least 4.9+ (5.3+ recommended) |
| **IP Range Conflicts** | Ensure Cilium uses a different CIDR range than Flannel during migration |
| **Pod Communication Failures** | Check Cilium agent logs, verify MTU settings, ensure proper chaining configuration |
| **Network Policy Enforcement Issues** | Start with permissive policies, gradually implement stricter policies |
| **Performance Degradation** | Enable BPF NodePort, tune MTU settings, consider direct routing instead of VXLAN |

## Calico to Cilium

Migrating from Calico to Cilium is typically motivated by the desire for enhanced observability, eBPF-based performance improvements, and advanced L7 network policies.

### Key Differences

| Aspect | Calico | Cilium |
|:-------|:-------|:-------|
| **Network Model** | BGP or VXLAN/IPinIP | eBPF-based networking |
| **Network Policies** | L3-L4 policies | Advanced L3-L7 policies |
| **Performance** | Good performance | High performance with eBPF |
| **Observability** | Basic flow logs | Advanced with Hubble |
| **BGP Support** | Native, mature | Available but less mature |
| **Complexity** | Moderate | Higher |

### Migration Steps

1. **Preparation**
   - Ensure all nodes are running a compatible kernel version (4.9+ for basic Cilium, 5.3+ for all features)
   - Document all existing Calico network policies
   - Back up all Kubernetes resources
   - Document BGP configurations if using Calico with BGP

2. **Convert Network Policies**
   - Convert Calico network policies to Cilium network policies
   - Cilium supports Kubernetes Network Policies natively
   - For Calico-specific policies, create equivalent Cilium Network Policies
   - Test policy conversion in a non-production environment

3. **Plan IP Address Management**
   - Decide whether to use Cilium's IPAM or migrate Calico's IPAM configuration
   - If using Calico's IPAM with BGP, consider how to handle the transition
   - Document all IP pools and block allocations

4. **Deploy Cilium**
   - Install Cilium with the appropriate configuration for your environment
   - Configure Cilium to use the same or compatible CIDR ranges as Calico
   - If using BGP with Calico, configure Cilium's BGP integration

   ```bash
   helm install cilium cilium/cilium \
     --namespace kube-system \
     --set ipam.operator.clusterPoolIPv4PodCIDR=<your-pod-cidr> \
     --set ipam.mode=kubernetes
   ```

5. **Migrate Nodes**
   - For large clusters, consider a node-by-node migration approach
   - Drain a node, remove Calico, install Cilium, and uncordon
   - Alternatively, create a new node pool with Cilium and gradually migrate workloads

6. **Verify and Test**
   - Deploy test applications to verify network connectivity
   - Test network policy enforcement
   - Verify service discovery and DNS resolution
   - Monitor for any performance issues or connectivity problems

7. **Complete Migration**
   - Once all nodes are running Cilium, remove Calico components
   - Update any monitoring or operational tools to work with Cilium
   - Implement Hubble for enhanced observability

8. **Cleanup**
   - Remove Calico CRDs and resources
   - Clean up any Calico-specific configurations on nodes
   - Update documentation and operational procedures

### Common Issues and Solutions

| Issue | Solution |
|:------|:---------|
| **BGP Integration Challenges** | If using BGP with Calico, carefully plan how to migrate BGP configurations to Cilium |
| **Network Policy Differences** | Test converted policies thoroughly, be aware of semantic differences between Calico and Cilium policies |
| **IPAM Conflicts** | Ensure Cilium's IPAM doesn't conflict with existing IP allocations |
| **Performance Tuning** | Adjust Cilium's performance parameters based on your workload characteristics |
| **Monitoring Integration** | Update monitoring tools to collect metrics from Cilium instead of Calico |

## Other Migration Paths

### Weave Net to Cilium

Migrating from Weave Net to Cilium is typically motivated by performance improvements, advanced network policies, and better observability.

#### Key Considerations
- Weave Net uses a mesh overlay network, while Cilium uses eBPF
- Weave Net supports encryption, which Cilium also supports but implements differently
- Weave Net supports multicast, which Cilium does not natively support

#### Migration Approach
1. Deploy Cilium alongside Weave Net initially
2. Gradually migrate workloads to use Cilium
3. Pay special attention to encrypted traffic if using Weave Net's encryption
4. If using multicast with Weave Net, plan for alternative approaches with Cilium

### Canal to Cilium

Canal combines Flannel for networking and Calico for network policies. Migrating to Cilium consolidates both functions into a single solution.

#### Key Considerations
- Canal uses Flannel's networking with Calico's network policies
- Migrating to Cilium provides a unified solution for both networking and policy enforcement
- The migration process is similar to migrating from Flannel, but with additional consideration for Calico policies

#### Migration Approach
1. Convert Calico network policies to Cilium network policies
2. Follow the Flannel to Cilium migration path for the networking component
3. Test policy enforcement thoroughly after migration

### Flannel to Calico

While this document focuses on migrations to Cilium, migrating from Flannel to Calico is also common, particularly for organizations that need network policy enforcement but prefer Calico's approach.

#### Key Considerations
- Calico provides network policy enforcement that Flannel lacks
- Calico can use either BGP or overlay networking (VXLAN/IPinIP)
- Calico has more mature BGP integration than Cilium

#### Migration Approach
1. Install Calico with the appropriate IPAM and networking configuration
2. Gradually migrate nodes from Flannel to Calico
3. Implement network policies after the migration is complete

## Migration Best Practices

Regardless of the specific migration path, the following best practices will help ensure a successful CNI migration:

### 1. Documentation and Planning

- Document the current network architecture in detail
- Create a detailed migration plan with clear steps and responsibilities
- Define success criteria for the migration
- Establish a rollback plan in case of issues
- Schedule maintenance windows with stakeholders

### 2. Testing and Validation

- Create a test environment that mirrors production as closely as possible
- Test the migration process end-to-end before attempting in production
- Validate all applications and services after migration
- Test network policies thoroughly
- Perform load testing to identify any performance issues

### 3. Incremental Approach

- Consider migrating one node or node pool at a time
- Start with non-critical workloads
- Monitor closely after each step
- Be prepared to roll back if issues arise
- Gradually implement network policies, starting with monitoring mode

### 4. Monitoring and Observability

- Implement monitoring for both the old and new CNI during migration
- Set up alerts for network connectivity issues
- Use tools like Hubble (for Cilium) to gain visibility into network flows
- Monitor application performance and error rates
- Keep logs from both CNIs for troubleshooting

### 5. Communication and Coordination

- Communicate the migration plan to all stakeholders
- Coordinate with application teams to ensure they're aware of potential impacts
- Provide regular updates during the migration process
- Document any issues encountered and their solutions
- Share lessons learned after the migration

## Document Information

**Version:** 1.0
**Last Updated:** May 2025
**Author:** Personal research compilation
**Purpose:** Self-reference and knowledge sharing
**Feedback:** Corrections, updates, and feedback are welcome

This document is part of the [Kubernetes CNI Comparison Guide](k8s-cni-comparison.md) series.
