# ServerUtil

Description: Server CLI tool for Duplicati

Usage: Duplicati.CommandLine.ServerUtil \[command] \[options]

Options: --password The password to use for connecting to the server \[] --hosturl The host URL to use \[default: http://localhost:8200/] --server-datafolder The server datafolder to use for locating the database \[] --settings-file The settings file to use \[] --insecure Accepts any TLS/SSL certificate (dangerous) \[default: False] --version Show version information -?, -h, --help Show help and usage information

Commands: pause Pauses the server \[] resume Resumes the server list-backups List all backups run Runs a backup login Logs in to the server change-password Changes the server password logout Logs out of the server import Import a backup configuration export Export a backup configuration health Checks the server health endpoint
