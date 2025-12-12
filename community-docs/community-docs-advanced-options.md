# Community docs: advanced options

This page is copied from the community documentation: [See the original version of this page](https://prev-docs.duplicati.com/en/latest/06-advanced-options/)

For each Duplicati command you have to specify a number of arguments. When performing a backup, you must supply the location and credentials for the backup files and one or more source folders. Optionally, you can specify one or more advanced options. These options will give you more control to how the command will be executed. Also additional features, like reporting, can be configured by supplying some advanced options.

Those additional options should only be used with care. For normal operation none of them should ever be required. Use this alphabetical list as a reference to find the advanced options that fit your needs.

### Core options[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#core-options) <a href="#core-options" id="core-options"></a>

These options can be used to influence the behavior of the Duplicati backup engine.

#### allow-full-removal[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#allow-full-removal) <a href="#allow-full-removal" id="allow-full-removal"></a>

`--allow-full-removal = false`\
By default, the last fileset cannot be removed. This is a safeguard to make sure that all remote data is not deleted by a configuration mistake. Use this flag to disable that protection, such that all filesets can be deleted.

#### allow-missing-source[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#allow-missing-source) <a href="#allow-missing-source" id="allow-missing-source"></a>

`--allow-missing-source = false`\
Use this option to continue even if some source entries are missing.

#### allow-passphrase[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#allow-passphrase) <a href="#allow-passphrase" id="allow-passphrase"></a>

`--allow-passphrase-change = false`\
Use this option to allow the passphrase to change, note that this option is not permitted for a backup or repair operation.

#### allow-sleep[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#allow-sleep) <a href="#allow-sleep" id="allow-sleep"></a>

`--allow-sleep = false`\
Allow system to enter sleep power modes for inactivity during backup/restore operations (Windows/OSX only).

#### all-versions[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#all-versions) <a href="#all-versions" id="all-versions"></a>

`--all-versions = false`\
When searching for files, only the most recent backup is searched. Use this option to show all previous versions too.

#### asynchronous-concurrent-upload-limit[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#asynchronous-concurrent-upload-limit) <a href="#asynchronous-concurrent-upload-limit" id="asynchronous-concurrent-upload-limit"></a>

`--asynchronous-concurrent-upload-limit = 4`\
When performing asynchronous uploads, the maximum number of concurrent uploads allowed. Set to zero to disable the limit.

If you are using a (external) hard disk as backend/destination, it is recommended to set the value to 1, because hard disks are bad at multiple, simultaneous write accesses. However, if you use an SSD as backend/destination, you can leave this value unchanged, because SSDs can handle multiple simultaneous writes much better.

#### asynchronous-upload-folder[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#asynchronous-upload-folder) <a href="#asynchronous-upload-folder" id="asynchronous-upload-folder"></a>

`--asynchronous-upload-folder = C:\Users\User\AppData\Local\Temp\`\
The pre-generated volumes will be placed into the temporary folder by default, this option can set a different folder for placing the temporary volumes, despite the name, this also works for synchronous runs.

#### asynchronous-upload-limit[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#asynchronous-upload-limit) <a href="#asynchronous-upload-limit" id="asynchronous-upload-limit"></a>

`--asynchronous-upload-limit = 4`\
When performing asynchronous uploads, Duplicati will create volumes that can be uploaded. To prevent Duplicati from generating too many volumes, this option limits the number of pending uploads. Set to zero to disable the limit

#### auto-cleanup[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#auto-cleanup) <a href="#auto-cleanup" id="auto-cleanup"></a>

`--auto-cleanup = false`\
If a backup is interrupted there will likely be partial files present on the backend. Using this flag, Duplicati will automatically remove such files when encountered.

#### auto-compact-interval[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#auto-compact-interval) <a href="#auto-compact-interval" id="auto-compact-interval"></a>

`--auto-compact-interval = 0m`\
The minimum amount of time that must elapse after the last compaction before another will be automatically triggered at the end of a backup job. Automatic compaction can be a long-running process and may not be desirable to run after every single backup. Requires `--no-auto-compact = false`.

#### auto-update[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#auto-update) <a href="#auto-update" id="auto-update"></a>

`--auto-update = false`\
Set this option if you prefer to have the commandline version automatically update

#### auto-vacuum[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#auto-vacuum) <a href="#auto-vacuum" id="auto-vacuum"></a>

`--auto-vacuum = false`\
Some operations that manipulate the local database leave unused entries behind. These entries are not deleted from a hard drive until a VACUUM operation is run. This operation saves disk space in the long run but needs to temporarily create a copy of all valid entries in the database. Setting this to true will allow Duplicati to perform VACUUM operations at its discretion.

#### auto-vacuum-interval[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#auto-vacuum-interval) <a href="#auto-vacuum-interval" id="auto-vacuum-interval"></a>

`--auto-vacuum-interval = 0m`\
The minimum amount of time that must elapse after the last vacuum before another will be automatically triggered at the end of a backup job. Automatic vacuum can be a long-running process and may not be desirable to run after every single backup. Requires `--auto-vacuum = true`.

#### backup-name[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#backup-name) <a href="#backup-name" id="backup-name"></a>

`--backup-name = Duplicati.CommandLine`\
A display name that is attached to this backup. Can be used to identify the backup when sending mail or running scripts.

#### backup-test-percentage[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#backup-test-percentage) <a href="#backup-test-percentage" id="backup-test-percentage"></a>

`--backup-test-percentage = 0` After a backup is completed, some (dblock, dindex, dlist) files from the remote backend are selected for verification. Use this option to specify the percentage (between 0 and 100) of files to test. If the backup-test-samples option is also provided, the number of samples tested is the maximum implied by the two options. If the no-backend-verification option is provided, no remote files are verified.

#### backup-test-samples[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#backup-test-samples) <a href="#backup-test-samples" id="backup-test-samples"></a>

`--backup-test-samples = 1`\
After a backup is completed, some files (in each file categoy: dblock, dindex, dlist) are selected for verification on the remote backend. Use this option to change how many files in each category should be tested. If this value is set to 0 or the option --no-backend-verification is set, no remote files are verified.

#### block-hash-algorithm[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#block-hash-algorithm) <a href="#block-hash-algorithm" id="block-hash-algorithm"></a>

`--block-hash-algorithm = SHA256`\
This is a very advanced option! This option can be used to select a block hash algorithm with smaller or larger hash size, for performance or storage space reasons.

#### blocksize[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#blocksize) <a href="#blocksize" id="blocksize"></a>

`--blocksize = 100kb`\
The block size determines how files are fragmented. Choosing a large value will cause a larger overhead on file changes, choosing a small value will cause a large overhead on storage of file lists. Note that the value cannot be changed after remote files are created.

#### changed-files[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#changed-files) <a href="#changed-files" id="changed-files"></a>

`--changed-files` This option can be used to limit the scan to only files that are known to have changed. This is usually only activated in combination with a filesystem watcher that keeps track of file changes.

#### check-filetime-only[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#check-filetime-only) <a href="#check-filetime-only" id="check-filetime-only"></a>

`--check-filetime-only = false`\
This flag instructs Duplicati to not look at metadata or filesize when deciding to scan a file for changes. Use this option if you have a large number of files and notice that the scanning takes a long time with unmodified files.

#### compression-extension-file[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#compression-extension-file) <a href="#compression-extension-file" id="compression-extension-file"></a>

`--compression-extension-file = C:\Program Files\Duplicati 2\default_compressed_extensions.txt`\
This property can be used to point to a text file where each line contains a file extension that indicates a non-compressible file. Files that have an extension found in the file will not be compressed, but simply stored in the archive. The file format ignores any lines that do not start with a period, and considers a space to indicate the end of the extension. A default file is supplied, that also serves as an example. The default file is placed in `C:\Program Files\Duplicati 2\default_compressed_extensions.txt`.

#### compression-module[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#compression-module) <a href="#compression-module" id="compression-module"></a>

`--compression-module = zip`\
Duplicati supports pluggable compression modules. Use this option to select a module to use for compression. This is only applied when creating new volumes, when reading an existing file, the filename is used to select the compression module.

#### concurrency-block-hashers[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#concurrency-block-hashers) <a href="#concurrency-block-hashers" id="concurrency-block-hashers"></a>

`--concurrency-block-hashers = 2`\
Use this option to set the number of processes that perform hashing of data.

#### concurrency-compressors[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#concurrency-compressors) <a href="#concurrency-compressors" id="concurrency-compressors"></a>

`--concurrency-compressors = 2`\
Use this option to set the number of processes that perform compression of output data.

#### concurrency-max-threads[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#concurrency-max-threads) <a href="#concurrency-max-threads" id="concurrency-max-threads"></a>

`--concurrency-max-threads = 0`\
Use this option to set the maximum number of threads used. Setting this value to zero or less will dynamically balance the number of active threads to fit the hardware.

#### console-log-filter[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#console-log-filter) <a href="#console-log-filter" id="console-log-filter"></a>

`--console-log-filter`\
This option accepts filters that removes or includes messages regardless of their log level. Multiple filters are supported by separating with :`.` Filters are matched against the log tag and assumed to be including, unless they start with `-`. Regular expressions are supported within hard braces. Example: `+Path*:+*Mail*:-[.*DNS]`

#### console-log-level[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#console-log-level) <a href="#console-log-level" id="console-log-level"></a>

`--console-log-level = Warning`\
Console information level

#### control-files[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#control-files) <a href="#control-files" id="control-files"></a>

`--control-files = false`\
Use control files.

#### dblock-size[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#dblock-size) <a href="#dblock-size" id="dblock-size"></a>

`--dblock-size = 50mb`\
This option can change the maximum size of dblock files. Changing the size can be useful if the backend has a limit on the size of each individual file.

#### dbpath[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#dbpath) <a href="#dbpath" id="dbpath"></a>

`--dbpath`\
Path to the file containing the local cache of the remote file database.

#### debug-output[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#debug-output) <a href="#debug-output" id="debug-output"></a>

`--debug-output = false`\
Activating this option will make some error messages more verbose, which may help you track down a particular issue.

#### debug-retry-errors[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#debug-retry-errors) <a href="#debug-retry-errors" id="debug-retry-errors"></a>

`--debug-retry-errors = false`\
When an error occurs, Duplicati will silently retry, and only report the number of retries. Enable this option to have the error messages displayed when a retry is performed.

#### default-filters[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#default-filters) <a href="#default-filters" id="default-filters"></a>

`--default-filters`\
Exclude files that match the given filter sets. Which default filter sets should be used. Valid sets are `Windows`, `OSX`, `Linux` and `All`. If this parameter is set with no value, the set for the current operating system will be used.

#### deleted-files[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#deleted-files) <a href="#deleted-files" id="deleted-files"></a>

`--deleted-files`\
This option can be used to supply a list of deleted files. This option will be ignored unless the option `--changed-files` is also set.

#### disable-autocreate-folder[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-autocreate-folder) <a href="#disable-autocreate-folder" id="disable-autocreate-folder"></a>

`--disable-autocreate-folder = false`\
If Duplicati detects that the target folder is missing, it will create it automatically. Activate this option to prevent automatic folder creation.

#### disable-filepath-cache[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-filepath-cache) <a href="#disable-filepath-cache" id="disable-filepath-cache"></a>

`--disable-filepath-cache = true`\
This option can be used to reduce the memory footprint by not keeping paths and modification timestamps in memory.

#### disable-filetime-check[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-filetime-check) <a href="#disable-filetime-check" id="disable-filetime-check"></a>

`--disable-filetime-check = false`\
The operating system keeps track of the last time a file was written. Using this information, Duplicati can quickly determine if the file has been modified. If some application deliberately modifies this information, Duplicati won't work correctly unless this flag is set.

#### disable-on-battery[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-on-battery) <a href="#disable-on-battery" id="disable-on-battery"></a>

`--disable-on-battery = false`\
When this flag is enabled, a scheduled backup will not run if the system is detected to be running on battery power (manual or command line backups will still be run). If the detected power source is mains (i.e., AC) or unknown, then scheduled backups will proceed as normal.

#### disable-module[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-module) <a href="#disable-module" id="disable-module"></a>

`--disable-module`\
Supply one or more module names, separated by commas to unload them.

#### disable-piped-streaming[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-piped-streaming) <a href="#disable-piped-streaming" id="disable-piped-streaming"></a>

`--disable-piped-streaming = false`\
Use this option to disable multithreaded handling of up- and downloads, that can significantly speed up backend operations depending on the hardware you're running on and the transfer rate of your backend.

#### disable-streaming-transfers[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-streaming-transfers) <a href="#disable-streaming-transfers" id="disable-streaming-transfers"></a>

`--disable-streaming-transfers = false`\
Enabling this option will disallow usage of the streaming interface, which means that transfer progress bars will not show, and bandwidth throttle settings will be ignored.

#### disable-synthetic-filelist[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-synthetic-filelist) <a href="#disable-synthetic-filelist" id="disable-synthetic-filelist"></a>

`--disable-synthetic-filelist = false`\
If Duplicati detects that the previous backup did not complete, it will generate a filelist that is a merge of the last completed backup and the contents that were uploaded in the incomplete backup session.

#### disable-time-tolerance[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-time-tolerance) <a href="#disable-time-tolerance" id="disable-time-tolerance"></a>

`--disable-time-tolerance = false`\
When matching timestamps, Duplicati will adjust the times by a small fraction to ensure that minor time differences do not cause unexpected updates. If the option `--keep-time` is set to keep a week of backups, and the backup is made the same time each week, it is possible that the clock drifts slightly, such that full week has just passed, causing Duplicati to delete the older backup earlier than expected. To avoid this, Duplicati inserts a 1% tolerance (max 1 hour). Use this option to disable the tolerance, and use strict time checking.

#### dont-compress-restore-paths[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#dont-compress-restore-paths) <a href="#dont-compress-restore-paths" id="dont-compress-restore-paths"></a>

`--dont-compress-restore-paths = false`\
When restore a subset of a backup into a new folder, the shortest possible path is used to avoid generating deep paths with empty folders. Use this flag to skip this compression, such that the entire original folder structure is preserved, including upper level empty folders.

#### dont-read-manifests[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#dont-read-manifests) <a href="#dont-read-manifests" id="dont-read-manifests"></a>

`--dont-read-manifests = false`\
This option will make sure the contents of the manifest file are not read. This also implies that file hashes are not checked either. Use only for disaster recovery.

#### dry-run[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#dry-run) <a href="#dry-run" id="dry-run"></a>

`--dry-run = false`\
This option can be used to experiment with different settings and observe the outcome without changing actual files.

#### enable-module[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#enable-module) <a href="#enable-module" id="enable-module"></a>

`--enable-module`\
Supply one or more module names, separated by commas to load them.

#### encryption-module[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#encryption-module) <a href="#encryption-module" id="encryption-module"></a>

`--encryption-module = aes`\
Duplicati supports pluggable encryption modules. Use this option to select a module to use for encryption. This is only applied when creating new volumes, when reading an existing file, the filename is used to select the encryption module.

#### exclude[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#exclude) <a href="#exclude" id="exclude"></a>

`--exclude`\
Exclude files that match this filter. The special character `*` means any number of character, and the special character `?` means any single character, use `*.txt` to exclude all files with a txt extension. Regular expressions are also supported and can be supplied by using hard braces, i.e. `[.*\.txt]`.

#### exclude-empty-folders[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#exclude-empty-folders) <a href="#exclude-empty-folders" id="exclude-empty-folders"></a>

`--exclude-empty-folders = false`\
Use this option to remove all empty folders from a backup.

#### exclude-files-attributes[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#exclude-files-attributes) <a href="#exclude-files-attributes" id="exclude-files-attributes"></a>

`--exclude-files-attributes`\
Use this option to exclude files with certain attributes. Use a comma separated list of attribute names to specify more than one. Possible values are: `ReadOnly`, `Hidden`, `System`, `Directory`, `Archive`, `Device`, `Normal`, `Temporary`, `SparseFile`, `ReparsePoint`, `Compressed`, `Offline`, `NotContentIndexed`, `Encrypted`, `IntegrityStream`, `NoScrubData`.

#### file-hash-algorithm[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#file-hash-algorithm) <a href="#file-hash-algorithm" id="file-hash-algorithm"></a>

`--file-hash-algorithm = SHA256`\
This is a very advanced option! This option can be used to select a file hash algorithm with smaller or larger hash size, for performance or storage space reasons.

#### file-read-buffer-size[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#file-read-buffer-size) <a href="#file-read-buffer-size" id="file-read-buffer-size"></a>

`--file-read-buffer-size = 0kb`\
Use this size to control how many bytes a read from a file before processing.

#### force-locale[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#force-locale) <a href="#force-locale" id="force-locale"></a>

`--force-locale`\
By default, your system locale and culture settings will be used. In some cases you may prefer to run with another locale, for example to get messages in another language. This option can be used to set the locale. Supply a blank string to choose the "Invariant Culture".

#### full-block-verification[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#full-block-verification) <a href="#full-block-verification" id="full-block-verification"></a>

`--full-block-verification = false`\
Use this option to increase verification by checking the hash of ALL blocks in a block volume. When restoring, all blocks of a volume are tested or none according to this option.\
When testing, if this option is set all blocks of a volume are tested, if not, a random 20%.\
When testing, this option implies --full-remote-verification.\
It is defaulting to false in both backup and explicit test, since it is using several database queries for each block in a remote volume, which is slow.

#### full-remote-verification[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#full-remote-verification) <a href="#full-remote-verification" id="full-remote-verification"></a>

`--full-remote-verification = false`\
After a backup is completed, some (by default 1 of each type, see the --backup-test- options to change that) files are selected for verification on the remote backend; use this option to turn on verification of the selected volumes, which will decrypt and verify their content, instead of simply verifying the volume hash if false.\
Note that whatever the option value, the volume has to be fetched from the backend, but no temporary file is created when just checking the hash. For backups, to have any effect, the --no-backend-verification option should be NOT set.\
For tests launched independently of backups (test in command line mode), if defined, it is set by default to false. However, if not defined, a full remote verification is performed.\
Note that a data volume is not necessarily completely verified, see above --full-block-verification.

#### full-result[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#full-result) <a href="#full-result" id="full-result"></a>

`--full-result = false`\
Use this option to increase the amount of output generated as the result of the operation, including all filenames.

#### hardlink-policy[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#hardlink-policy) <a href="#hardlink-policy" id="hardlink-policy"></a>

`--hardlink-policy = All`\
Use this option to handle hardlinks (only works on Linux/OSX). The `first` option will record a hardlink ID for each hardlink to avoid storing hardlinked paths multiple times. The option `all` will ignore hardlink information, and treat each hardlink as a unique path. The option `none` will ignore all hardlinks with more than one link.

#### ignore-filenames[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#ignore-filenames) <a href="#ignore-filenames" id="ignore-filenames"></a>

`--ignore-filenames`\
Use this option to set a filename, or list of filenames, that indicate exclusion of a folder which contains it. A common use would be to have a file named something like ".nobackup" and place this file into folders that should not be backed up. The separator for Windows systems is a semicolon (;) and for Linux systems is a colon (:).

#### include[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#include) <a href="#include" id="include"></a>

`--include`\
Include files that match this filter. The special character `*` means any number of character, and the special character `?` means any single character, use `*.txt` to include all files with a txt extension. Regular expressions are also supported and can be supplied by using hard braces, i.e.`[.*\.txt]`.

#### index-file-policy[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#index-file-policy) <a href="#index-file-policy" id="index-file-policy"></a>

`--index-file-policy = Full`\
The index files are used to limit the need for downloading dblock files when there is no local database present.\
The more information is recorded in the index files, the faster operations can proceed without the database. The tradeoff is that larger index files take up more remote space and which may never be used.

#### keep-time[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#keep-time) <a href="#keep-time" id="keep-time"></a>

`--keep-time`\
Use this option to set the timespan in which backups are kept.

#### keep-versions[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#keep-versions) <a href="#keep-versions" id="keep-versions"></a>

`--keep-versions = 0`\
Use this option to set number of versions to keep, supply `-1` to keep all versions.

#### list-folder-contents[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#list-folder-contents) <a href="#list-folder-contents" id="list-folder-contents"></a>

`--list-folder-contents = false`\
When searching for files, all matching files are returned. Use this option to return only the entries found in the folder specified as filter.

#### list-prefix-only[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#list-prefix-only) <a href="#list-prefix-only" id="list-prefix-only"></a>

`--list-prefix-only = false`\
When searching for files, all matching files are returned. Use this option to return only the largest common prefix path.

#### list-sets-only[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#list-sets-only) <a href="#list-sets-only" id="list-sets-only"></a>

`--list-sets-only = false`\
Use this option to only list filesets and avoid traversing file names and other metadata which slows down the process.

#### list-verify-uploads[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#list-verify-uploads) <a href="#list-verify-uploads" id="list-verify-uploads"></a>

`--list-verify-uploads = false`\
Verify uploads by listing contents.

#### log-file[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#log-file) <a href="#log-file" id="log-file"></a>

`--log-file`\
Log internal information.

#### log-file-log-filter[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#log-file-log-filter) <a href="#log-file-log-filter" id="log-file-log-filter"></a>

`--log-file-log-filter`\
This option accepts filters that removes or includes messages regardless of their log level. Multiple filters are supported by separating with :. Filters are matched against the log tag and assumed to be including, unless they start with `-`. Regular expressions are supported within hard braces. Example: `+Path*:+*Mail*:-[.*DNS]`

#### log-file-log-level[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#log-file-log-level) <a href="#log-file-log-level" id="log-file-log-level"></a>

`--log-file-log-level = Warning`\
Log file information level Possible options: See `log-level`.

#### log-level[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#log-level) <a href="#log-level" id="log-level"></a>

`--log-level = Warning`\
Specifies the amount of log information to write into the file specified by `--log-file`.\
Suppored options:\
\&#xNAN;_`Error`: Error messages only._\
`Warning`: Also warnings, meaning that later errors may be related to this message.\
\&#xNAN;_`DryRun`: Also messages from dry- run output._\
`Information`: Also informational messages that do not indicate problems.\
\&#xNAN;_`Retry`: Also messages from a retry._\
`Profiling`: Also messages that are not wanted for display.\
\* `ExplicitOnly`: Also messages that are only be shown if explicitly requested.

#### log-retention[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#log-retention) <a href="#log-retention" id="log-retention"></a>

`--log-retention = 30D`\
Set the time after which log data will be purged from the database.

#### no-auto-compact[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#no-auto-compact) <a href="#no-auto-compact" id="no-auto-compact"></a>

`--no-auto-compact = false`\
If a large number of small files are detected during a backup, or wasted space is found after deleting backups, the remote data will be compacted. Use this option to disable such automatic compacting and only compact when running the compact command.

#### no-backend-verification[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#no-backend-verification) <a href="#no-backend-verification" id="no-backend-verification"></a>

`--no-backend-verification = false`\
If this flag is set, the local database is not compared to the remote filelist on startup. The intended usage for this option is to work correctly in cases where the filelisting is broken or unavailable.

#### no-connection-reuse[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#no-connection-reuse) <a href="#no-connection-reuse" id="no-connection-reuse"></a>

`--no-connection-reuse = false`\
Duplicati will attempt to perform multiple operations on a single connection, as this avoids repeated login attempts, and thus speeds up the process. This option can be used to ensure that each operation is performed on a separate connection.

#### no-encryption[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#no-encryption) <a href="#no-encryption" id="no-encryption"></a>

`--no-encryption = false`\
If you store the backups on a local disk, and prefer that they are kept unencrypted, you can turn off encryption completely by using this switch.

#### no-local-blocks[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#no-local-blocks) <a href="#no-local-blocks" id="no-local-blocks"></a>

`--no-local-blocks = false`\
Duplicati will attempt to use data from source files to minimize the amount of downloaded data. Use this option to skip this optimization and only use remote data.

#### no-local-db[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#no-local-db) <a href="#no-local-db" id="no-local-db"></a>

`--no-local-db = false`\
When listing contents or when restoring files, the local database can be skipped. This is usually slower, but can be used to verify the actual contents of the remote store.

#### number-of-retries[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#number-of-retries) <a href="#number-of-retries" id="number-of-retries"></a>

`--number-of-retries = 5`\
If an upload or download fails, Duplicati will retry a number of times before failing. Use this to handle unstable network connections better.

#### overwrite[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#overwrite) <a href="#overwrite" id="overwrite"></a>

`--overwrite = false`\
Use this option to overwrite target files when restoring, if this option is not set the files will be restored with a timestamp and a number appended.

#### parameters-file[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#parameters-file) <a href="#parameters-file" id="parameters-file"></a>

`--parameters-file`\
This option can be used to store some or all of the options given to the commandline client. The file must be a plain text file, UTF-8 encoding is preferred. Each line in the file should be of the format `--option=value`. The special options `--source` and `--target` can be used to override the localpath and the remote destination uri, respectively. The options in this file take precedence over the options provided on the commandline. You cannot specify filters in both the file and on the commandline. Instead, you can use the special`--replace-filter`, `--append-filter`, or `--prepend-filter` options to specify filters inside the parameter file. Each filter must be prefixed with either a `+` or a `-`, and multiple filters must be joined with `;`.

#### passphrase[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#passphrase) <a href="#passphrase" id="passphrase"></a>

`--passphrase`\
Supply a passphrase that Duplicati will use to encrypt the backup volumes, making them unreadable without the passphrase. This variable can also be supplied through the environment variable `PASSPHRASE`.

#### patch-with-local-blocks[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#patch-with-local-blocks) <a href="#patch-with-local-blocks" id="patch-with-local-blocks"></a>

`--patch-with-local-blocks = false`\
Enable this option to look into other files on this machine to find existing blocks. This is a fairly slow operation but can limit the size of downloads.

#### prefix[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#prefix) <a href="#prefix" id="prefix"></a>

`--prefix = duplicati`\
A string used to prefix the filenames of the remote volumes, can be used to store multiple backups in the same remote folder. The prefix cannot contain a hyphen (`-`), but can contain all other characters allowed by the remote storage.

#### quiet-console[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#quiet-console) <a href="#quiet-console" id="quiet-console"></a>

`--quiet-console = false`\
If this option is set, progress reports and other messages that would normally go to the console will be redirected to the log.

#### quota-size[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#quota-size) <a href="#quota-size" id="quota-size"></a>

`--quota-size`\
This value can be used to set a known upper limit on the amount of space a backend has. If the backend reports the size itself, this value is ignored.

#### repair-only-paths[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#repair-only-paths) <a href="#repair-only-paths" id="repair-only-paths"></a>

`--repair-only-paths = false`\
Use this option to build a searchable local database which only contains path information. This option is usable for quickly building a database to locate certain content without needing to reconstruct all information. The resulting database can be searched, but cannot be used to restore data with.

#### restore-path[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#restore-path) <a href="#restore-path" id="restore-path"></a>

`--restore-path`\
By default, files will be restored in the source folders, use this option to restore to another folder.

#### restore-permissions[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#restore-permissions) <a href="#restore-permissions" id="restore-permissions"></a>

`--restore-permissions = false`\
By default permissions are not restored as they might prevent you from accessing your files. Use this option to restore the permissions as well.

#### restore-symlink-metadata[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#restore-symlink-metadata) <a href="#restore-symlink-metadata" id="restore-symlink-metadata"></a>

`--restore-symlink-metadata = false`\
If symlink metadata is applied, it will usually mean changing the symlink target, instead of the symlink itself. For this reason, metadata is not applied to symlinks, but this option can be used to override this, such that metadata is applied to symlinks as well.

#### retention-policy[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#retention-policy) <a href="#retention-policy" id="retention-policy"></a>

`--retention-policy`\
Use this option to limit growth or reduce the size of a backup. This is achieved by erasing all versions which are closer than a specified time distance from the preceeding version, thus keeping only one version after an each interval. The interval may increase in steps. Backup data which are not referred by any version any longer, get purched. --retention-policy is mutual exclusive to the other retention options --keep-time and --keep-version.

Specify one or more timeframe:interval duples, such as '7D:0s' Valid letters for time: 's', 'm', 'h', 'D', 'W', 'M', 'Y' Multiple duples shall be comma separated, and time frames shall be increasing. For example the value '7D:0s,3M:1D,10Y:2M' means "during the next 7 day keep all backups, during the next 3 months from now keep a daily backup and for 10 years from now keep one backup every 2nd month. 0s stands for an interval of zero length, allowing unlimited versions to be kept, which can be also noted as 'U' To avoid gaps, time frames all start at "now" and overlap, with smaller time frames taking priority, thus the effective duration of longer time frames becomes shorter.

Versions which are outside of any time frame will be erased. A safeguard prevents that at least one version is retained (which is the most recent). This can be overridden by --allow-full-removal. Hence, the latest (most current) version of existing files will be kept unlimited time, while deleted or lost files and older-than-current versions of existing files will be purged after the longest time frame.

The most current version is excluded from retention-policy, so whenever a backup is run manually it will be kept, until the next run, then the rules apply. This can also be overridden by --allow-full-removal.

Notice that 'versions' are in fact indices that refer to versions of the source and backups. When indices age through the schema, they will be passed from one timeframe to the next. Every rule checks if an entering index fits in the intervall, and if the distance to the preceding index is to short, the newer index will get purged. The deduplication of the backup volume implicate that file are referred by multiple indices. Only backup data will be purged, which are not longer referred by any index, respectively are not longer needed to restore any preserved version of the source.

#### retry-delay[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#retry-delay) <a href="#retry-delay" id="retry-delay"></a>

`--retry-delay = 10s`\
After a failed transmission, Duplicati will wait a short period before attempting again. This is useful if the network drops out occasionally during transmissions.

#### skip-file-hash-checks[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#skip-file-hash-checks) <a href="#skip-file-hash-checks" id="skip-file-hash-checks"></a>

`--skip-file-hash-checks = false`\
If the hash for the volume does not match, Duplicati will refuse to use the backup. Supply this flag to allow Duplicati to proceed anyway.

#### skip-files-larger-than[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#skip-files-larger-than) <a href="#skip-files-larger-than" id="skip-files-larger-than"></a>

`--skip-files-larger-than`\
This option allows you to exclude files that are larger than the given value. Use this to prevent backups becoming extremely large.

#### skip-metadata[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#skip-metadata) <a href="#skip-metadata" id="skip-metadata"></a>

`--skip-metadata = false`\
Use this option to disable the storage of metadata, such as file timestamps. Disabling metadata storage will speed up the backup and restore operations, but does not affect file size much.

#### skip-restore-verification[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#skip-restore-verification) <a href="#skip-restore-verification" id="skip-restore-verification"></a>

`--skip-restore-verification = false`\
After restoring files, the file hash of all restored files are checked to verify that the restore was successful.\
Use this option to disable the check and avoid waiting for the verification.

#### small-file-max-count[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#small-file-max-count) <a href="#small-file-max-count" id="small-file-max-count"></a>

`--small-file-max-count = 20`\
To avoid filling the remote storage with small files, this value can force grouping small files. The small volumes will always be combined when they can fill an entire volume.

#### small-file-size[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#small-file-size) <a href="#small-file-size" id="small-file-size"></a>

`--small-file-size`\
When examining the size of a volume in consideration for compacting, a small tolerance value is used, by default 20 percent of the volume size. This ensures that large volumes which may have a few bytes wasted space are not downloaded and rewritten.

#### snapshot-policy[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#snapshot-policy) <a href="#snapshot-policy" id="snapshot-policy"></a>

`--snapshot-policy = off`\
This setting controls the usage of snapshots, which allows Duplicati to backup files that are locked by other programs. If this is set to `off`, Duplicati will not attempt to create a disk snapshot. Setting this to `auto` makes Duplicati attempt to create a snapshot, and fail silently if that was not allowed or supported. A setting of `on` will also make Duplicati attempt to create a snapshot, but will produce a warning message in the log if it fails. Setting it to `required` will make Duplicati abort the backup if the snapshot creation fails. On Windows this uses the Volume Shadow Copy Services (VSS) and requires administrative privileges. On Linux this uses Logical Volume Management (LVM) and requires root privileges.

#### store-metadata[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#store-metadata) <a href="#store-metadata" id="store-metadata"></a>

`--store-metadata = true`\
Stores metadata, such as file timestamps and attributes. This increases the required storage space as well as the processing time.

#### symlink-policy[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#symlink-policy) <a href="#symlink-policy" id="symlink-policy"></a>

`--symlink-policy = Store`\
Use this option to handle symlinks differently. The `store` option will simply record a symlink with its name and destination, and a restore will recreate the symlink as a link. Use the option `ignore`to ignore all symlinks and not store any information about them. The option `follow` will cause the symlinked target to be backed up and restored as a normal file with the symlink name. Early versions of Duplicati did not support this option and bevhaved as if `follow` was specified.

#### synchronous-upload[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#synchronous-upload) <a href="#synchronous-upload" id="synchronous-upload"></a>

`--synchronous-upload = false`\
Duplicati will upload files while scanning the disk and producing volumes, which usually makes the backup faster.\
Use this flag to turn the behavior off, so that Duplicati will wait for each volume to complete.

#### tempdir[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#tempdir) <a href="#tempdir" id="tempdir"></a>

`--tempdir = C:\Users\User\AppData\Local\Temp\`\
Duplicati will use the system default temporary folder. This option can be used to supply an alternative folder for temporary storage. Note that SQLite will always put temporary files in the system default temporary folder.\
Consider using the `TMPDIR` environment variable on Linux to set the temporary folder for both Duplicati and SQLite.

#### thread-priority[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#thread-priority) <a href="#thread-priority" id="thread-priority"></a>

`--thread-priority = normal`\
Selects another thread priority for the process. Use this to set Duplicati to be more or less CPU intensive.

#### threshold[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#threshold) <a href="#threshold" id="threshold"></a>

`--threshold = 25`\
As files are changed, some data stored at the remote destination may not be required. This option controls how much wasted space the destination can contain before being reclaimed. This value is a percentage used on each volume and the total storage.

#### throttle-download[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#throttle-download) <a href="#throttle-download" id="throttle-download"></a>

`--throttle-download = 0kb`\
By setting this value you can limit how much bandwidth Duplicati consumes for downloads. Setting this limit can make the backups take longer, but will make Duplicati less intrusive.

#### throttle-upload[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#throttle-upload) <a href="#throttle-upload" id="throttle-upload"></a>

`--throttle-upload = 0kb`\
By setting this value you can limit how much bandwidth Duplicati consumes for uploads. Setting this limit can make the backups take longer, but will make Duplicati less intrusive.

#### time[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#time) <a href="#time" id="time"></a>

`--time = now`\
By default, Duplicati will list and restore files from the most recent backup, use this option to select another item. You may use relative times, like "-2M" for a backup from two months ago.

#### unittest-mode[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#unittest-mode) <a href="#unittest-mode" id="unittest-mode"></a>

`--unittest-mode = false`\
When running in unittest mode, no automatic fixes are applied, which assumes that the input data is always in perfect shape. This option is not intended for use in daily backups, but required for testing purposes to reveal potential problems.

#### upload-unchanged-backups[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#upload-unchanged-backups) <a href="#upload-unchanged-backups" id="upload-unchanged-backups"></a>

`--upload-unchanged-backups = false`\
If no files have changed, Duplicati will not upload a backup set. If the backup data is used to verify that a backup was executed, this option will make Duplicati upload a backupset even if it is empty.

#### upload-verification-file[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#upload-verification-file) <a href="#upload-verification-file" id="upload-verification-file"></a>

`--upload-verification-file = false`\
Use this option to upload a verification file after changing the remote storage. The file is not encrypted and contains the size and SHA256 hashes of all the remote files and can be used to verify the integrity of the files.

#### use-background-io-priority[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#use-background-io-priority) <a href="#use-background-io-priority" id="use-background-io-priority"></a>

`--use-background-io-priority = false`\
This option instructions the operating system to set the current process to use the lowest IO priority level, which can make operations run slower but will interfere less with other operations running at the same time.

#### usn-policy[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#usn-policy) <a href="#usn-policy" id="usn-policy"></a>

`--usn-policy = off`\
This setting controls the usage of NTFS USN numbers, which allows Duplicati to obtain a list of files and folders much faster. If this is set to `off`, Duplicati will not attempt to use USN. Setting this to `auto` makes Duplicati attempt to use USN, and fail silently if that was not allowed or supported. A setting of `on` will also make Duplicati attempt to use USN, but will produce a warning message in the log if it fails. Setting it to `required` will make Duplicati abort the backup if the USN usage fails. This feature is only supported on Windows and requires administrative privileges.

#### verbose[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#verbose) <a href="#verbose" id="verbose"></a>

`--verbose = false`\
Use this option to increase the amount of output generated when running an option. Generally this option will produce a line for each file processed.

#### version[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#version) <a href="#version" id="version"></a>

`--version`\
By default, Duplicati will list and restore files from the most recent backup, use this option to select another item. You may enter multiple values separated with comma, and ranges using `-`, e.g. `0,2-4,7`.

#### vss-exclude-writers[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#vss-exclude-writers) <a href="#vss-exclude-writers" id="vss-exclude-writers"></a>

`--vss-exclude-writers`\
Use this option to exclude faulty writers from a snapshot. This is equivalent to the -wx flag of the vshadow.exe tool, except that it only accepts writer class GUIDs, and not component names or instance GUIDs. Multiple GUIDs must be separated with a semicolon, and most forms of GUIDs are allowed, including with and without curly braces.

#### vss-use-mapping[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#vss-use-mapping) <a href="#vss-use-mapping" id="vss-use-mapping"></a>

`--vss-use-mapping = false`\
Activate this option to map VSS snapshots to a drive (similar to SUBST, using Win32 DefineDosDevice). This will create temporary drives that are then used to access the contents of a snapshot. This workaround can speed up file access on Windows XP.

### HTTP options[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#http-options) <a href="#http-options" id="http-options"></a>

With these options can be used to change the way http requests are issued. The module that provide these options is loaded automatically, use `--disable-module` to prevent this.

#### disable-expect100-continue[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-expect100-continue) <a href="#disable-expect100-continue" id="disable-expect100-continue"></a>

`--disable-expect100-continue (Boolean)`\
Disable the expect header.\
The default HTTP request has the header "Expect: 100-Continue" attached, which allows some optimizations when authenticating, but also breaks some web servers, causing them to report "417 - Expectation failed".\
Default value: `false`

#### disable-nagling[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#disable-nagling) <a href="#disable-nagling" id="disable-nagling"></a>

`--disable-nagling (Boolean)`\
Disable nagling.\
By default the http requests use the RFC 896 nagling algorithm to support transfer of small packages more efficiently.\
Default value: `false`

#### accept-specified-ssl-hash[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#accept-specified-ssl-hash) <a href="#accept-specified-ssl-hash" id="accept-specified-ssl-hash"></a>

`--accept-specified-ssl-hash (String)`\
Optionally accept a known SSL certificate.\
If your server certificate is reported as invalid (eg. with self-signed certificates), you can supply the certificate hash (SHA1) to approve it anyway. The hash value must be entered in hex format without spaces or colons. You can enter multiple hashes separated by commas.

#### accept-any-ssl-certificate[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#accept-any-ssl-certificate) <a href="#accept-any-ssl-certificate" id="accept-any-ssl-certificate"></a>

`--accept-any-ssl-certificate (Boolean)`\
Accept any server certificate.\
Use this option to accept any server certificate, regardless of what errors it may have. Please use `--accept-specified-ssl-hash` instead, whenever possible.

#### oauth-url[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#oauth-url) <a href="#oauth-url" id="oauth-url"></a>

`--oauth-url (String)`\
Alternate OAuth URL.\
Duplicati uses an external server to support the OAuth authentication flow. If you have set up your own Duplicati OAuth server, you can supply the refresh url.\
Default value: `https://duplicati-oauth-handler.appspot.com/refresh`

#### allowed-ssl-versions[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#allowed-ssl-versions) <a href="#allowed-ssl-versions" id="allowed-ssl-versions"></a>

`--allowed-ssl-versions (Flags)`\
Sets allowed SSL versions.\
This option changes the default SSL versions allowed. This is an advanced option and should only be used if you want to enhance security or work around an issue with a particular SSL protocol.\
Values: `Ssl3`, `Tls`, `Tls11`, `Tls12`, `SystemDefault`\
Default value: `SystemDefault,Ssl3,Tls`

#### http-operation-timeout[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#http-operation-timeout) <a href="#http-operation-timeout" id="http-operation-timeout"></a>

`--http-operation-timeout (Timespan)`\
Sets the default operation timeout.\
This option changes the default timeout for any HTTP request, the time covers the entire operation from initial packet to shutdown.

#### http-readwrite-timeout[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#http-readwrite-timeout) <a href="#http-readwrite-timeout" id="http-readwrite-timeout"></a>

`--http-readwrite-timeout (Timespan)`\
Sets readwrite.\
This option changes the default read-write timeout. Read-write timeouts are used to detect a stalled requests, and this option configures the maximum time between activity on a connection.

#### http-enable-buffering[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#http-enable-buffering) <a href="#http-enable-buffering" id="http-enable-buffering"></a>

`--http-enable-buffering (Boolean)`\
Sets HTTP buffering.\
This option sets the HTTP buffering. Setting this to "true" can cause memory leaks, but can also improve performance in some cases.\
Default value: `false`

### Scripting options[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#scripting-options) <a href="#scripting-options" id="scripting-options"></a>

With these options you can execute a script before starting an operation, and again on completion. Module is loaded automatically, use `--disable-module` to prevent this.

***

> ![](https://prev-docs.duplicati.com/en/latest/icon_important.png) 'before' scripts can set environment variable(s) by writing to stdout. See run-script-exemple for more information.

***

#### run-script-before[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#run-script-before) <a href="#run-script-before" id="run-script-before"></a>

`--run-script-before (Path)`\
Run a script on startup.\
Executes a script before performing an operation. The operation will block until the script has completed or timed out.

#### run-script-after[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#run-script-after) <a href="#run-script-after" id="run-script-after"></a>

`--run-script-after (Path)`\
Run a script on exit.\
Executes a script after performing an operation. The script will receive the operation results written to stdout.

#### run-script-before-required[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#run-script-before-required) <a href="#run-script-before-required" id="run-script-before-required"></a>

`--run-script-before-required (Path)`\
Run a required script on startup.\
Executes a script before performing an operation. The operation will block until the script has completed or timed out. If the script returns a non-zero error code or times out, the operation will be aborted.

#### run-script-timeout[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#run-script-timeout) <a href="#run-script-timeout" id="run-script-timeout"></a>

`--run-script-timeout (Timespan)`\
Sets the script timeout.\
Sets the maximum time a script is allowed to execute. If the script has not completed within this time, it will continue to execute but the operation will continue too, and no script output will be processed.\
Default value: `60s`

### Reporting options[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#reporting-options) <a href="#reporting-options" id="reporting-options"></a>

These options provide support for sending status reports via HTTP messages. Modules sendhttp, sendmail and sendxxmp are loaded automatically, use `--disable-module` to prevent this.

#### send-http-url[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-url) <a href="#send-http-url" id="send-http-url"></a>

`--send-http-url (String)`\
HTTP report url.\
\&#xNAN;_This option is deprecated. For version 2.0.8.106 and newer, use_ `--send-http-form-urls` _and_ `--send-http-json-urls` _instead._

#### send-http-form-urls[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-form-urls) <a href="#send-http-form-urls" id="send-http-form-urls"></a>

`--send-http-form-urls (String)`\
HTTP report urls for sending form-encoded data. This property accepts multiple urls, separated by a semi-colon. All urls will receive the same data. Note that this option ignores the format and verb settings.

#### send-http-json-urls[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-json-urls) <a href="#send-http-json-urls" id="send-http-json-urls"></a>

`--send-http-json-urls (String)`\
HTTP report urls for sending JSON data. This property accepts multiple urls, separated by a semi-colon. All urls will receive the same data. Note that this option ignores the format and verb settings.

#### send-http-message[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-message) <a href="#send-http-message" id="send-http-message"></a>

`--send-http-message (String)`\
The message template.\
This value can be a filename. If the file exists, the file contents will be used as the message. In the message, certain tokens are replaced:

* `%OPERATIONNAME%`\
  The name of the operation, normally `Backup`.
* `%REMOTEURL%`\
  Remote server url.
* `%LOCALPATH%`\
  The path to the local files or folders involved in the operation (if any).
* `%PARSEDRESULT%`
* The parsed result, if the operation is a backup. Possible values are: `Error`, `Warning`, `Success`

All command line options are also reported within `%value%`, e.g. `%volsize%`. Any unknown/unset value is removed.\
Default value: `Duplicati %OPERATIONNAME% report for %backup-name%`\
`%RESULT%`

#### send-http-message-parameter-name[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-message-parameter-name) <a href="#send-http-message-parameter-name" id="send-http-message-parameter-name"></a>

`--send-http-message-parameter-name (String)`\
The name of the parameter to send the message as.\
Default value: `message`

#### send-http-extra-parameters[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-extra-parameters) <a href="#send-http-extra-parameters" id="send-http-extra-parameters"></a>

`--send-http-extra-parameters (String)`\
Extra parameters to add to the http message. I.e. `parameter1=value1&parameter2=value2`.

#### send-http-level[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-level) <a href="#send-http-level" id="send-http-level"></a>

`--send-http-level (Enumeration)`\
The messages to send.\
You can specify one of `Success`, `Warning`, `Error`, `Fatal`. You can supply multiple options with a comma separator, e.g. `Success,Warning`. The special value `All` is a shorthand for `Success,Warning,Error,Fatal` and will cause all backup operations to send a message.\
Values: `Unknown`, `Success`, `Warning`, `Error`, `Fatal`, `All`\
Default value: `all`

#### send-http-any-operation[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-http-any-operation) <a href="#send-http-any-operation" id="send-http-any-operation"></a>

`--send-http-any-operation (Boolean)`\
Send messages for all operations.\
By default, messages will only be sent after a Backup operation. Use this option to send messages for all operations.

#### --send-http-result-output-format[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#-send-http-result-output-format) <a href="#send-http-result-output-format" id="send-http-result-output-format"></a>

`--send-http-result-output-format (Enumeration)`\
Selects the output format for results. Available formats: Duplicati, Json\
Values: `Duplicati`, `Json`\
Default value: `Duplicati`

#### send-mail-to[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-to) <a href="#send-mail-to" id="send-mail-to"></a>

`--send-mail-to (String)`\
Email recipient(s).\
This setting is required if mail should be sent, all other settings have default values. You can supply multiple email addresses separated with commas, and you can use the normal address format as specified by RFC2822 section 3.4.\
Example with 3 recipients: `Peter Sample <peter@example.com>, John Sample <john@example.com>, admin@example.com`

#### send-mail-from[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-from) <a href="#send-mail-from" id="send-mail-from"></a>

`--send-mail-from (String)`\
Email sender.\
Address of the email sender. If no host is supplied, the hostname of the first recipient is used. Examples of allowed formats:

* `sender`
* `sender@example.com`
* `Mail Sender <sender>`
* `Mail Sender <sender@example.com>`

Default value: `no-reply`

#### send-mail-subject[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-subject) <a href="#send-mail-subject" id="send-mail-subject"></a>

`--send-mail-subject (String)`\
The email subject.\
This setting supplies the email subject. Values are replaced as described in the description for `--send-mail-body`.\
Default value: `Duplicati %OPERATIONNAME% report for %backup-name%`

#### send-mail-body[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-body) <a href="#send-mail-body" id="send-mail-body"></a>

`--send-mail-body (String)`\
The message body.\
This value can be a filename. If the file exists, the file contents will be used as the message body.\
In the message body, certain tokens are replaced:

* `%OPERATIONNAME%`\
  The name of the operation, normally `Backup`.
* `%REMOTEURL%`\
  Remote server url.
* `%LOCALPATH%`\
  The path to the local files or folders involved in the operation (if any).
* `%PARSEDRESULT%`\
  The parsed result, if the operation is a backup. Possible values are: `Error`, `Warning`, `Success`.

All command line options are also reported within `%value%`, e.g.`%volsize%`. Any unknown/unset value is removed.\
Default value: `%RESULT%`

#### send-mail-url[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-url) <a href="#send-mail-url" id="send-mail-url"></a>

`--send-mail-url (String)`\
SMTP Url.\
A url for the SMTP server, e.g. `smtp://example.com:25`. Multiple servers can be supplied in a prioritized list, separated with semicolon. If a server fails, the next server in the list is tried, until the message has been sent.\
If no server is supplied, a DNS lookup is performed to find the first recipient's MX record, and all SMTP servers are tried in their priority order until the message is sent.\
To enable SMTP over SSL, use the format `smtps://example.com`. To enable SMTP STARTTLS, use the format `smtp://example.com:25/?starttls=when-available` or `smtp://example.com:25/?starttls=always`. If no port is specified, port 25 is used for non-ssl, and 465 for SSL connections. To force not to use STARTTLS use `smtp://example.com:25/?starttls=never`.

#### send-mail-username[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-username) <a href="#send-mail-username" id="send-mail-username"></a>

`--send-mail-username (String)`\
SMTP Username.\
The username used to authenticate with the SMTP server if required.

#### send-mail-password[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-password) <a href="#send-mail-password" id="send-mail-password"></a>

`--send-mail-password (String)` SMTP Password.\
The password used to authenticate with the SMTP server if required.

#### send-mail-level[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-level) <a href="#send-mail-level" id="send-mail-level"></a>

`--send-mail-level (String)`\
The messages to send.\
You can specify one of `Success`, `Warning`, `Error`, `Fatal`. You can supply multiple options with a comma separator, e.g. `Success,Warning`. The special value `All` is a shorthand for `Success,Warning,Error,Fatal` and will cause all backup operations to send an email.\
Values: `Unknown`, `Success`, `Warning`, `Error`, `Fatal`, `All`\
Default value: `all`

#### send-mail-any-operation[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-any-operation) <a href="#send-mail-any-operation" id="send-mail-any-operation"></a>

`--send-mail-any-operation (Boolean)`\
Send email for all operations.\
By default, mail will only be sent after a Backup operation. Use this option to send mail for all operations.

#### send-mail-result-output-format[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-mail-result-output-format) <a href="#send-mail-result-output-format" id="send-mail-result-output-format"></a>

`--send-mail-result-output-format (Enumeration)`\
Selects the output format for results. Available formats: Duplicati, Json\
Values: `Duplicati`, `Json`\
Default value: `Duplicati`

#### send-xmpp-to[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-to) <a href="#send-xmpp-to" id="send-xmpp-to"></a>

`--send-xmpp-to (String)`\
XMPP recipient email.\
The users who should have the messages sent, specify multiple users separated with commas.

#### send-xmpp-message[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-message) <a href="#send-xmpp-message" id="send-xmpp-message"></a>

`--send-xmpp-message (String)`\
The message template.\
This value can be a filename. If the file exists, the file contents will be used as the message.\
In the message, certain tokens are replaced:

* `%OPERATIONNAME%`\
  The name of the operation, normally `Backup`.
* `%REMOTEURL%`\
  Remote server url.
* `%LOCALPATH%`\
  The path to the local files or folders involved in the operation (if any).
* `%PARSEDRESULT%`\
  The parsed result, if the operation is a backup. Possible values are: `Error`, `Warning`, `Success`.

All command line options are also reported within `%value%`, e.g. `%volsize%`. Any unknown/unset value is removed.\
Default value: `Duplicati %OPERATIONNAME% report for %backup-name%`\
`%RESULT%`

#### send-xmpp-username[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-username) <a href="#send-xmpp-username" id="send-xmpp-username"></a>

`--send-xmpp-username (String)`\
The XMPP username.\
The username for the account that will send the message, including the hostname. I.e. `account@jabber.org/Home`.

#### send-xmpp-password[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-password) <a href="#send-xmpp-password" id="send-xmpp-password"></a>

`--send-xmpp-password (String)`\
The XMPP password.\
The password for the account that will send the message.

#### send-xmpp-level[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-level) <a href="#send-xmpp-level" id="send-xmpp-level"></a>

`--send-xmpp-level (Enumeration)`\
The messages to send.\
You can specify one of `Success`, `Warning`, `Error`, `Fatal`.\
You can supply multiple options with a comma separator, e.g. `Success,Warning`. The special value `All` is a shorthand for `Success,Warning,Error,Fatal` and will cause all backup operations to send a message.\
Values: `Unknown`, `Success`, `Warning`, `Error`, `Fatal`, `All`\
Default value: `all`

#### send-xmpp-any-operation[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-any-operation) <a href="#send-xmpp-any-operation" id="send-xmpp-any-operation"></a>

`--send-xmpp-any-operation (Boolean)`\
Send messages for all operations.\
By default, messages will only be sent after a Backup operation. Use this option to send messages for all operations.

#### send-xmpp-result-output-format[¶](https://prev-docs.duplicati.com/en/latest/06-advanced-options/#send-xmpp-result-output-format) <a href="#send-xmpp-result-output-format" id="send-xmpp-result-output-format"></a>

`--send-xmpp-result-output-format (Enumeration)`\
Selects the output format for results. Available formats: Duplicati, Json\
Values: `Duplicati`, `Json`\
Default value: `Duplicati`
