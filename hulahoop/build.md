

You don't need to use ~/Whonix/whonix_build at all. Each step can be done separately. https://www.whonix.org/wiki/Dev/Source_Code_Intro#Introduction Would be unworkable without that feature.
These are the build-steps:

ls -la ~/Whonix/build-steps.d/




Setup build machine according to pages above then run the following commands to create builds:

sudo REPO_PROXY=http://127.0.0.1:3142 ./whonix_build --flavor whonix-gateway-xfce --build --arch amd64 --repo true --target qcow2


sudo SKIP_SCRIPTS+=" 1200_create-debian-packages " SKIP_SCRIPTS+=" 1100_prepare-build-machine " REPO_PROXY=http://127.0.0.1:3142 ./whonix_build --flavor whonix-workstation-xfce --build --arch amd64 --repo true --target qcow2


Optionally add "--connection onion" to force fetching packages from Onion servers.

For Kicksecure replace whonix-gateway-xfce with: kicksecure-xfce / kicksecure-cli



https://github.com/jessfraz/dotfiles/blob/master/.gnupg/gpg-agent.conf
https://www.gnupg.org/documentation/manuals/gnupg/Agent-Options.html

Increase GPG timeout limit to avoid signing failure. Password rentry required after compression. Press 'c' to ignore and continue the signing process during the prepare release step:

echo -e "pinentry-program /usr/bin/pinentry-gnome3\nno-grab\ndefault-cache-ttl 18000\nmax-cache-ttl 86400\nignore-cache-for-signing\npinentry-timeout 86400" | tee ~/.gnupg/gpg-agent.conf



Run command below to sign and compress. Btw Gateway prepare release command does nothing as expected and the Workstation prepare release command will do everything (both gw and ws):

~/Whonix/packages/whonix-developer-meta-files/release/prepare_release --build --target qcow2 --flavor whonix-workstation-xfce


Upload to whonix.org
(server fingerprint: SHA256:tsvKWEuUwbP0vx+vrePlMqbC4qUXn5fscrm/lZLhVrE)
(server folder: /home/hulahoop/libvirt/version-number/vm-name....):

export server="hulahoop@whonix.org:/var/rsync/libvirt"

~/Whonix/packages/whonix-developer-meta-files/release/upload_images





Don't run this script as root. Type sf.net password when prompted:

Upload to sf.net [deprecated]:

export server="hulahoop,whonix-kvm@frs.sourceforge.net:/home/frs/project/whonix-kvm/libvirt"


Creating SSH server pub key via cli (can be also done manually by saving th contents in a .pub file). May need to delete ~/.ssh/known_hosts if previous attempts failed. This step isn;t necessary because the keys are saved in the backed up .ssh:

ssh-keygen -lf ssh_host_ed25519_key.pub

256 SHA256:tsvKWEuUwbP0vx+vrePlMqbC4qUXn5fscrm/lZLhVrE
root@Debian-98-stretch-64-minimal (ED25519)







Patrick:

I forgot how the script looks but the general answer is yes, the
SKIP_SCRIPTS mechanism works too, if done right.

sudo SKIP_SCRIPTS+=" ... "

is different than

SKIP_SCRIPTS+=" ... "

If you set env var SKIP_SCRIPTS as root with sudo, it's lost at the next
invocation of sudo. If a program runs as sudo and terminates, it does
not modify the env of the calling program (shell).

If you set SKIP_SCRIPTS+=" ... " as user and then use 'sudo -E' (which
stands for preserve environment) that should work.

If you use one long command 'sudo SKIP_SCRIPTS+=" something
something-else " whonix_build ...'

starting bash:

$ sudo SKIP_SCRIPTS+=" a " bash
$ sudo SKIP_SCRIPTS+=" a " SKIP_SCRIPTS+=" b " bash

but env var SKIP_SCRIPTS is empty.

Limitation of linux/posix/shell/bash/sudo whomever to blame.

I don't recommend 'sudo SKIP_SCRIPTS+='. The += syntax is a bash
built-in feature for variables. It's not a linux shell thing.

SKIP_SCRIPTS+=" a "
SKIP_SCRIPTS+=" b "



Tor Browser Version: We are setting ' tbb_version="8.5.5" ', i.e. instructing tb-updater to download Tor Browser version 8.5.5 because Whonix 15.0.0.4.9 points to a tb-updater version that hardcodes Tor Browser version  8.5.4 which is no longer available for download which would make the build fail. This version number might need to be updated. Check https://www.torproject.org/projects/torbrowser/RecommendedTBBVersions for latest stable Tor Browser version.

sudo tbb_version="8.5.5" ~/Whonix/whonix_build --flavor whonix-workstation-xfce --target virtualbox --build

