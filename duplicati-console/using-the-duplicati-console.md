---
description: >-
  A comprehensive guide to using the Duplicati Console for centralized backup
  management and monitoring across multiple machines and organizations.
---

# Using the Duplicati Console

The Duplicati Console is a centralized management interface designed to streamline the monitoring and configuration of backups across multiple machines and organizations. Whether you are managing a handful of devices or hundreds of machines across multiple organizations, the console provides a comprehensive set of tools for ensuring data safety and operational efficiency.

{% hint style="info" %}
The Duplicati Console is available at [https://app.duplicati.com](https://app.duplicati.com). Some features require an Enterprise or Pro license.
{% endhint %}

## Free Pro Trial

When you connect your first machine to the Duplicati Console, you automatically receive a **free 30-day Pro trial**. This trial gives you access to advanced features including:

- **Alert Center** – Set up proactive monitoring rules and notifications
- **Advanced Reporting** – Detailed backup reports and analytics
- **AI analysis** - Get an AI expert to analyse your backup results

The trial begins as soon as a machine successfully connects to your console account. No credit card is required to start the trial. After the 30-day period, you can choose to upgrade to a Pro or Enterprise plan to continue using these features, or continue with the limited free tier which includes basic monitoring and management capabilities.

## Getting Started with the Console

### Accessing the Console

Navigate to [https://app.duplicati.com](https://app.duplicati.com) and sign in with your Duplicati account. Once logged in, you will be presented with the main dashboard showing an overview of your organizations and connected machines.

### Connecting Your First Machine

Before you can manage backups through the console, you need to connect machines to your account. There are two primary methods:

1. **Using the TrayIcon or Server** – For existing Duplicati installations, enable remote management from the local settings page. See [Using remote management](../detailed-descriptions/using-remote-management/) for detailed instructions.

2. **Using the Agent** – For new installations, deploy the [Duplicati Agent](../duplicati-programs/agent.md) which is designed for headless environments and easier mass deployment. The Agent can be pre-configured with a registration link for seamless onboarding.

## Monitoring at Scale

The Duplicati Console excels at providing visibility across large installations. Instead of checking each machine individually, you can monitor all your backups from a single interface.

### Machine Dashboard

The **Machine Dashboard** serves as your command center for all connected devices:

- **Overview** – View a complete list of all machines associated with your organization, including their current connection status.
- **Status Indicators** – Quickly identify which machines are online, offline, or experiencing issues.
- **Registration Links** – Manage pre-authorized registration tokens for onboarding new machines at scale.
- **Machine Tags** – Apply custom tags to machines for better organization and filtering.

For MSPs and enterprises managing many devices, the ability to see all machines at a glance eliminates the need to log into each device individually.

### Backup Dashboard

The **Backup Dashboard** provides a centralized view of all backup operations across your entire fleet:

- **Global View** – Monitor backup job statuses across all machines in a single view.
- **Storage Usage** – Track total storage consumption across all backups, helping you plan capacity needs.
- **Visual Status Indicators** – Color-coded indicators show backup results at a glance:
  - **Green (Success)** – Backup completed successfully.
  - **Yellow (Warning)** – Backup completed with warnings that may need attention.
  - **Red (Error/Fatal)** – Backup failed or encountered critical errors requiring immediate action.

### Alert Center (Pro Feature)

The **Alert Center** enables proactive monitoring through customizable rules and multi-channel notifications:

#### Monitoring Rules

Create custom profiles to detect abnormalities before they become problems:

- **Missed Backups** – Alert when a backup hasn't run by an expected time, ensuring no machine falls through the cracks.
- **Modified Files Threshold** – Alert when the number of modified files exceeds a defined threshold, potentially indicating unusual activity.
- **Duration Monitoring** – Track backup duration deviations that might indicate performance issues or network problems.

#### Notification Channels

Configure multiple channels to receive alerts where your team will see them:

- **Email** – Send alerts to validated email addresses.
- **Discord** – Post alerts to a Discord channel.
- **Slack** – Send notifications to a Slack workspace.
- **Webhooks** – Integrate with custom endpoints or other services for advanced automation.

## Managing Backups Centrally

### Creating Backup Configurations

The console allows you to create backup configurations that can be applied to multiple machines, ensuring consistency across your organization:

#### Source Configuration

- **Custom Paths** – Specify exact file or folder paths to back up.
- **Filters** – Add inclusion or exclusion filters based on file patterns.
- **Built-in Exclusions** – Toggle options to exclude Hidden, System, and Temporary files, or files larger than a specific size.

#### Destination Support

The console supports the full range of Duplicati storage providers:

- **Local Storage** – File System backups.
- **Cloud Object Storage** – S3 Compatible (AWS, MinIO), Azure Blob Storage, Google Cloud Storage.
- **Cloud Drives** – Google Drive, OneDrive (Personal & Business), pCloud, Microsoft SharePoint.
- **Network Storage** – SSH/SFTP.

#### Scheduling and Encryption

- **Automatic Scheduling** – Set backups to run automatically at specific times with configurable frequency (daily, weekly, or custom intervals).
- **Manual Triggers** – Option to trigger backups manually when needed.
- **Encryption Management** – Manage encryption keys centrally to ensure data security across all backups.

### Applying Configurations to Machines

Once a backup configuration is created, it can be deployed to any connected machine in your organization. This ensures that all machines follow the same backup policy without requiring manual configuration on each device.

## Organization Management

For MSPs and large enterprises, the console provides powerful multi-organization capabilities:

### Multi-Organization Support

- **Organization Hierarchy** – Create and manage multiple organizations from a single account, with support for up to three levels of hierarchy.
- **Context Switching** – Seamlessly toggle between different organizations to manage their respective resources.
- **Tenant Isolation** – Each organization is isolated, ensuring data and access boundaries between customers or departments.

See [Organization management](organizations-and-sub-organizations.md) for detailed information on setting up hierarchies.

### User and Team Management

- **User Invitation** – Invite new members to your organization via email with predefined roles.
- **Teams** – Group users into teams for easier permission management.
- **Access Control** – Granularly assign access rights to specific machines or backup sets for different teams.

See [User management in the Duplicati Console](user-management-in-the-duplicati-console.md) for more details.

## Machine Management at Scale

### Claiming Machines

Securely add new machines to your organization using unique registration tokens. This process ensures that only authorized machines can connect to your console.

### Registration Links

Generate pre-configured links to easily onboard clients or new devices:

- **Pre-authorized URLs** – Create links that allow machines to register automatically without manual approval.
- **Mass Deployment** – Use registration links with deployment tools or preload configurations for unattended installations.
- **Security** – Revoke links at any time to prevent unauthorized registrations.

### Remote Access

Once a machine is connected, you can access its local Duplicati interface directly from the console:

1. Navigate to **Settings → Registered Machines**.
2. Find the machine you want to access.
3. Click **Connect** to open the machine's local interface.

This eliminates the need for VPNs or remote desktop connections to manage individual machines.

## Best Practices for Large Installations

### 1. Organize with Tags

Apply meaningful tags to machines (e.g., by location, department, or customer) to make filtering and management easier.

### 2. Use Configuration Templates

Create standardized backup configurations for different use cases (e.g., "Workstation Backup", "Server Backup") and apply them consistently.

### 3. Set Up Proactive Monitoring

Configure Alert Center rules to catch issues early:

- Set up missed backup alerts for critical machines.
- Monitor for unusual file modification patterns.
- Track backup duration trends to identify performance degradation.

### 4. Leverage the Organization Hierarchy

For MSPs, use the three-level hierarchy to model your customer structure:

- Root organization for MSP staff
- Sub-organizations for each customer
- Further sub-organizations for customer departments or sites

### 5. Automate Onboarding

Use the Client or Agent with pre-authorized registration links and [preload configurations](../detailed-descriptions/preload-settings.md) for seamless mass deployment. The `preload.json` file can be downloaded directly from the Links page in the console and should be placed into the installation folder. When the Agent starts, it will automatically register with the console and any configured backups will be pushed to the agent.

## Summary

The Duplicati Console transforms backup management from a machine-by-machine task into a centralized, scalable operation. By providing a unified view of all backups, proactive monitoring capabilities, and powerful organizational tools, the console makes it easy to manage and monitor large installations while maintaining security and compliance.

Whether you are an MSP managing hundreds of customer devices or an enterprise IT team overseeing a distributed workforce, the Duplicati Console provides the visibility and control needed to ensure your data protection strategy succeeds.
