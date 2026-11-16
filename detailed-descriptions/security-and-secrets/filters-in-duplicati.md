---
description: >-
  This page describes how filters are evaluated inside Duplicati and how to
  construct them
---

# Filters in Duplicati

Duplicati uses the same setup for filters to select individual files. It is most prominent when choosing the sources, but can be applied in other places where individual files can be selected.

## Path representations

Internally, Duplicati represents folders with a trailing path separator, which makes it easy to distinguish between the two types. This distinction is important when constructing filters, as Duplicati requires a full match, including the trailing path separator, before a match is considered. An example for Windows and Linux/MacOS:

* Windows
  * Folders
    * `C:\Users\john\`
    * `X:\data\`
  * Files
    * `C:\Users\myfile`
    * `X:\data\file.bin`
* Linux/MacOS
  * Folders
    * `/home/john/`
    * `/usr/share/`
  * Files
    * `/home/myfile`
    * `/usr/file.bin`

For brevity, the remainder of this page will only use the Linux/MacOS format in examples, but the same can be applied to the Windows paths.

## Filter types

Duplicati supports 4 different kinds of filters: paths, globbing, regex, and predefined groups. The simplest type of filter is the path. To use a path-type filter, simply provide the full path to the file or folder to target.&#x20;

### Globbing expressions

While it would be possible to maintain an ever growing list of paths in a filter, it can quickly become hard to manage. For cases where there is some similarity between multiple files or folder paths, it is possible to target multiple paths with a [file-globbing syntax](https://en.wikipedia.org/wiki/Glob\_\(programming\)). The wildcard character `*` matches any length of characters (including zero) and the character `?` matches a exactly one character. Unlike other glob implementations, the path separator is also matched in Duplicati filters.

An example of glob expressions:

```
/usr/share/IMG_????.jpeg
/home/*/Download/
*.iso
```

&#x20;The first expression matches files with the 4 `?` characters replaced by any character, and the second expression matches the `Download` folder for any user, and the third matches any files with the `.iso` extension.

### Regular expressions

If the paths to match are more complicated than what can be expressed with globbing, it is also possible to use [regular expressions](https://en.wikipedia.org/wiki/Regular\_expression), which are a common way of expressing a string pattern. Understanding regular expressions and applying them can be a challenging task, and will most often require some testing to ensure it is working as expected. Also note that since Duplicati is written in C#, it uses the [.NET variant of regular expressions](https://learn.microsoft.com/en-us/dotnet/standard/base-types/regular-expressions).

Regular expressions are provided by wrapping the expressions with hard braces `[ ]`:

```
[/usr/share/IMG_\d{4}\.jpeg]
[/home/[^/]+/Download/]
[.*\.iso]
```

Note that for Windows, the path separators must be escaped with a backslash, `\` so each separator becomes a double backslash `\\` .

### Predefined filter groups

Some files are commonly excluded on many systems, and to make it easier to exclude such files, Duplicati has a number of built in filter groups:

*   SystemFiles

    Files that are not real files, such as `/proc` or System Volume Information.
*   OperatingSystem

    Files that are provided by the operating system, such as `/bin` or `C:\Windows\`
*   CacheFiles

    Files that are part of application or operating system caches, such as the browser cache.
*   TemporaryFiles

    Files that are stored temporarily by applications as part of normal operations
*   Applications

    Binary applications, such as `/lib/` or `C:\Program files\` &#x20;
*   DefaultExcludes

    All the above filters in one group

To use a filter group, supply one or more names inside curly braces `{ }`, separated with commas. As an example:

```
{CacheFiles,TemporaryFiles}
```

The group names are not case sensitive, and each group also has shorter aliases:

| Group | Aliases |
| --- | --- |
| SystemFiles | `System`, `Sys` |
| OperatingSystem | `OS` |
| CacheFiles | `CacheFolders`, `Caches`, `Cache` |
| TemporaryFiles | `TemporaryFolders`, `TempFolders`, `TempFiles`, `Temp`, `Tmp` |
| Applications | `Application`, `Apps`, `App` |
| DefaultExcludes | `DefaultExclude`, `Excludes`, `Exclude`, `Exc` |

Run `duplicati-cli help filter-groups` to see the paths that each group contains on your system.

## Case sensitivity

By default, filters follow the operating system: they ignore casing on Windows and macOS, and are case-sensitive on Linux. Sources that name their items with case-sensitive identifiers, such as Microsoft 365 and Google Workspace, make the filters case-sensitive.

To choose the behavior yourself, set `--filter-case-sensitivity`. With the value `default`, the rules above apply.

{% hint style="info" %}
From canary 2.4.1.102, the change tracking of a backup and the check for duplicate paths are always case-sensitive, also on Windows and macOS. Before, they followed the operating system, which failed for remote sources, such as S3 or Google Drive, with files whose names differ only in casing. Filters are not affected by this change.
{% endhint %}

## Environment variables

Filters can contain environment variables in the format `%NAME%`, which are replaced with the value of the variable when the filter is used. This format is used on all operating systems; the `$NAME` format is not supported. For example, with the environment variable `EXT` set to `iso`:

```
-*.%EXT%
```

excludes all files with the `.iso` extension.

In the filters of a backup configuration, the special folder names that the user interface uses for common folders, such as `%MY_DOCUMENTS%`, `%DESKTOP%` and `%HOME%`, are also replaced. In a regular expression filter of a backup configuration, the value is escaped, so it is matched literally.

## Apply filters

By default, Duplicati will recurse the source folders and include every file and folder found. For this reason, most of the filters will be exclude filters that removes something from the backup. Include filters are prefixed with a `+` and exclude filters are prefixed with a `-`.

When Duplicati is evaluating filters, it will consider only the first full match, and not evaluate further. It will also evaluate folders before files, meaning that it is not possible to include a file, if the parent folder is excluded. Importantly, the filters are processed in the order they are supplied, which makes it possible to supply advanced rules. As an example:

```
+/usr/share/*.txt
-*.txt
-*.bin
+/usr/share/*.bin
```

In the example, the first rule is applied before the second rule, which means that all `.txt` files in `/usr/share/` are included, but any other `.txt` files are excluded. The inverse goes for the `.bin` files, because the exclude rule is before the last rule, the files will be matched as exclude, even though there is an include rule.

If we append a rule:

```
-/usr/share/
```

Even if this rule is last, it will exclude the entire folder. Since the folder is excluded, the match on the include rule is never evaluated. This cut-off at the folder level makes it possible to fully avoid processing subfolders, which could otherwise be time consuming.

## Other ways to leave out files

Besides filters, these options leave files out of a backup. They can be added as advanced options to a backup in the UI, or given on the command line.

### Staying on one file system

`--one-file-system` makes the backup stay on the file systems of the source folders, like the rsync option of the same name. A folder below a source folder where another file system or volume is mounted, such as a disk, a network share or a virtual file system like `/proc`, is left out together with its contents. A source folder is always included, even when it is a mount point itself.

On Windows, a volume mounted in a folder is otherwise stored as a link without its contents, unless `--symlink-policy` is set to `Follow`.

### Files that are not stored locally

Placeholder files from a sync tool, such as OneDrive, Dropbox or Google Drive, and files moved to offline storage, cannot be read without downloading them, and fail with a generic error. Duplicati recognizes these errors and suggests the option `--exclude-non-local-files`, which leaves such files out of the backup.

### Missing and unreadable source folders

A source folder that is missing stops the backup. From canary 2.4.1.100, the same applies to a source folder that exists but cannot be listed, for example on a failing drive. Before, it only gave a warning, and the backup stored a version without any of its files, so the retention policy could remove the versions that still had them. Set `--allow-missing-source` to continue the backup without missing or unreadable sources.

### Skipping files by modification time

`--skip-files-modified-before` backs up only the files modified after a point in time. Files last modified before it are skipped, and are not in the new version.

`--skip-files-modified-after` backs up only the files that have not changed for a while. Files last modified after the given point in time are skipped, and are not in the new version. This can be used to avoid backing up files that are still being written.

The value can be a date, such as `2026-01-01`, or an interval back from now, such as `1W` or `1M`. An interval cannot be combined with `--changed-files`, or with a source that tracks its own changes; use a date in those cases.

{% hint style="warning" %}
A skipped file is not in the new version, even if it was in the previous version. With retention settings, older versions that hold the file are eventually deleted.
{% endhint %}
