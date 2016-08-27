# OpenWrt OPKG Package Manager

The `opkg` utility (an ipkg fork) is a lightweight package manager used to download and install OpenWrt packages from local package repositories or ones located in the Internet.

Opkg attempts to resolve dependencies with packages in the repositories - if this fails, it will report an error, and abort the installation of that package.

## Example

On MediaTek LinkIt Smart 7688 board:

```
-> Add opkg source
root@mylinkit:/# vi /etc/opkg.conf

src/gz chaos_calmer https://downloads.openwrt.org/chaos_calmer/15.05/ramips/mt7628/packages/packages
dest root /
dest ram /tmp
lists_dir ext /var/opkg-lists
option overlay_root /overlay
option check_signature 1

-> Update opkg source
root@mylinkit:/# opkg update

-> Install package
root@mylinkit:/# opkg install mpd-full
```


## Usage

```
usage: opkg [options...] sub-command [arguments...]
where sub-command is one of:

Package Manipulation:
	update			Update list of available packages
	upgrade <pkgs>		Upgrade packages
	install <pkgs>		Install package(s)
	configure <pkgs>	Configure unpacked package(s)
	remove <pkgs|regexp>	Remove package(s)
	flag <flag> <pkgs>	Flag package(s)
	 <flag>=hold|noprune|user|ok|installed|unpacked (one per invocation)

Informational Commands:
	list			List available packages
	list-installed		List installed packages
	list-upgradable		List installed and upgradable packages
	list-changed-conffiles	List user modified configuration files
	files <pkg>		List files belonging to <pkg>
	search <file|regexp>	List package providing <file>
	find <regexp>		List packages whose name or description matches <regexp>
	info [pkg|regexp]	Display all info for <pkg>
	status [pkg|regexp]	Display all status for <pkg>
	download <pkg>		Download <pkg> to current directory
	compare-versions <v1> <op> <v2>
	                    compare versions using <= < > >= = << >>
	print-architecture	List installable package architectures
	depends [-A] [pkgname|pat]+
	whatdepends [-A] [pkgname|pat]+
	whatdependsrec [-A] [pkgname|pat]+
	whatrecommends[-A] [pkgname|pat]+
	whatsuggests[-A] [pkgname|pat]+
	whatprovides [-A] [pkgname|pat]+
	whatconflicts [-A] [pkgname|pat]+
	whatreplaces [-A] [pkgname|pat]+

Options:
	-A			Query all packages not just those installed
	-V[<level>]		Set verbosity level to <level>.
	--verbosity[=<level>]	Verbosity levels:
					0 errors only
					1 normal messages (default)
					2 informative messages
					3 debug
					4 debug level 2
	-f <conf_file>		Use <conf_file> as the opkg configuration file
	--conf <conf_file>
	--cache <directory>	Use a package cache
	-d <dest_name>		Use <dest_name> as the the root directory for
	--dest <dest_name>	package installation, removal, upgrading.
				<dest_name> should be a defined dest name from
				the configuration file, (but can also be a
				directory name in a pinch).
	-o <dir>		Use <dir> as the root directory for
	--offline-root <dir>	offline installation of packages.
	--add-arch <arch>:<prio>	Register architecture with given priority
	--add-dest <name>:<path>	Register destination with given path

Force Options:
	--force-depends		Install/remove despite failed dependencies
	--force-maintainer	Overwrite preexisting config files
	--force-reinstall	Reinstall package(s)
	--force-overwrite	Overwrite files from other package(s)
	--force-downgrade	Allow opkg to downgrade packages
	--force-space		Disable free space checks
	--force-postinstall	Run postinstall scripts even in offline mode
	--force-remove	Remove package even if prerm script fails
	--force-checksum	Don't fail on checksum mismatches
	--noaction		No action -- test only
	--download-only	No action -- download only
	--nodeps		Do not follow dependencies
	--nocase		Perform case insensitive pattern matching
	--size			Print package size when listing available packages
	--force-removal-of-dependent-packages
				Remove package and all dependencies
	--autoremove		Remove packages that were installed
				automatically to satisfy dependencies
	-t			Specify tmp-dir.
	--tmp-dir		Specify tmp-dir.

 regexp could be something like 'pkgname*' '*file*' or similar
 e.g. opkg info 'libstd*' or opkg search '*libop*' or opkg remove 'libncur*'
```


## Reference

