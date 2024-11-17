---
description: >-
  This page describes what versions of the database is used in each version of
  Duplicati
---

# Database versions

Duplicati use a database named `Duplicati-server.sqlite` to keep track of all configured backups. This database contains sensitive information, such as connection credentials and encryption passphrases. To protect the information in the database, it is highly recommeded that you set up an encryption key. Setting a key will ensure that all sensitive fields are encrypted and protected against a future leak of the database.

Besides the server database, Duplicati also has a [local database](../detailed-descriptions/the-local-database.md) which is created for each configured backup. The local database is essentially a cache of what data is stored remotely, so the backup and restore operations can make queries without needing access to the remote destination. This database can be recreated from the remote data so it should generally not be backed up It contains the paths from the local database, which may be sensitive information.

## Server database

The server database was updated in these versions:

Version 5 and less: 2.0.4.23 and earlier

Version 6: Duplicati 2.0.8.1

Version 8: Duplicati 2.1.0.0

## Local database

The local database was updated in these versions:

Version 10: 2.0.5.1

Version 11: 2.0.6.3

Version 12: 2.0.8.1

Version 14: 2.1.0.0
