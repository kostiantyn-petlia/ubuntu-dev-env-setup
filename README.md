# Ubuntu 24.04 LTS: PHP Web Development Environment Setup

**version 0.9.0**

The guide for Ubuntu development environment setup.

We suppose to use work and personal git repositories with different SSH keys and user emails.

---

- [Chrome](#chrome)
- [Curl](#curl)
- [PHP](#php)
- [PhpStorm](#phpstorm)
    - [Fix the case hotkey](#fix-the-case-hotkey)
    - [Default project directory](#default-project-directory)
- [Git](#git)
    - [Name, email and line ending](#name-email-and-line-ending)
    - [Cleanup alias](#cleanup-alias)
    - [Personal name and email](#personal-name-and-email)
- [SSH keys](#ssh-keys)
    - [Permissions of keys](#permissions-of-keys)
- [Composer](#composer)
- [Node](#node)
    - [Node version manager](#node-version-manager)
    - [Node versions](#node-versions)
    - [Yarn](#yarn)
- [Docker](#docker)
    - [Docker post-installation steps](#docker-post-installation-steps)
    - [Docker compose](#docker-compose)
- [VPN](#vpn)
  - [Network Manager plugins](#network-manager-plugins)
  - [TunnelBear Chrome extension](#tunnelbear-chrome-extension)
- [Preparing to migrate to another computer](#preparing-to-migrate-to-another-computer)
    - [Archive of the Home folder](#archive-of-the-home-folder)
    - [Save and sync PhpStorm settings](#save-and-sync-phpstorm-settings)
    - [Save all open Chrome tabs](#save-all-open-chrome-tabs)
- [Known Ubuntu 24.04 LTS issues](#known-ubuntu-2404-lts-issues)
    - [Screen lock issue](#screen-lock-issue)
    - [Thumbnails issue](#-thumbnails-issue)

---

Do we migrate form an existing computer?
See [Preparing to migrate to another computer](#preparing-to-migrate-to-another-computer).

Update the package lists and then upgrade all installed packages on an Ubuntu system to their latest versions,
ensuring the system is up-to-date with security patches and bug fixes.

```
sudo apt update && sudo apt upgrade
```

---

## Chrome

[Link](https://askubuntu.com/questions/1514599/how-do-i-install-google-chrome-on-ubuntu-24-04)

If there is any issue preventing the GUI from loading, we can try starting the google-chrome application from terminal:

```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
google-chrome
```

## Curl

[Link](https://www.cyberciti.biz/faq/how-to-install-curl-command-on-a-ubuntu-linux/)

```
sudo apt install curl
curl --version
```

## PHP

[Link](https://ubuntu.com/server/docs/how-to-install-and-configure-php)

```
sudo apt install php libapache2-mod-php
php -v
```

## PhpStorm

[Link](https://www.jetbrains.com/help/phpstorm/installation-guide.html#snap)

```
sudo snap install phpstorm --classic
```

### Fix the case hotkey

[Link](https://superuser.com/a/1392682/1338402)

The Ctrl+Shift+U hotkey of PhpStorm changes selected text to lowercase/uppercase,
and it conflicts with the Ubuntu unicode hotkey.
Change the unicode hotkey on the terminal, for example to Ctrl + Shift + Super + u:

```
# Get
gsettings get org.freedesktop.ibus.panel.emoji unicode-hotkey
# Set
gsettings set org.freedesktop.ibus.panel.emoji unicode-hotkey "['<Control><Super><Shift>u']"
```

### Default project directory

Set `home/<username>/projects`:

`PhpStorm > Settings > Appearance & Behavior > System Settings > Default directory`.

## Git

[Link](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

```
sudo apt install git-all
git -v
```

### Name, email and line ending

[Link](https://kuantingchen04.github.io/line-endings/)

```
git config --global user.name "John Doe"
git config --global user.email "john.doe@company.com"
git config --global core.autocrlf input
git config --list # results
```

### Cleanup alias

The alias for deleting merged local branches.
The next branches are ignored: master, main, dev, develop, development, staging, stage.

```
git config --global alias.cleanup "!git branch --merged | grep  -v '\\*\\|master\\|main\\|dev\\|develop\\|development\\|staging\\|stage' | xargs -n 1 git branch -d"
git cleanup # usage
```

The alt method is to add into the `home/<username>/.gitconfig` file:

```
[alias]
  cleanup = "!git branch --merged | grep  -v '\\*\\|master\\|main\\|dev\\|develop\\|development\\|staging\\|stage' | xargs -n 1 git branch -d"
```

### Personal name and email

If we are going to commit to work and personal repositories we need to set different emails for the git user.
All personal project will be placed in the `home/<username>/projects/personal/` directory.

We need to create the `home/<username>/.gitconfig-personal` file with personal email:

```
[user]
  name = John Doe
  email = john.doe@gmail.com
```

And update the default global git config `home/<username>/.gitconfig` by adding to the end:

```
[includeIf "gitdir:~/projects/personal/"]
  path = ~/.gitconfig-personal
```

## SSH keys

[Link-1](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys),
[Link-2](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

We can use existing keys or generate new ones.

```
# Checking for existing SSH keys
ls -la ~/.ssh

# Generating a new SSH key
ssh-keygen -t ed25519 -C "john.doe@example.com"

# Start the SSH agent
eval $(ssh-agent -s)

# Add our keys to the SSH agent
ssh-add ~/.ssh/ed25519
ssh-add ~/.ssh/ed25519_personal

# Create or edit the SSH config file
nano ~/.ssh/config
```

The `~/.ssh/config` file content to use work and personal keys in the same time:

```
Host company-github
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519
  User git
  IdentitiesOnly yes

Host personal-github
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519_personal
  User git
  IdentitiesOnly yes
```

Verify the connection using the keys:

```
# Verify the connection
ssh -T git@company-github
ssh -T git@personal-github
```

Clone repos:

```
# Company repo
git clone git@company-github:company/repo.git

# Personal repo
git clone git@personal-github:username/repo.git
```

### Permissions of keys

[Link](https://superuser.com/a/215506/1338402)

The `.ssh` folder contains sensitive data. Private keys should be readable by the user but not
accessible by others (read/write/execute).

```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/*
chmod 644 ~/.ssh/*.pub
```

## Composer

[Link](https://getcomposer.org/download/)

```
sudo apt install composer
php composer.phar -V
```

```
# Or the last version
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

## Node

To use LTS versions only is a good point.

### Node version manager

[Link-1](https://nodejs.org/en/download/package-manager),
[Link-2](https://github.com/nvm-sh/nvm)

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm -v
```

### Node versions

```
nvm install 18
nvm install 20
nvm list
nvm alias default 20
nvm use default

node -v
npm -v
```

### Yarn

[Link](https://classic.yarnpkg.com/en/docs/install/#debian-stable)

```
npm install --global yarn
yarn -v
```

## Docker

[Link](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Docker post-installation steps

[Link](https://docs.docker.com/engine/install/linux-postinstall/)

```
# List of all groups
getent group

# Add the docker group if it is needed
sudo groupadd docker

# Add our user to the docker and www-data groups
sudo usermod -aG docker $USER
sudo usermod -aG www-data $USER
```

### Docker compose

[Link-1](https://github.com/docker/compose),
[Link-2](https://medium.com/@meghasharmaa704/installing-docker-compose-d6233d8bf3c3)

```
sudo apt-get update
sudo apt-get install docker-compose-plugin
docker compose version
```

or [Link-3](https://stackoverflow.com/questions/49839028/how-to-upgrade-docker-compose-to-latest-version)

```
sudo rm /usr/local/bin/docker-compose
curl + grep
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | grep -Po '"tag_name": "\K.*\d')
DESTINATION=/usr/local/bin/docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
sudo chmod 755 $DESTINATION
```

## VPN

### Network Manager plugins

We can install VPN plugins for default Network Manager and use them in `Settings > Network > VPN > +`
or `Top bar > VPN`.

```
# Simple, but enough
sudo apt install network-manager-fortisslvpn network-manager-fortisslvpn-gnome

# Advanced
sudo apt install network-manager-openconnect network-manager-openconnect-gnome

# After installing the plugin, restart Network Manager
sudo systemctl restart NetworkManager
```

### TunnelBear Chrome extension

[Link](https://chromewebstore.google.com/detail/tunnelbear-vpn/omdakjcmkglenbhjadbccaookpfjihpa?hl=en)

We can use this free Chrome extension to change our county fast and simple.

---

## Preparing to migrate to another computer

### Archive of the Home folder

We need to copy our files form the `/home/ourname`.

Before archiving our home folder, it's generally a good idea to ensure that the permissions are set correctly.
Here's how we can adjust the permissions:

```
# Permissions on directories:
find /home/ourname -type d -exec chmod 755 {} \;

# Permissions on files:
find /home/ourname -type f -exec chmod 644 {} \;

# If we have any scripts or programs that need to be executable:
find /home/ourname -type f -name "*.sh" -exec chmod 755 {} \;
```

We can use GUI or 7zip to archive the home directory.

```
sudo apt update
sudo apt install p7zip-full
7z # see help
```

Old computer:

```
7z a /path/to/backup/home_backup.7z /home/ourname
```

New computer:

```
7z x /path/to/backup/home_backup.7z
```

**Pay attention!** In the end we need to restrict permissions for sensitive data like the `.ssh` directory.

### Save and sync PhpStorm settings

Old computer:

Export settings as a file and copy in the new computer.

```
PhpStorm -> File -> Manage IDE Settings -> Export Settings...
```

Sync them with our JetBrains account too.

```
PhpStorm -> File -> Manage IDE Settings -> Settings Sync...
```

New computer:

Sync or import settings.

### Save all open Chrome tabs

[Link](https://superuser.com/a/1280951/1338402)

We can use tab groups for better results.

Old computer:

- Right click on the tab bar, and select `Bookmark all tabs...`.
- Enter a name for the new folder that will be created containing a bookmark for each open tab.
- Pick a location in our bookmarks tree.
- Check that the sync of chrome settings is enabled.

New computer:

- Open Chrome and login in into a Chrome account.
- Right click on the folder on the browser panel and `Open all (N)`.

## Known Ubuntu 24.04 LTS issues

### Screen lock issue

Screen lock logs me out completely.
[Link](https://askubuntu.com/a/1518292/1681582)

### Thumbnails issue

Some image thumbnails no longer displayed.
[Link](https://bugs.launchpad.net/ubuntu/+source/nautilus/+bug/2047256)
