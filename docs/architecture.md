# Architecture

## Overview

This platform implements the shared Azure foundation for the Azure Landing Zone environment.

The architecture separates platform responsibilities from future application workloads by using dedicated management groups and subscriptions for Management, Connectivity, Identity, and Security.

The platform was deployed in Australia East using the Azure Landing Zones IaC Accelerator, Terraform, Azure Verified Modules, and Azure DevOps.

![Azure Landing Zone Platform Architecture](screenshots/alz-platform-architecture.png)

## Management Group Hierarchy

```text
Tenant Root Group
|
+-- Azure Landing Zones (alz)
    |
    +-- Platform
    |   +-- Management
    |   +-- Connectivity
    |   +-- Identity
    |   +-- Security
    |
    +-- Landing Zones
    |   +-- Corp
    |   +-- Online
    |   +-- Local
    |
    +-- Sandbox
    +-- Decommissioned
```

The hierarchy separates shared platform responsibilities from future workload landing zones and supports Azure Policy and RBAC inheritance through the management-group structure.

## Platform Subscriptions

### Management

The Management subscription hosts shared operational and deployment resources, including:

- Log Analytics workspace
- Data Collection Rules
- Azure Monitor Agent managed identity
- Terraform plan and apply managed identities
- Terraform remote state storage
- Service Health alerting resources

### Connectivity

The Connectivity subscription provides the shared networking foundation.

The current cost-optimized parked state retains:

- Hub virtual network
- Network Watcher
- Service Health alerting resources

The hub virtual network is deployed in Australia East with the address space:

```text
10.0.0.0/22
```

### Identity

The Identity subscription provides a dedicated platform boundary for identity-related governance and future shared identity services.

### Security

The Security subscription provides a dedicated platform boundary for security-related governance and future shared security services.

## Connectivity Design

The platform follows a hub-and-spoke networking model.

The Connectivity subscription owns the shared hub network. Future Application Landing Zones can introduce spoke virtual networks and workload platforms without recreating the platform foundation.

Several higher-cost connectivity services were deployed and validated during the project and later removed for lab cost optimisation:

- Azure Firewall Premium
- Azure Bastion
- DDoS Protection Plan
- VPN Gateway
- ExpressRoute Gateway
- Private DNS Resolver
- Private DNS zone catalogue
- Associated public IP resources

## Platform and Application Separation

The overall solution is intentionally divided into two phases.

### Phase 1 - Platform Landing Zone

Provides the shared Azure foundation:

- Management-group hierarchy
- Platform subscriptions
- Governance
- Shared management services
- Hub networking
- Terraform state
- Deployment identities
- Azure DevOps delivery controls

### Phase 2 - Application Landing Zone

Will consume the platform foundation and introduce workload-specific services such as:

- Spoke networking
- AKS
- Azure Container Registry
- Key Vault
- Workload identity
- Observability
- Application CI/CD and GitOps

This separation keeps platform governance independent from application lifecycle changes and provides a reusable foundation for future workloads.
