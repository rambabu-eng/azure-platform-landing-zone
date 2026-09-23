# Troubleshooting and Engineering Lessons

## Overview

This document captures the main implementation issues encountered during the Azure Landing Zone platform build and the engineering lessons learned from resolving them.

The purpose is to separate troubleshooting history from the main project documentation while preserving the practical operational knowledge gained during the lab.

## 1. Root Management Group Configuration

### Issue

A management group named `alz` was created manually before running the Azure Landing Zones IaC Accelerator.

The Accelerator expected to create and manage its own Azure Landing Zone hierarchy, which caused a conflict with the manually created management group.

### Resolution

The manually created `alz` management group was removed.

The Accelerator configuration was corrected so the tenant root management group was used as the parent:

```text
root_parent_management_group_id = tenant root management group
```

The Accelerator was then allowed to create the Azure Landing Zone hierarchy itself.

### Lesson

When using the Azure Landing Zones IaC Accelerator, avoid manually creating framework resources that the Accelerator is designed to manage.

## 2. Bootstrap and Branch Policy Conflict

### Issue

Azure DevOps branch policies initially prevented the Accelerator bootstrap process from pushing required changes directly to the generated repository.

### Resolution

Branch-policy bypass permission was temporarily allowed for the bootstrap operation.

After the bootstrap completed successfully, the bypass permission was removed and normal pull request controls were restored.

### Lesson

Bootstrap automation may require temporary elevated repository permissions, but those permissions should be removed after initialization.

## 3. DDoS Protection Policy Reattachment

### Issue

The DDoS Protection Plan was manually removed during cost reduction, but an Azure Policy assignment attempted to reattach DDoS protection to the hub virtual network.

### Root Cause

The Azure Landing Zone policy configuration still contained the DDoS deployment policy.

Terraform configuration and manual resource changes were therefore not aligned with the active governance policy.

### Resolution

The policy behavior was temporarily disabled while the network was detached from the DDoS plan.

The Microsoft-generated Azure Landing Zone policy override configuration was then updated so DDoS enforcement was removed from the cost-optimized lab configuration.

### Lesson

Azure Policy must be considered part of the infrastructure lifecycle. Removing a resource without reviewing related policy assignments can result in automatic remediation or resource reattachment.

## 4. Private DNS Policy and Role Assignment Drift

### Issue

After Private DNS resources were removed, Terraform apply continued to encounter stale role assignments and policy dependencies.

### Root Cause

The Azure Landing Zone configuration still included Private DNS deployment policy behavior even though the related DNS resources had been removed.

### Resolution

The Microsoft-generated policy override configuration was updated to remove the Private DNS deployment policy from the applicable landing-zone configuration.

Terraform then planned the remaining policy and role-assignment removals and successfully reconciled the environment.

### Lesson

When shared platform services are removed, related policy assignments, identities, and role assignments must also be reviewed.

## 5. Emergency Cost Reduction

### Issue

The initial landing-zone deployment included several production-style networking services that generated significant lab cost.

These included:

- Azure Firewall Premium
- Azure Bastion
- DDoS Protection Plan
- VPN Gateway
- ExpressRoute Gateway
- Private DNS Resolver
- Private DNS zone catalogue
- Associated public IP resources

### Resolution

The higher-cost services were removed or disabled after their deployment patterns had been validated.

The core platform foundation was retained, including governance, monitoring, Terraform state, deployment identities, and the hub virtual network.

### Lesson

Enterprise reference architectures often include services that are appropriate for production but expensive for personal labs.

A lab implementation should validate the design while maintaining clear cost controls and a planned parked state.

## 6. Terraform Reconciliation After Manual Changes

### Issue

Some resources were removed manually during urgent cost reduction, creating temporary drift between Azure, Terraform state, and repository configuration.

### Resolution

The configuration and policy overrides were updated through the normal branch and pull request workflow.

The environment was then reconciled until the final Terraform plan returned:

```text
No changes. Your infrastructure matches the configuration.
```

The final apply confirmed:

```text
Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
```

### Lesson

Manual intervention may occasionally be necessary, but infrastructure should ultimately be brought back under Infrastructure-as-Code control and reconciled to a clean state.

## 7. Git and Documentation Formatting

### Issue

During documentation work, line endings, trailing whitespace, and pasted Markdown formatting created several small consistency issues.

### Resolution

`git diff --check` was used repeatedly to detect whitespace problems before commits.

File endings were normalized and screenshot filenames were standardized using lowercase hyphenated naming.

### Lesson

Documentation quality is part of engineering quality. Small Git and Markdown checks help keep repositories clean and professional.

## Key Engineering Takeaways

The project reinforced several practical platform-engineering principles:

- Understand the automation framework before manually creating shared resources
- Treat Azure Policy as an active operational control, not just static configuration
- Keep Terraform configuration, Azure resources, and policy state aligned
- Use pull requests and validation for infrastructure changes
- Use workload identity federation instead of long-lived deployment secrets
- Separate validation of enterprise patterns from ongoing lab operating cost
- Reconcile manual changes back into Infrastructure as Code
- Maintain clean documentation and evidence alongside the technical implementation
