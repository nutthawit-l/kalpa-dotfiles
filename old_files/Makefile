create-template-opensuse-tumbleweed:
	podman build -t opensuse-tumbleweed-template -f Containerfile-OpenSUSE-Tumbleweed-Template
create-opensuse-tumbleweed:
	#distrobox rm -f opensuse-tumbleweed && distrobox create -i localhost/opensuse-tumbleweed-template -n opensuse-tumbleweed
	distrobox assemble create --replace --file opensuse-tumbleweed.ini
enter-opensuse-tumbleweed:
	distrobox enter opensuse-tumbleweed

base:
	zypper --non-interactive remove --no-confirm falkon
	zypper --non-interactive install curl git gh make neovim stow tree
	zypper --non-interactive addrepo --no-gpgcheck https://download.opensuse.org/repositories/M17N:fonts/openSUSE_Tumbleweed/M17N:fonts.repo
	zypper refresh 
	zypper --non-interactive install thai-fonts
	zypper --non-interactive install distrobox distrobox-bash-completion

desktop: base install_antigravity install_firefox install_vscode
	
c_devel: base
	zypper --non-interactive install --type pattern --no-recommends devel_C_C++

kvm: base
	zypper --non-interactive install --recommends kvm_server
	zypper --non-interactive install --no-recommends kvm_tools
	zypper --non-interactive install virt-viewer

install_antigravity:
	zypper --non-interactive addrepo --refresh --no-gpgcheck https://us-central1-yum.pkg.dev/projects/antigravity-auto-updater-dev/antigravity-rpm antigravity-rpm
	zypper refresh
	zypper in antigravity

install_firefox:
	zypper --non-interactive install MozillaFirefox

install_vscode: 
	rpm --import https://packages.microsoft.com/keys/microsoft.asc
	echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\nautorefresh=1\ntype=rpm-md\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" | tee /etc/zypp/repos.d/vscode.repo > /dev/nullk
	zypper --non-interactive install code

flatpak_install_apps:
	flatpak install --user -y flathub org.keepassxc.KeePassXC
	flatpak install --user -y flathub org.telegram.desktop
	flatpak install --user -y flathub com.interversehq.qView
	flatpak install --user -y flathub com.google.Chrome
	flatpak install --user -y flathub org.kde.okular
	flatpak uninstall --user -y org.mozilla.firefox
