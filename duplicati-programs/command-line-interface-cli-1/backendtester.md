---
description: This page describes the backend tester tool
---

# BackendTester

Before trusting a storage location with your backups, it's essential to verify its reliability. The built-in Storage Testing Tool helps validate your backup destination through comprehensive integrity testing.&#x20;

The BackendTester binary is called `Duplicati.CommandLine.BackendTester.exe` and `duplicati-backend-tester` on Linux and MacOS. The tool is mostly intended for system administrators that needs to verify a certain storage solution is working as expected, or for developers who are writing a new storage destination provider.

**How the Storage Test Works:**

1. The tool automatically creates test files:
   * Generates files of varying sizes
   * Uses randomized file names
   * Creates the number of files you specify
2. Performs a complete backup simulation:
   * Uploads all test files to your chosen storage location
   * Downloads each file back to verify retrieval
   * Validates file integrity using hash verification
   * Repeats this cycle multiple times for confidence
3. Provides detailed test results:
   * Success/failure status of each operation
   * Upload and download performance metrics
   * Data integrity confirmation

**Customizable Test Parameters:**

* File count: Choose how many test files to generate
* File sizes: Set minimum and maximum file sizes
* Filename parameters: Configure allowed characters
* Test iterations: Specify how many test cycles to run

\


```
Usage: <protocol>://<username>:<password>@<path>
Example: ftp://user:pass@server/folder

Supported backends: aliyunoss,aftp,azure,b2,box,cloudfiles,dropbox,ftp,file,gcs,googledrive,e2,jottacloud,mega,msgroup,onedrivev2,openstack,rclone,s3,ssh,od4b,mssp,sharepoint,sia,storj,tahoe,cos,webdav

 --reruns (Inteiro): The number of test runs to perform
   A number that describes how many times the test is performed
   * default value:: 5
 --tempdir (Caminho): The path used to store temporary files
   The backend tester will use the system default temp path. You can set this option to choose another path.
 --extended-chars (String): A list of allowed extended filename chars
   A list of characters besides {a-z, A-Z, 0-9} to use when generating filenames
   * default value:: -_',=)(&%$#@! +
 --number-of-files (Inteiro): The number of files to test with
   An integer describing how many files to upload during a test run
   * default value:: 10
 --min-file-size (Tamanho): The minimum allowed file size
   File sizes are chosen at random, this value is the lower bound
   * default value:: 1kb
 --max-file-size (Tamanho): The maximum allowed file size
   File sizes are chosen at random, this value is the upper bound
   * default value:: 50mb
 --min-filename-length (Inteiro): The minimum allowed filename length
   File name lengths are chosen at random, this value is the lower bound
   * default value:: 5
 --max-filename-length (Inteiro): The minimum allowed filename length
   File name lengths are chosen at random, this value is the upper bound
   * default value:: 80
 --trim-filename-spaces (Boleano): Trim whitespace from filenames
   A value that indicates if whitespace should be trimmed from the ends of randomly generated filenames
   * default value:: false
 --auto-create-folder (Boleano): Allow automatic folder creation
   A value that indicates if missing folders are created automatically
   * default value:: false
 --skip-overwrite-test (Boleano): Bypass the overwrite test
   A value that indicates if dummy files should be uploaded prior to uploading the real files
   * default value:: false
 --auto-clean (Boleano): Remove any files found in target folder
   A value that indicates if all files in the target folder should be deleted before starting the first test
   * default value:: false
 --force (Boleano): Activate file deletion
   A value that indicates if existing files should really be deleted when using auto-clean
   * default value:: false
```
