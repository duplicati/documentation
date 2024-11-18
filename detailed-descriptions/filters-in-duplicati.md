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

For brewity, the remainder of this page will only use the Linux/MacOS format in examples, but the same can be applied to the Windows paths.

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
