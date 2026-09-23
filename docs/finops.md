# FinOps and Cost Optimisation

## Overview

This platform was designed as a hands-on engineering lab, so cost management became an important operational requirement after the initial Azure Landing Zone deployment.

The project initially deployed a broader set of shared connectivity services to validate the architecture and deployment patterns. After validation, high-cost resources were removed or disabled to reduce ongoing spend while preserving the core platform foundation.

## Cost Optimisation Actions

The following higher-cost services were deployed and validated, then removed or disabled for the parked lab state:

- Azure Firewall Premium
- Azure Bastion
- DDoS Protection Plan
- VPN Gateway
- ExpressRoute Gateway
- Private DNS Resolver
- Private DNS zone catalogue
- Associated public IP resources

The retained platform continues to provide:

- Management-group hierarchy
- Dedicated platform subscriptions
- Governance and policy structure
- Centralized management resources
- Terraform state storage
- Terraform deployment identities
- Log Analytics and monitoring foundation
- Hub virtual network
- Network Watcher
- Service Health alerting resources

## Policy-Aware Cost Reduction

Resource removal required more than simply deleting infrastructure.

Azure Policy assignments were reviewed because some policies could recreate or reattach resources after manual removal.

The project included corrective changes for:

- DDoS protection policy behavior
- Private DNS deployment policy behavior
- Related role assignments

This ensured that the final Terraform configuration, policy state, and deployed resources remained aligned.

## Final Connectivity State

The final Connectivity subscription retains the shared hub networking foundation without the higher-cost services that were not required for the parked lab state.

![Connectivity Subscription Final Resources](screenshots/connectivity-subscription-final-resources.png)

## Final Management State

The Management subscription retains centralized monitoring, Terraform state, deployment identities, and management resources.

![Management Subscription Final Resources](screenshots/management-subscription-final-resources.png)

## FinOps Outcome

The cost-optimised parked state preserves the architecture, governance, CI/CD workflow, monitoring foundation, and reusable platform structure while significantly reducing ongoing lab cost.

This approach allows future Application Landing Zone work to continue without rebuilding the entire platform foundation.
