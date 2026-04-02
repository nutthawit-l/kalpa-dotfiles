## Configuration Guide for openSUSE Kalpa

openSUSE Kalpa is an immutable operating system that uses run0 (a systemd-run wrapper) as the default tool for privilege escalation instead of traditional sudo. This guide covers how to configure passwordless execution, manage system packages, and set up your development environment.

### Enabling Passwordless Privileges (Polkit)

To allow members of the `wheel` group to execute commands without a password—similar to `sudo NOPASSWD`—you must create a custom **PolicyKit** rule.

1) Create the Polkit rule:

```console
# cat <<EOF > /etc/polkit-1/rules.d/40-run0-nopasswd.rules
polkit.addAdminRule(function(action, subject) {
    return ["unix-group:wheel"];
});

/* Allow members of the wheel group to execute any actions
 * without password authentication, similar to "sudo NOPASSWD:" */
polkit.addRule(function(action, subject) {
    if (subject.isInGroup("wheel")) {
        return polkit.Result.YES;
    }
});
EOF
```

2) Initialize the wheel group and add your user:

```console
# groupadd wheel
# usermod -aG wheel tie
```

3) Re-login for the group changes to take effect.

### Managing System Packages

Because Kalpa uses a read-only root filesystem, system-level packages must be managed via `transactional-update`. This command creates a new read/write snapshot of your system. 

Once you exit the shell, the snapshot is finalized and will become the default boot target after a **reboot**.

1) Enter transactional shell:

```console
$ sudo -s
# transactional-update shell
```

2) Remove unnecessary repositories (e.g., local DVD media):

```console
transactional update# zypper rr openSUSE-MicroOS-20260307-0
```

3) Install essential packages and Thai fonts:

```console
transactional update# zypper ar https://download.opensuse.org/repositories/M17N:fonts/openSUSE_Tumbleweed/M17N:fonts.repo
transactional update# zypper ref 
transactional update# zypper -n in thai-fonts curl git make neovim stow tree
```

4) [Optional] Install Virtualization (KVM/Libvirt):

```console
transactional update# zypper -n install --pattern --recommends kvm_server
transactional update# zypper -n install --pattern --no-recommends kvm_tools
transactional update# zypper -n install virt-viewer
```

5) Exit and apply changes:

```console
transactional update# exit
# reboot
```

### Managing dotfiles with GNU Stow

GNU Stow allows you to manage your configuration files symmetrically by symlinking them from a central repository to your home directory.

**Prerequisites**: Ensure your ~/.ssh directory is configured so you can clone via SSH.

1) Clone the dotfiles repository:

```console
git clone git@github.com:nutthawit-l/kalpa-dotfiles.git ~/.dotfiles
```

2) Deploy configurations using Stow:

```console
stow -v -d ~/.dotfiles/stow_files -t ~ git
```

> **_TIP:_**
> Managing Existing Files with --adopt
>
> If you already have configuration files in your home directory and want to bring them under Stow’s management without manually moving them, use the --adopt flag:
>
>```console
>stow -v -d stow_files -t ~ --adopt git
>```
>
>_Warning:_ This will "pull" the content from your home directory into your dotfiless repository, overwriting the version in your repo with the local version. 
>Use this only when you want to initialize your >repository with your current system settings. Check git diff immediately after using this command to verify the changes.

### Installing Desktop Applications (Flatpak)

On Kalpa, user-facing applications should be installed via Flatpak to keep the base system clean and stable.

```console
flatpak install --user -y flathub org.keepassxc.KeePassXC \
    flatpak install --user -y flathub org.telegram.desktop \
    flatpak install --user -y flathub com.interversehq.qView \
    flatpak install --user -y flathub com.google.Chrome \
    flatpak install --user -y flathub org.kde.okular
```
