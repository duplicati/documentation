---
description: >-
  Guidance for MSPs and Enterprises using organizations and sub-organizations in
  the Duplicati console
---

# Organization management

{% hint style="info" %}
Organizations in the Duplicati console are similar to tenants found in other systems.
{% endhint %}

Managed service providers (MSPs) can use organizations and sub-organizations in the Duplicati console to isolate tenants, delegate access, and safely offboard customers. The console enforces depth and licensing limits so MSP operators maintain strong governance while working across multiple customer environments.

The description below is targeted MSPs but can also be applied to larger organizations, where there is a need to isolate different areas, for example geographically or by organizational structures.

### Hierarchy model

* **Root organization**: Created without a parent. MSP staff typically sign in to the MSP root and operate downstream organizations from that context.
* **Sub-organization**: Created under a parent organization to represent a customer or site. Each sub-organization handles references to the root and parent organizations. The hierarchy is currently limited to **three levels** to keep trees manageable.
* **Detached organization**: Created without a parent or shared root to start a completely new tree. MSPs can use this when a customer must live in an isolated tenant before being linked to a broader tree. Note that each root organization needs its own payment configuration.

{% hint style="info" %}
Creating and managing organizations is an Enterprise feature. Contact Duplicati sales or support if you need an Enterprise license or trial.
{% endhint %}

### Creating organizations from the console

1. **Ensure ownership and licensing**: The signed-in operator must be an **owner** of the current organization. Only organizations with Enterprise-level subscriptions can create sub-organizations, so confirm the MSP root has the right plan before adding customers.&#x20;
2. **Choose hierarchy placement**:
   * To add a customer under the MSP tree, create a new organization while signed in to the MSP root (or an existing parent).&#x20;
   * The organization can be created under the current organization, so make sure you have switched to the desired parent organization before creating it.
   * To start a fresh tenant tree, select the option to create a **detached organization** so it uses its own root.
3. **Name and confirm**: Provide the customer name and confirm creation. The console records the parent/root identifiers automatically and returns you to the organization list. The screen for creating an organization looks like this:

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-12-02 at 11.33.03.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-12-02 at 11.32.45.png" alt="Image showing the create organization dialog"></picture><figcaption></figcaption></figure>

### Delegating access across the hierarchy

* Security groups can be linked **only downward** from a parent organization to its descendants. To link a group, you must own the source organization, the target organization, and the security group itself.
* This model lets MSPs define shared automation or operator roles in the MSP root and selectively expose them to customer organizations without risking cross-tenant access.

An example setup for a multi-level enterprise organization could look like this:

<figure><picture><source srcset="../.gitbook/assets/Screenshot 2025-12-02 at 14.15.33.png" media="(prefers-color-scheme: dark)"><img src="../.gitbook/assets/Screenshot 2025-12-02 at 14.15.44.png" alt="Image showing an example 3-level organization structure"></picture><figcaption></figcaption></figure>

### Deleting organizations safely

1. **Select the target**: From the organizations list, pick the customer organization you need to remove. You cannot delete the organization you are currently operating from, and you must be an owner of the target organization.
2. **Clear blockers**: Deletion is blocked while the organization still has active subscriptions, child organizations, or customer resources (backup reports, connected agents, registered machines, client encryption keys, connection strings, or client backup configurations). Remove or migrate these items first.
3. **Confirm removal**: Once prerequisites are cleared, run the delete action. The console revokes accounts and other items that are not directly user managed.

### MSP best practices

1. **Add customers as sub-organizations**: Create new orgs without the Detached flag so each customer anchors to the MSP root for consistent reporting and licensing checks.
2. **Delegate access with intent**: Link MSP-owned security groups down to specific customer orgs that require shared operational roles or automation.
3. **Offboard cleanly**: Before deleting a customer org, clear active subscriptions and resources, then delete to revoke linked access keys automatically.
