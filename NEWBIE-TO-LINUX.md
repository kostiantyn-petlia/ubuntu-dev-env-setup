# Newbie to Linux

**Welcome to Linux!**

This guide is designed to help newcomers get started with Linux.

---

- [Newbie Challenges](#newbie-challenges)
- [Essential terminal commands](#essential-terminal-commands)
- [Command line interface (Terminal)](#command-line-interface-terminal)
- [Permissions and user management](#permissions-and-user-management)
- [Correct file permissions for WordPress](#correct-file-permissions-for-wordpress)
    - [Permissions for local environment](#permissions-for-local-environment)
    - [Permissions for production environment](#permissions-for-production-environment)

---

## Newbie challenges

New Linux users often face several challenges:

- **Command line interface**: Many Linux operations require using the terminal, which can be daunting for those used to
  graphical interfaces.
- **Permissions and user management**: Understanding and managing file permissions and user accounts can be complex.
- File system structure: The Linux file system hierarchy is different from Windows, which can be confusing.
- Software installation: The process of installing software differs from Windows or macOS, often involving package
  managers.
- Hardware compatibility: Some hardware may not have Linux drivers, causing functionality issues.
- Different software alternatives: Familiar programs may not be available, requiring users to learn new applications.
- Choice overload: Many Linux distributions and desktop environments are available, making it hard to choose.
- Troubleshooting: Solving problems may require more technical knowledge compared to other operating systems.

## Essential terminal commands

Here's a list of essential terminal commands for daily Linux usage:

- `ls` - List directory contents
- `cd` - Change directory
- `pwd` - Print working directory
- `mkdir` - Create a new directory
- `rm` - Remove files or directories
- `cp` - Copy files or directories
- `mv` - Move or rename files/directories
- `cat` - Display file contents
- `grep` - Search for patterns in files
- `chmod` - Change file permissions
- `sudo` - Execute a command as a superuser
- `apt-get` or `apt` - Package management (Debian-based systems)
- `yum` or `dnf` - Package management (Red Hat-based systems)
- `man` - Display manual pages for commands
- `top` - Display system processes
- `df` - Show disk space usage
- `du` - Estimate file space usage
- `ping` - Test network connectivity
- `ssh` - Secure shell for remote access
- `tar` - Archive files

These commands cover basic file management, system information, networking, and package management.

## Command line interface (Terminal)

The terminal is our friend. 

Use `Ctrl+Alt+T` to open Terminal.

To be continued...

## Permissions and user management

To be continued...

## Correct file permissions for WordPress

[Link-1](https://stackoverflow.com/questions/18352682/correct-file-permissions-for-wordpress),
[Link-2](https://developer.wordpress.org/advanced-administration/security/hardening/#file-permissions),
[Link-3](https://developer.wordpress.org/advanced-administration/server/file-permissions/)

### Permissions for local environment

The simplest, **but not the safest** way (it's not so important for local environment) to set permissions is to run next
commands:
(Replace 'ourusername' to the correct username.)

```
# Visible files and directories owner
chown www-data:ourusername -R ../*

# Hidden files and directories owner, except the special directories . and ..
chown www-data:ourusername -R ../.[^.]*

# Change directory permissions rwxrwxr-x
find ../ -type d -exec chmod 775 {} \;

# Change file permissions rw-rw-r–
find ../ -type f -exec chmod 664 {} \;
```

For reuse, we can create a bash `permissions.sh` script file in the project root and run it on Terminal:

```
nano permissions.sh       # Create, write & save a file
sudo bash permissions.sh  # Run it
```

The `permissions.sh` file:

```
#!/bin/bash

# Echo everything
set -x

# Visible files and directories owner
chown www-data:ourusername -R ../*

# Hidden files and directories owner, except the special directories . and ..
chown www-data:ourusername -R ../.[^.]*

# Change directory permissions rwxrwxr-x
find ../ -type d -exec chmod 775 {} \;

# Change file permissions rw-rw-r–
find ../ -type f -exec chmod 664 {} \;
```

### Permissions for production environment

To be continued...
