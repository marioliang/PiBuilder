# PiBuilder Change Summary

* 2023-11-17

	- Bump default version of docker-compose installed via script to v2.23.1
	- Prune change-log entries prior to 2023.

* 2023-11-01

	- Rewrite [original build method](./README-ADVANCED.md#originalBuild) material to cater for Bookworm.
	- Update `running_OS_build()` function to allow for change to where `issue.txt` file is stored in Bookworm.

* 2023-10-29

	- Supports the following environment variables in either options.sh, or inline on the call to the 03 and 04 scripts, or exported to the environment before calling those scripts:

		variable                 | default
		-------------------------|----------------------------------------------------
		`IOTSTACK`               | `$HOME/IOTstack`
		`IOTSTACK_URL`           | `https://github.com/SensorsIot/IOTstack.git`
		`IOTSTACK_BRANCH`        | `master`
		`IOTSTACKALIASES_URL`    | `https://github.com/Paraphraser/IOTstackAliases.git`
		`IOTSTACKALIASES_BRANCH` | `master`
		`IOTSTACKBACKUP_URL`     | `https://github.com/Paraphraser/IOTstackBackup.git`
		`IOTSTACKBACKUP_BRANCH`  | `master`

	- `IOTSTACK` allows the installation folder to be something other than `~/IOTstack` while the others permit cloning from forks or copies of the relevant repositories.
	- Explanation of above added to [Advanced README](./README-ADVANCED.md#envVarOverrides) 
	- Removed installation of `sshfs` from 03 script (deprecated upstream).

* 2023-10-22

	- Adds `set_hostname.sh` helper script. This mimics (in part) the approach of `raspi-config` to changing the hostname but augments it with a best-efforts discovery of any local domain name which may have been learned from DHCP. Taken together, this is closer to the result obtained from running the Debian ISO installer. It results in `/etc/hosts` gaining a fully-qualified domain name and that, in turn, means the `hostname -d` and `hostname -f` commands work.

		> for anyone following along at home, the problem with `hostname -d` and `-f` not working because `/etc/hosts` lacked an FQDN was a chance discovery while attempting to add ownCloud to IOTstack. It worked properly in a Debian guest on Proxmox but not on the Pi.

	- Change-of-hostname functionality in the 01 script now implemented by invoking `set_hostname.sh`.


* 2023-10-20

	- Bump default version of docker-compose installed via script to v2.23.0

* 2023-10-06

	- Remove patch added 2023-08-02 which removes pins from Python requirements files using `sed`. Now that [PR 723](https://github.com/SensorsIot/IOTstack/pull/723) has been applied, this is no longer needed.

* 2023-10-03

	- Reset `exim4` paniclog if non-zero length.
	- Expands [Proxmox tutorial](./docs/running-on-proxmox-debian.md) to discuss migrating an IOTstack instance (eg from a Raspberry Pi to a Debian guest).

* 2023-09-23

	- Bump default version of docker-compose installed via script to v2.22.0

* 2023-09-22

	- Incorporates feedback from Andreas on [tutorial](./docs/running-on-proxmox-debian.md).

* 2023-09-17

	- Adds a [tutorial](./docs/running-on-proxmox-debian.md) for installing a Debian guest on a Proxmox&nbsp;VE instance, then running PiBuilder, and then getting started with IOTstack.

* 2023-08-31

	- Add check for pre-existing IOTstack folder at start of 03 script.
	- Bump default version of docker-compose installed via script to v2.21.0
	- Adds `apt update` to start of 03 script to guard against any significant delays between running the 01 and 03 scripts.
	- Adds check to 04 script to detect absence of the IOTstack folder. When doing a full PiBuilder run, the most likely reasons why the IOTstack folder will not exist are:

		* because one or more `apt install «package»` commands failed and the user didn't realise the 03 script is designed so that it can be run over and over until all dependencies are installed, after which 03 then clones IOTstack; or
		* because the 04 script is being run in isolation, just to get docker and docker-compose installed and have the other IOTstack-specific spadework done (docker group, Python prerequistes, etc).

	- Adds `-c` option (3 context lines) to patch generation in [NTP tutorial](docs/ntp.md). Needed so patches will succeed on both Bullseye and Bookworm.

* 2023-08-22

	- Better handling of `hopenpgp-tools` on Bookworm, per [DrDuh PR386](https://github.com/drduh/YubiKey-Guide/pull/386).

* 2023-08-21

	- Adds support for GRUB-based boots. Default patch disables IPv6.
	- Improved handling of python3-ykman (not available on Buster).
	- Bump SQLite version to 3420000.

* 2023-08-13

	- Bump default version of docker-compose installed via script to v2.20.3

* 2023-08-08

	- Tested PiBuilder on Ubuntu Jammy guest under Proxmox.
	- Included mention of Debian Bookworm guest under Proxmox, tested previously.
	
* 2023-08-07

	- Skip locales generation following a patch failure. Ubuntu already has many more locales enable by default than either Raspberry Pi OS or Debian so, if the desired locale is not among them, it's better to have users prepare custom patches that are actually relevant to their specific situations, rather than plough on and regenerate redundant locales (a lot of wasted build time for no gain).

* 2023-08-02

	- After cloning the IOTstack repo in 03 script, create the `.new_install` marker file. This bypasses the somewhat misleading dialog about the repo not being up-to-date (after a clean clone it will, by definition, be in-sync with GitHub).
	- Simulate effect of [PR723](https://github.com/SensorsIot/IOTstack/pull/723) when Bookworm is the running OS. This removes version pins from `requirements-menu.txt`, which is needed for a successful install on Bookworm. 

* 2023-07-31

	- Define meaning and usage of «guillemets» as placeholders.
	- Expand example of calling 01 script with and without «hostname» argument.
	- Sanitise «hostname» argument (lower-case letters, digits and hyphens)
	- Mimic `raspi-config` method of changing hostname so it also works on non-Raspberry Pi (eg Debian, Proxmox VE).
	- Simpler `cp -n` syntax for copying `resolvconf.conf`.

* 2023-07-30

	- Expand [Configuring Static IP addresses on Raspbian](./docs/ip.md) to clarify use of `domain_name_servers` field in `dhcpcd.conf`. This follows on from a misunderstanding revealed in a discussion on [Discord](https://discord.com/channels/638610460567928832/638610461109256194/1134819901626925159).
	- Adds how-to for setting up a Pi as an authoritative DNS server.

* 2023-07-19

	- Bump default version of docker-compose installed via script to v2.20.1
	- And later the same day to v2.20.2

* 2023-07-17

	- Major documentation restructure:

		- Rather than cloning PiBuilder onto your support host, then copying the contents of `~/PiBuilder/boot` to the `/boot` partition such that scripts are run like this:

			```
			$ /boot/scripts/01_setup.sh «new host name»
			$ /boot/scripts/02_setup.sh
			…
			```

			the focus is on cloning PiBuilder onto the Raspberry Pi and running the scripts like this:
		
			```
			$ ~/PiBuilder/boot/scripts/01_setup.sh «new host name»
			$ ~/PiBuilder/boot/scripts/02_setup.sh
			…
			```

			The older mechanism still works but the newer mechanism is more straightforward when trying to use PiBuilder in situations where the `/boot` partition does not exist (eg Proxmox, Parallels, non-Pi hardware).
			
			The new approach can also be used with customised builds. Instead of cloning PiBuilder onto your Pi from GitHub, you:
			
			1. Clone PiBuilder onto your support host from GitHub.
			2. Customise PiBuilder on your support host.
			3. Clone the customised instance onto your Pi from your support host.

		- Main README simplified and aimed at first-time users.
		- Customisation documentation moved to "advanced" README.
		- Acknowledgement: Andreas suggested this change of approach.

	- Explain how to enable SAMBA sharing of home directory.
	- Rewrite VNC documentation and explain how to install TightVNC as an alternative to RealVNC.

* 2023-07-12

	- Bump default version of docker-compose installed via script to v2.20.0
			
* 2023-07-03

	- First pass at supporting Debian Bookworm. A test build can start with:

		1. Go to the [Raspberry Pi OS downloads](https://www.raspberrypi.com/software/operating-systems/) page.
		2. Find the "Raspberry Pi OS (64-bit)" grouping (half way down the page).
		3. Download "Raspberry Pi OS with desktop" May 3rd 2023.

		The expected filename is `2023-05-03-raspios-bullseye-arm64.img.xz`
		
	- To build based on Bookworm:

		```
		$ DEBIAN_BOOKWORM_UPGRADE=true /boot/scripts/01_setup.sh {«hostname»}
		```
		
* 2023-07-01

	- Bump default version of docker-compose installed via script to v2.19.1

* 2023-06-22

	- Bump default version of docker-compose installed via script to v2.19.0

* 2023-05-18

	- Bump default version of docker-compose installed via script to v2.18.1

* 2023-05-17

	- Bump default version of docker-compose installed via script to v2.18.0

* 2023-05-15

	- Fix bug in tidyPATH() function.

* 2023-04-27

	- Support third (optional, boolean) argument to `try_patch()`. If true, the function will return success even if the patching operation fails.
	- Better support for `LOCALE_LANG` on non-Pi hosts. If the language defined by `LOCALE_LANG` is active in `/etc/locale.gen` (either because that's the default or because a patch was successful in making it active) then that language will be made the default. Otherwise the setting will be skipped with a warning.
	- All language setting now occurs in 02 script.
	- Add SSHD and SSH restarts to 01 script to try to improve end-of-script reboot reliability. 

* 2023-04-25

	- Rename SQLite installation script from `06_setup.sh` to `install_sqlite.sh` in the `helpers` subdirectory.
	- Support for `LOCALCC` and `LOCALTZ` withdrawn in favour of setting via Raspberry Pi Imager.

* 2023-04-21

	- Bump default version of docker-compose installed via script to v2.17.3

* 2023-04-17

	- Bump default version of SQLite to 3410200

* 2023-03-27

	- Bump default version of docker-compose installed via script to v2.17.2

* 2023-03-25

	- Bump default version of docker-compose installed via script to v2.17.1

* 2023-03-24

	- Bump default version of docker-compose installed via script to v2.17.0

* 2023-02-09

	- Bump default version of docker-compose installed via script to v2.16.0
	- Bump SQLite to version 3400100
	- Adds `VMSWAP=custom` to support custom VM configurations

* 2023-01-10

	- Bump default version of docker-compose installed via script to v2.15.1 (for S474N)

* 2023-01-07

	- Bump default version of docker-compose installed via script to v2.15.0 (for S474N)

* 2023-01-04

	- add capability for installing `/etc/docker/daemon.json`
	- Bump default version of docker-compose installed via script to v2.14.2 
