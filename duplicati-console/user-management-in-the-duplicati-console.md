---
description: >-
  How managed service providers and enterprises use user management to control
  access across organizations and sub-organizations.
---

# User management in the Duplicati Console

Managed service providers (MSPs) can manage multiple customer tenants by combining the [organization hierarchy](organizations-and-sub-organizations.md) with user and security group tooling. This article explains how to set up the hierarchy, invite users, and reuse security groups across sub-organizations while keeping access auditable.

{% hint style="info" %}
User management and security groups are an Enterprise feature. Contact sales or support if you need an Enterprise trial or license.
{% endhint %}

### Model the organization tree and licensing

* **Hierarchy** – Each root organization is a fully isolated entity and cannot share anything across the organization boundary. Sub-organizations are also default isolated from the parent organizations, but the license is inherited from the root organizations.
* **Security group linking** – Cross-organization features (invites and linking) can be performed top-down, so one or more groups can be defined at a parent organization and assigned to a child organization.

### Claims and security groups

* **Organization-scoped claims** – Users gain privileges through claims tied to a specific organization, including the Owner claim that represents tenant ownership.
* **Security groups as containers** – Groups hold owners and members. Owners control membership changes, and members receive the permissions assigned to the group within each organization where it is active.

In other words: owners of a group has the permissions to add/remove users from that group, but do not gain permissions to use the claims of the group. Members of a group gain the permissions the group entitles them to, but do not grant them access to add/remove members. It is possible for users to be both owner and member of a group.

### Inviting and onboarding users

* **Group-centric invites** – Organization owners can invite new people directly into a security group with a predefined role (owner or member). The inviter must own the group and the parent organization and must be covered by Enterprise licensing.
* **Invitation tracking** – The invite flow tracks pending invitations with their intended roles so onboarding is predictable.
* **Audit-ready roster** – Organization owners can list both active users and pending invitations, along with the security groups that grant their access, making it easy for MSP operators to review each tenant.

### Reusing security groups across sub-organizations

* **Link instead of duplicate** – MSP admins can link a security group from the parent organization into a child organization so the same owners and members manage resources across tenants without rebuilding group rosters.
* **Ownership and lineage checks** – Linking requires the caller to own both organizations and the source group. Links only work from a parent to one of its sub-organizations; horizontal links or self-links are blocked to preserve tenant boundaries.

### MSP operational playbook

1. **Create the hierarchy** – Establish the root MSP org and child customer orgs, ensuring the root carries Enterprise licensing when cross-tenant links are needed.
2. **Build security groups in the parent** – Add MSP administrators as owners and assign customer operators as members to reflect who should manage and who should operate.
3. **Link groups to sub-organizations** – Reuse the parent’s groups in each customer org that needs them, verifying each target is a true child organization.
4. **Invite customer users into the right groups** – Send invitations that place new users directly into the correct roles while honoring duplicate and cooldown protections.
5. **Review access regularly** – Use the organization roster view to confirm which users and pending invitations exist per tenant and which linked groups provide their access.

By following these steps, MSPs can scale consistent access controls across many tenants while keeping ownership clear and audit trails intact.
