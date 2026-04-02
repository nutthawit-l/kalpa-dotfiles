Step

1. Remove default Firefox flatpak

```sh
flatpak uninstall --user -y org.mozilla.firefox
```

2. Make sudo without password 

```sh 
sudo -s

cat <<EOF > /etc/polkit-1/rules.d/40-run0-nopasswd.rules
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
groupadd wheel
usermod -aG wheel tie
```

3. Enter transactional shell

```sh
transactional-update shell

# Remove DVD Repository
zypper rr openSUSE-MicroOS-20260307-0
```

4. Install require packages

```sh
# Remove Falkon Browser
zypper -n rm falkon  

# Add fonts Repository
zypper ar https://download.opensuse.org/repositories/M17N:fonts/openSUSE_Tumbleweed/M17N:fonts.repo && zypper ref && zypper  -n in thai-fonts

# Normal
zypper -n install curl firefox git make neovim python313-podman-compose stow tree yt-dlp  

# KVM Host
zypper -n install curl firefox git make neovim stow tree
zypper -n install --pattern --recommends kvm_server
zypper -n install --pattern --no-recommends kvm_tools
zypper -n install virt-viewer
```

5. Add VSCode Repository

```sh
# Import Microsoft GPG key
rpm --import https://packages.microsoft.com/keys/microsoft.asc

# Add the VS Code Repository
echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\nautorefresh=1\ntype=rpm-md\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" | tee /etc/zypp/repos.d/vscode.repo > /dev/null
```

6. Add Google Antigravity Repository

```sh
zypper ar -f -G https://us-central1-yum.pkg.dev/projects/antigravity-auto-updater-dev/antigravity-rpm antigravity-rpm 
```

7. Install VSCode Antigravity Thai-font

```sh
zypper -n ref && zypper -n install code antigravity 
```

8. Install WaveTerminal

```sh
curl -o wave-terminal.rpm https://dl.waveterm.dev/releases-w2/waveterm-linux-x86_64-0.14.3.rpm
zypper in wave-terminal.rpm
```

9. Make sudo without password 

```sh
sudo cp -v 40-run0-nopasswd.rules /etc/polkit-1/rules.d/
sudo groupadd wheel
sudo usermod -aG wheel $USER
```

10. Install Flatpak Applications

```sh
flatpak install --user -y flathub org.keepassxc.KeePassXC
flatpak install --user -y flathub org.telegram.desktop
flatpak install --user -y flathub com.interversehq.qView
flatpak install --user -y flathub com.google.Chrome
flatpak install --user -y flathub org.kde.okular
```



