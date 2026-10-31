---
description: >-
  This page describes how to use restore test policies in the Duplicati Console
  to decide how often backups must pass a restore test, get an alert when a
  backup falls behind, and start restore tests from the console.
---

# Restore test policies

A successful backup shows that the files were uploaded, but the only way to know that they can be restored is to restore them. A **restore test** restores a sample of a backup from the remote destination to a temporary location on the machine, and checks that every restored file matches the backup.

A **restore test policy** is a rule that decides how often a backup must pass a restore test, and what happens when it does not. With a policy, the Duplicati Console can:

- Show which backups are **overdue** for a restore test.
- Raise an **alert** when a backup goes longer than the interval without a passing test.
- Have the machines **run the test after a backup** once the interval has passed.
- **Start a test** when a machine with an overdue backup connects to the console.

Policies are found in the Duplicati Console under **Alert Center → Settings → Restore tests**. There are no default policies, so backups are not checked until a policy is created.

{% hint style="info" %}
Restore tests are run by the Duplicati client, and starting them from the console requires Duplicati 2.4.0.105 or newer. On older versions the machine still reports its backups, but the console cannot start a test, and the interval from a policy has no effect.
{% endhint %}

## How a policy tracks a backup

The console keeps track of each backup it receives reports from, and each backup has a **due date** for the next passing restore test:

- If the backup has passed a restore test, it is due one interval after the last passing test.
- If the backup has never passed a restore test, it is due one interval after the console first saw the backup, or one interval after the policy was created if that is later. A new policy therefore gives untested backups a full interval before they are overdue.
- If the policy has [Send interval to clients](#send-interval-to-clients) enabled, the backup is due one day later, as the client may have to wait for the next backup to run the test.

The due date is calculated from the policy, so it changes right away when a policy is created, changed or removed. The console checks the due dates once per hour, and a backup shows as **overdue** from the first check after the due date. A backup that has not reported for its interval plus 7 days is most likely removed or disabled, so it is not shown as overdue until it reports again.

Only a passing test moves the due date:

| Result  | Meaning                                                                               | Moves the due date |
| ------- | ------------------------------------------------------------------------------------- | ------------------ |
| Passed  | Every restored file matched the backup                                                | Yes                |
| Warning | The test completed, but files were skipped or the source has changed since the backup | Yes                |
| Failed  | The sample could not be restored, or restored files did not match the backup          | No                 |

A restore test does not change the result of the backup it ran after, so a failed test shows up as a failed restore test and not as a failed backup.

{% hint style="warning" %}
A new policy only gives a full interval to backups that have never passed a test. A backup with a passing test from longer ago than the interval is overdue as soon as the policy is created, and raises an alert if the policy has **Alert when overdue** enabled.
{% endhint %}

Sync jobs copy a backup to another location and have nothing to restore, so they are not tracked. Test the backup that the sync job copies instead.

## Create a policy

You need to be an owner of the organization to create, change or delete policies. Other members can see the policies and the results.

1. Go to **Alert Center → Settings**.
2. In the **Restore tests** section, click **Create policy**.
3. Fill in the settings described below.
4. Click **Create policy**.

| Setting                   | Default | Description                                                                                                          |
| ------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------- |
| Policy name               |         | A name that identifies the policy. The name is used in the alerts.                                                   |
| Enabled                   | On      | A disabled policy is kept, but it is not applied.                                                                    |
| Test every                | 7 Days  | How long a backup may go without a passing restore test, in hours, days or weeks. The shortest interval is one hour. |
| Alert when overdue        | On      | See [Alert when overdue](#alert-when-overdue).                                                                       |
| Run on connect            | Off     | See [Run on connect](#run-on-connect).                                                                               |
| Send interval to clients  | On      | See [Send interval to clients](#send-interval-to-clients).                                                           |
| Limit to selected backups | Off     | Without limits the policy applies to all backups. See [Choose the backups](#choose-the-backups).                     |

The list of policies shows the interval, whether the policy applies to **All backups** or is **Filtered**, and which of the three actions are enabled. Use the buttons at the end of each row to edit or delete a policy.

A policy applies to the organization it was created in. It does not apply to the sub-organizations, which need their own policies.

## Choose the backups

A policy applies to all backups unless you enable **Limit to selected backups**, which shows the following filters:

| Filter               | Selects                                              |
| -------------------- | ---------------------------------------------------- |
| Machines             | All backups on the chosen machines                   |
| Backups              | The chosen backups                                   |
| Machine name pattern | All backups on machines with a name that matches     |
| Backup name pattern  | All backups with a name that matches, on any machine |

A pattern must match the whole name, and it is case sensitive. Use `*` for any number of characters and `?` for a single character, and separate several patterns with commas, for example `Office-*,Laptop-*`.

A backup is selected if **any** of the filters match, so the filters widen the selection rather than narrow it. A policy with the machine name pattern `Office-*` and the backup name pattern `*Documents*` applies to all backups on the office machines, and to the backups with `Documents` in the name on all other machines.

### When more than one policy matches

Each backup uses a single policy, which is picked in this order:

1. The oldest enabled policy with filters that match the backup.
2. If none match, the oldest enabled policy that applies to all backups.
3. If there is no such policy, the backup is not tracked and has no due date.

The list shows the policies oldest first, which is the order they are tried in. A policy with a filter that is just `*` counts as a policy for all backups.

This makes it possible to set a general rule with a policy for all backups, and use filtered policies for the backups that need something else, regardless of the order they were created in. Disabling or deleting a policy moves its backups to the next policy that matches.

## Alert when overdue

With **Alert when overdue** enabled, an alert is raised when a backup becomes overdue, which can be up to an hour after the due date.

- The alert is raised once for each due date. A passing test moves the due date, and the backup is alerted on again if it falls behind later.
- A change that moves the due date, such as a new interval, also makes the backup eligible for a new alert.
- Alerts require an active subscription. In an organization without one, the alert is held back and raised when the organization gets a subscription.

The alert shows up under **Alert Center → Alerts** and links to the backup. To also get the alert on a channel, such as email or Slack, add **Restore Test Overdue** to the subscribed events of the channel under **Alert Center → Settings → Channels**. The events **Restore Test Passed**, **Restore Test Warning** and **Restore Test Failed** are also available, and are sent for each restore test regardless of the policies.

{% hint style="info" %}
A failed restore test does not raise the overdue alert until the due date is reached. Subscribe to **Restore Test Failed** to be told about the failure right away.
{% endhint %}

## Send interval to clients

With **Send interval to clients** enabled, the machines are told to run a restore test as the last step of a backup when the interval has passed since the previous test. This is the same as setting the `--perform-restore-test-after` option on the backups. The interval is updated on the machines when a policy changes, and when a machine connects.

- A test only runs after a backup, so a machine that does not run backups does not run tests.
- If the option is already set in the client, either on the backup or in the global settings, the client keeps its own value.
- A backup that the console has not seen yet gets the interval from the policy that applies to the machine, or the policy for all backups. A policy that selects backups by ID or name takes effect once the backup has reported.

{% hint style="warning" %}
The client runs the test after the first backup that finishes when the interval has passed, so if the interval matches the backup schedule, the test can run one backup later than expected. The extra day on the due date covers this for backups that run at least once per day.

For backups that run less often, such as a weekly backup with a policy of 7 days, the test can still run after the due date. To avoid alerts in this case, set `--perform-restore-test-after` on the backup in the client to a shorter interval than the policy, for example 6 days for a policy of 7 days.
{% endhint %}

## Run on connect

With **Run on connect** enabled, the console starts a restore test about 30 seconds after a machine with an overdue backup connects to the console. Machines that stay connected are checked once per hour, so they start the test when a backup becomes overdue.

- The console waits 24 hours before asking again for the same backup.
- The machine needs a Duplicati version that supports restore tests, and it refuses the test if control from the console is disabled on the machine.

This setting is useful for laptops and other machines that are not always online, as the test is started as soon as the machine is available.

## Run a restore test manually

You can start a restore test from the console at any time, regardless of the policies. This requires that you are an owner of the organization, that the machine is connected to the console with control from the console enabled, and that it runs a Duplicati version that supports restore tests.

- To test all backups on a machine, open the menu for the machine in the machine list and click **Run Restore Test**.
- To test a single backup, open the machine and click the restore test button on the row of the backup.

The test is queued on the machine, and it runs when the machine has finished what it is currently doing. The result shows up when the machine reports it. If a test is already queued or running for the backup, no new test is added.

## See the results

The restore test status is shown in these places:

- **Machine list**: Add the **Last Restore Test** column to show the newest test on each machine, or the number of overdue backups if there are any.
- **Machine details**: The **Last restore test** column shows the time of the last test for each backup, along with a chip that says **Overdue**, **Failed**, **Warning** or **Passed**. Hover over the time to see the next due date.
- **Backup page**: The **Restore tests** tab lists all tests of the backup, and the **Open report** button shows the details.
- **Backup report**: A test that ran after a backup is also shown on the **Restore test** tab of the report for that backup.

The report shows how the sample was chosen, which backup version was tested, how many files passed, failed or were skipped, how much data was restored and downloaded, and any errors and warnings. It also lists the files that failed, along with the problem:

| Problem                          | Meaning                                               |
| -------------------------------- | ----------------------------------------------------- |
| Content differs                  | The restored file does not have the expected content  |
| Size differs                     | The restored file does not have the expected size     |
| Remote volume missing or damaged | A file needed for the restore is missing or damaged   |
| Could not be restored            | The restore of the file failed                        |
| Metadata differs                 | The restored file does not have the expected metadata |

A test marked **Stopped early** reached a time or download limit, and did not verify all the files in the sample. If a machine uses [reduced reporting](../duplicati-programs/agent.md), its reports do not include file paths.

## Configure the restore test

A policy decides when a backup is tested, but what is tested is decided by the client. By default, a test restores 10 random files from the newest version. To change this, set the following advanced options on the backup in the client:

| Option                             | Default       | Description                                                                                               |
| ---------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| `--restore-test-mode`              | `RandomFiles` | How the sample is chosen: `RandomFiles`, `RandomSize`, `Full` or `Rolling`                                |
| `--restore-test-sample-count`      | `10`          | The number of files to test with `RandomFiles` and `Rolling`                                              |
| `--restore-test-sample-percent`    | `5`           | The percentage of the backup size to test with `RandomSize`                                               |
| `--restore-test-rolling-window`    | `30D`         | The period in which all files should be tested with `Rolling`                                             |
| `--restore-test-max-download-size` | Unlimited     | Stop the test if it needs to download more than this                                                      |
| `--restore-test-max-runtime`       | Unlimited     | Stop the test if it runs for longer than this                                                             |
| `--restore-test-temp-path`         | Temp folder   | The folder for the restored files, which needs enough free space for the sample                           |
| `--restore-test-recreate-database` | `false`       | Rebuild a temporary database from the remote destination, so the test does not rely on the local database |
| `--restore-test-compare-source`    | `false`       | Also compare the restored files with the source files, and report differences as warnings                 |

A restore test downloads data from the remote destination, which can take time and can cost money on some storage providers. Keep this in mind when you choose the interval and the size of the sample.
