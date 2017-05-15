# nihpkg
Package manager for my own Linux from scratch distro (nihOS)

Right now this project is just an idea, but it's a nice one and I hope that I one day finish it.

## Features
* thorough documentation of the build process
  * configuration, build and installation logs are kept
  * a record is made of the system and environment states during each step, enabling things like listing all packages built with an older version of glibc
* few dependencies so it can be used early in the LFS build process
  * yaml-cpp
  * argparse
* supports fake root so it can also be used before chroot in the LFS build process
* creates a user for each package and compiles and installs as that user
  * no files can get accidentally (or maliciously) overwritten
  * limit use of setuid by packages
  * easily see to which package a certain file belongs
* fine-grained control over the build process
* use the simplest tools available, so it remains possible to do things manually where desired
* package specification in yaml format

### Dependency resolution
I don't plan on doing any. It would however be convenient to list dependencies, so search would be more useful (e.g. I upgraded a package and need to know what to recompile), but it is unlikely that I would be able to invest enough time to list all dependencies for every package, and half a feature may be more harmful than no feature at all. Although this is information we might be able to copy from other package managers. I am undecided about this.

### Trust
I don't plan on adding package signing or signature verification. I have two good reasons for this.

1. We won't be providing archives with the actual source code, nor will we provide binaries.
2. I expect an LFS user to at least inspect the package specification and the commands that it will execute. To encourage this, and to make it easier, I will do my best to make this a readable format with only a moderate amount of code in it.

## Inspiration (in order of importance)
* [user-based package management from Linux from scratch](http://www.linuxfromscratch.org/hints/downloads/files/more_control_and_pkg_man.txt)
* Slackware package management (slackbuilds, slackpkg{,+}, sbopkg)
* guix

## Interface
### System
Ensure correct permissions on installation directories. Maybe some options for bootstrapping an installation? If we do decide tracking dependencies, we could do a system integrity check that reports packages being compiled with different versions of their dependencies than are currently installed.

### Repository
Managing the repository. It is kept in version control, and this can be as simple as doing a pull command. Ideally it would be possible to use multiple repositories, and pick and choose, but that is something for future versions.

### Info
Functions for gathering package information. This includes listing ALL package files and information about the environment that the package was built in.

### CheckUpdates
Ideally the package specification would include a way to check what the latest stable source version is. We could then check it to see if we have the latest version. This might have to be coded as a separate service that both fetches the source version and offers a download link for the latest version.

### Search
Search both repository and installed packages. Should incorporate advanced filter functions that include environment information (e.g. any package compiled with kernel<=3.10).

### Init
Create necessary user, group and user directories.

### Get
Create directory structure for specific version of package, and download or copy source archive. In the future this would also support installing from version control. Should be able to verify checksums.

### Configure
Run configuration if this package includes that step.

### Build
Run configure first if the environment or specification has changed since last execution. Run compilation if this package includes that step.

### Install
Run build first if the environment or specification has changed since the last execution.

Package installation. If a version of this package is already present in the system, it is uninstalled first, unless the package specification indicates this is a multi-versioned package. After installation the user is prompted about possible overwrites in the /etc directory which is kept in version control. Logs are then scanned for possible problems with the user package management, and the user is informed about them.

### Uninstall
Run uninstall if the package specifies one. Not all packages include a uninstallation option, in that case the user can use purge.

### Purge
Remove all files owned by package user except those in the package user's home directory. User is given the option to spare certain directories like /etc.

### Remove
This option removes anything and everything from this package, including the source directories and the user.
