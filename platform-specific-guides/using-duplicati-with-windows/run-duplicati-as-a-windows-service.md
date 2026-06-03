---
description: >-
  This page explains when you need to run Duplicati as a service and how to
  configure it
---

# Run Duplicati as a Windows Service

{% hint style="warning" %}
The setup described here was introduced in Canary 2.3.0.104 and is not yet available in a stable release
{% endhint %}

On Windows the operating system will lock files while they are in use. While there are advantages to this, it is problematic for a backup system because the files cannot be read by the backup tool either. To get access to locked files you need Administrator privileges, meaning you need to run Duplicati in a special way.&#x20;

Once way of running Duplicati with elevated privileges is to run it as a service. The benefit from this is that the backup runs even if no users are logged in to the machine.

When Duplicati runs as a Windows Service it will change the default to use the VSS service when running backup, such that locked files can be read.

{% hint style="danger" %}
When running Duplicati as a Windows Service this means Duplicati has full access to the machine, and you must be careful not to allow access to the service. Make sure that you do not store the password insecurely and do not enable remote access to Duplicati.
{% endhint %}

## Using the Windows Installer

When running the Windows Installer package, simply check the "Install Duplicati as a Windows Service" option:

<figure><img src="../../.gitbook/assets/Screenshot 2026-05-29 at 14.23.28.png" alt="" width="563"><figcaption></figcaption></figure>

After the installation has completed, you will get the option to copy the generated password by clicking "Copy password":

<figure><img src="../../.gitbook/assets/Screenshot 2026-05-28 at 12.35.42.png" alt="" width="563"><figcaption></figcaption></figure>

The installer will automatically configure the current user, such that it can connect securely to the service. If you need to let another user access the service, or need to use some of the command line tools, you can copy the password by clicking the "Copy password" button.

When you start Duplicati it will initially start in a disconnected state, and then connect to the server using the password stored in the Windows Credential Manager.

The password is only applied on the first install and will not be retrivable later, including if you uninstall Duplicati. See the [password reset section below](run-duplicati-as-a-windows-service.md#reset-password-for-windows-service) if you need to change the password or later want to use the password.

## Automated installation with a service

By default, Duplicati will install as a regular application and will be able to run as the logged in user. To configure the installer to install the service you need to pass options to `msiexec` explaining what you want. A few examples:

**Install Service + all shortcuts**

{% code overflow="wrap" %}
```
msiexec /i Duplicati.msi /qn ADDLOCAL=DuplicatiCore,DuplicatiServiceFeature,DuplicatiDesktopShortCutFeature,DuplicatiProgramMenuShortCutFeature,DuplicatiStartupShortCutFeature
```
{% endcode %}

**Install Service only and no shortcuts:**

{% code overflow="wrap" %}
```
msiexec /i Duplicati.msi /qn ADDLOCAL=DuplicatiCore,DuplicatiServiceFeature REMOVE=DuplicatiDesktopShortCutFeature,DuplicatiProgramMenuShortCutFeature,DuplicatiStartupShortCutFeature
```
{% endcode %}

**Install Service + only desktop shortcut**

{% code overflow="wrap" %}
```
msiexec /i Duplicati.msi /qn ADDLOCAL=DuplicatiCore,DuplicatiServiceFeature,DuplicatiDesktopShortCutFeature REMOVE=DuplicatiProgramMenuShortCutFeature,DuplicatiStartupShortCutFeature
```
{% endcode %}

In the examples, the `/qn` switch toggles unattended install which means no user interface will be shown. It also means that all actions will run in the installer context so the local user will not be configured to access Duplicati.

You can provide the desired password to the installer as an option:

{% code overflow="wrap" %}
```
msiexec /i Duplicati.msi DUPLICATI_SERVICE_PASSWORD=<your-password>
```
{% endcode %}

Note that providing the password to the installer may cause it to be logged. If you do not provide a password for unattended installs, Duplicati will default to writing a login link to the Windows Event log that can be used to log in. See also the [section on the access password](../../detailed-descriptions/configuration-and-management/duplicati-access-password.md) for other ways to manage the password.

You will need the password to access the UI and to allow the TrayIcon to connect to the service.

## Installing a [preload.json](../../detailed-descriptions/configuration-and-management/preload-settings.md) file

If you want to configure the installation, you can place a `preload.json` file in the same folder where the MSI is placed, and then launch with the option `INSTALL_PRELOAD=true` to have the installer pick up the `preload.json` file and install it along with the rest of the files:

{% code overflow="wrap" %}
```
msiexec /i Duplicati.msi INSTALL_PRELOAD=true
```
{% endcode %}

Without the option, the preload file is not installed even if it is found. This design prevents situations where a malicious file is placed in the source folder and unexpectedly configures the application.

## Reset password for Windows Service

The [`WindowsService.exe`](../../duplicati-programs/service.md) program supports the command `reset-password` which will set a password and then restart the service. This provides a secure way to set a new password without risking that the password is shown in logs. If Duplicati was installed with a silent install, this option allows you to change the password via a command line operation:

{% code overflow="wrap" %}
```
"C:\Program Files\Duplicati 2\Duplicati.WindowsService.exe" reset-password
```
{% endcode %}

The program will ask you to enter a password or offer to generate one. You can also see the [section about access password](../../detailed-descriptions/configuration-and-management/duplicati-access-password.md) for more ways to set the password.

If you are using the [TrayIcon](../../duplicati-programs/trayicon.md) to connect to the service, right-click the TrayIcon and choose "Change password", then enter the updated password.

Manual install

If you need additional customization beyond what the MSI package offers, see the [`WindowsService.exe`](../../duplicati-programs/service.md) page for details on how to configure the service from a commandline or scripts.
