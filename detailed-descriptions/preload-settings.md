---
description: This page describes how Preload settings are applied
---

# Preload settings

The preload settings allow configuring machine-wide or enterprise-wide default settings with a single file. Because of this usecase, all settings are applied only if they are not already present. This means a commandline argument could be set up to change the default blocksize, but if the user has applied another setting via the commandline or parameters-file, the preload setting has no effect.

For single-machine users, the preload settings are a convenient way to change the arguments passed to either [TrayIcon](../duplicati-programs/trayicon.md), [Server](../duplicati-programs/server.md), or [Agent](../duplicati-programs/agent.md), without needing to edit shortcuts or service files.

To support different ways of deploying the settings file, 3 locations are checked:

* `%CommonApplicationData%\Duplicati\preload.json` (see this [SO thread](https://stackoverflow.com/questions/895723/environment-getfolderpath-commonapplicationdata-is-still-returning-c-docum) for details, but usually `/usr/share/Duplicati/preload.json` and `C:\ProgramData\Duplicati\preload.json`)
* Inside the installation folder
* The file pointed to by `DUPLICATI_PRELOAD_SETTINGS`

For security reasons, all these paths are expected to be writeable only by Administrator/root so unprivileged users cannot modify the values. If the settings contains secrets, make sure that only the relevant users can read them.

The loading of the files is default silent, even if the parsing fails, but the environment variable `DUPLICATI_PRELOAD_SETTINGS_DEBUG=1` will toggle loader debug information to help investigate issues.

The implementation here follows the format:

```json
{
  "env": {
    "*": {
      "TEMP": "/mnt/tmp",
      "LOGGING": "false"
   },
   "tray": {
      "LOG": "1"
    },
    "server": {
        "DUPLICATI__WEBSERVICE_ALLOWED_HOSTNAMES": "m1"
    }
  },

  "db": {
    "server": {
      "--compression-module": "zip",
      "--send-http-result-output-format": "Json"
     }
  },

  "args": {
    "tray": [ "--hosturl=http://m1:8299" ],
    "server": [ "--webservice-port=8299" ]
  }
}
```

The file has 3 sections that are all similar and all optional: `env`, `db`, and `args`. Each section can apply to all executables (`*`) or a specific executable. The executable names can be seen in the source, but the most common ones are `tray` and `server`.

In the case where the `*` section and specific executable has the same variable, the specific one is used. If multiple settings files are found, they are loaded in the order described above. Here the last file loaded will be able to overwrite the others. The `*` settings are collected from all three files, as are the executable specific options, and only after all parsing is done, are the specific executable options applied (see below for an example).

Note that some executables will load others, such that TrayIcon, Service, and WindowsServer will load Server.

## Environment variables - `env`

The `env` section contains environment variables that are applied inside the process, after starting. Each entry under an executable is a key-value pair, where the key is the name of the environment variable, and the value will be the contents of the environment variable.

The environment variables are only set if they are not already set, allowing a custom base set, but prefers local machine variables.

In the case where one binary loads another, the starting application environment variables are applied first, and then any unset environment variables are applied for the loaded executable.

## Database settings - `db`

For the `db` section it is possible to use `*` but the settings are currently only applied when running the server, so for future compatibility this section should use `server` only. The settings under an executable in the `db` section are automatically prefixed with `--` to ensure they are valid options and are saved as the "application wide" settings, also visible in the UI under Settings -> Advanced Options.

The settings here are applied to the database if they are changed, meaning a change to the settings will overwrite settings the user has already applied. This check is performed on startup.

The database settings are not passed on from a binary when it loads another, so the only database settings that are loaded are done by Server, even if any are supplied by `tray` (may change in the future).

## Commandline arguments

The commandline arguments supports both the `*` and specific executable name. The arguments are expected to be switches in the format `--name=value` but can be any commandline argument. The general logic in Duplicati is that "last option wins", so the resolver logic for that is applied to try to get the most logical combination of arguments.

## Resolution with conflicts

If the following fragment is supplied:

```json
"env": {
  "*": {
    "E1": "a",
    "E2": "b"
  },
  "tray": {
    "E1": "c",
    "E3": "d"
  }
}
```

The Server executable will get the settings from `*` and the TrayIcon will get the values: "`E1=c E2=b E3=d`".

If the above fragment is found in the first file, but this fragment is found in a later file:

```json
"env": {
  "*": {
    "E3": "f"
  },
  "tray": {
    "E1": "g"
  }
}
```

First the `*` variables are collected, giving "`E1=a E2=b E3=f`", then the `tray` variables give "`E1=g E3=d`", and then they are combined to give "`E1=g E2=b E3=d`" for `tray`.

The same combination logic is applied for both the `db` and `args` sections, but since the `args` section are not key-value pairs, and their order matter, it is done by collecting the arguments first, and then reducing them:

```json
"args": {
  "*": ["--test=1", "--abc=123"],
  "server": ["--xyz=z", "--test=1", "--test=2"]
}
```

In this case the arguments are collected, with `*` first, then the executable specifics, giving:

```json
["--test=1", "--abc=123", "--xyz=z", "--test=1", "--test=2"]
```

Since this contains 3 options named `--test`, they are reduced and appended so it ends up with:

```json
["--abc=123", "--xyz=z", "--test=2"]
```

The intention here is to stay as close as possible to the original line that was entered. If the commandline arguments already contains `--test`, the values are not applied.
