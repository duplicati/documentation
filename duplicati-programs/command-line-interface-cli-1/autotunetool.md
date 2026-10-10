---
description: This page describes the AutoTuneTool for finding restore concurrency settings
---

# AutoTuneTool

The AutoTuneTool measures how fast a restore runs with different concurrency settings on the current machine, and reports the combination that performed best. It was added in canary 2.3.0.104 and is included from stable 2.4.0.0.

The binary is called `Duplicati.CommandLine.AutoTuneTool.exe` on Windows.

{% hint style="warning" %}
The tool makes a backup and then restores it many times. This puts a lot of load on the source, the destination and the restore target, so do not point it at systems that are in production use.
{% endhint %}

## What is tuned

The tool varies the four restore concurrency options:

* `--restore-file-processors`
* `--restore-volume-decompressors`
* `--restore-volume-decryptors`
* `--restore-volume-downloaders`

For each candidate combination it runs the restore a number of times (`--runs`, default `3`, after `--warmup` runs, default `1`) and uses the mean time. The best combination found is compared with a baseline, which is the Duplicati defaults unless `--baseline-params` is given.

## Running the tool

With no options, the tool generates test data in a temporary folder, backs it up to a temporary folder, and restores it to a temporary folder:

```
Duplicati.CommandLine.AutoTuneTool.exe
```

To measure a realistic setup, point it at your own data, destination and restore folder:

```
Duplicati.CommandLine.AutoTuneTool.exe
  --source-folder=<folder with test data>
  --destination=<empty destination url or folder>
  --restoretarget=<empty folder>
```

The destination must be empty, and the data written there is deleted again when the tool finishes. The restore target must also be empty, as it is emptied between measurements. Options for the destination, such as credentials, are passed with `--backend-options key1=value1 key2=value2`.

## Options

| Option | Description |
| --- | --- |
| `--source-folder` | Folder to back up. If it is empty, test data is generated in it. |
| `--destination` | Destination for the test backup. Must be empty. |
| `--restoretarget` | Folder to restore to. Must be empty. |
| `--temp-folder` | Where temporary folders are created. Defaults to the system temporary folder. |
| `--backend-options` | Options passed to the destination, as `key=value` pairs. |
| `--runs` | Number of measured runs per candidate. Default `3`. |
| `--warmup` | Number of warmup runs before measuring. Default `1`. |
| `--starting-steps` | Starting value for the four parameters, either one value for all or four values. Default `1`. |
| `--default-settings` | Start from the Duplicati defaults instead of `1`. Ignored if `--starting-steps` is given. |
| `--baseline-params` | Values to compare the result against, either one value for all or four values. Default: the Duplicati defaults. |
| `--exponential-steps` | Double the value for the next candidate instead of adding one. Converges faster but may miss the best setting. |
| `--dont-revisit-parameters` | Do not try parameters again after a better combination is found. Converges faster but may miss the best setting. |
| `--testdata-num-files` | Number of generated files. Default `10000`. |
| `--testdata-max-file-size` | Maximum size of a generated file in bytes. Default `1048576` (1 MiB). |
| `--testdata-max-total-size` | Maximum total size of the generated data in bytes. Default `536870912` (512 MiB). |
| `--testdata-sparse-factor` | Percentage of the generated data that is set to zero, to make deduplication happen. Default `30`. |
| `--verbose` | `0` disables output, `1` prints progress during the runs. Default `1`. |
