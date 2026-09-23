# Governance and Security

## Overview

Governance is applied through the Azure Landing Zone management-group hierarchy so that policies and access controls can be inherited consistently across platform and future workload subscriptions.

The design separates shared platform governance from application-specific resources and provides a controlled foundation for future landing zones.

## Management Group Governance

The Azure Landing Zone hierarchy provides the governance boundary for the platform.

Key management groups include:

- Platform
- Management
- Connectivity
- Identity
- Security
- Landing Zones
- Corp
- Online
- Local
- Sandbox
- Decommissioned

Policies assigned at higher levels of the hierarchy can be inherited by child management groups and subscriptions.

![Management Group Hierarchy](screenshots/management-group-hierarchy.png)

## Azure Policy

Azure Policy is used to apply governance controls across the management-group hierarchy.

During the project, policy behavior was also validated operationally when certain platform resources were removed for cost optimisation.

This included reviewing and adjusting policy assignments related to:

- DDoS protection
- Private DNS deployment
- Connectivity governance

This demonstrated the importance of understanding policy inheritance and remediation behavior before making infrastructure changes.

## Azure RBAC

Azure RBAC is used to control access to platform resources and deployment operations.

The platform uses separate managed identities for Terraform plan and Terraform apply activities.

This provides separation between read/validation operations and infrastructure deployment operations.

## Deployment Identity

Azure DevOps authenticates to Azure using workload identity federation / OIDC.

This avoids the use of long-lived client secrets for the platform deployment workflow.

Key controls include:

- Federated authentication
- Separate plan and apply identities
- Scoped Azure RBAC
- Controlled deployment through Azure DevOps pipelines
- Manual approval before Terraform apply

## Governance Through Source Control

Infrastructure changes are managed through Azure Repos.

The `main` branch is protected through pull request and validation controls.

Changes follow a controlled workflow:

```text
Feature Branch
      |
      v
Pull Request
      |
      v
Terraform Validation
      |
      v
Merge to Main
      |
      v
Controlled Deployment
```

This creates an auditable path between infrastructure configuration, review, approval, and deployment.

## Security Boundary

Dedicated subscriptions provide separation between major platform responsibilities:

- Management
- Connectivity
- Identity
- Security

This design allows governance and security controls to evolve independently while keeping shared platform services separate from future application workloads.

## Governance Outcome

The resulting platform demonstrates:

- Hierarchical Azure governance
- Azure Policy inheritance
- Azure RBAC-based access control
- Separate deployment identities
- Federated pipeline authentication
- Pull request-based infrastructure change control
- Manual deployment approval
- Clear separation between platform and workload responsibilities
