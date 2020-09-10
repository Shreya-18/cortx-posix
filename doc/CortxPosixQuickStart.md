# Cortx-Posix QuickStart guide
This is a step by step guide to get Cortxfs ready for you on your system for file access protocol, NFS.
Before cloning, you need RHEL 7.7 machine.

Note: Following instructions are applicable only for single node setup.

## Setting Dev enviornment
  ### Install Development Tools
  Install developement tools packages

  * `yum group list`
  * `yum group install "Development Tools"`
  
  ### Install Motr (using root user)
  
  Before setting up Motr below rpms are required on the system. If rpms are not installed refer [Cortx-Motr-Quick-Start-Guide](https://github.com/Seagate/cortx-motr/blob/dev/doc/Quick-Start-Guide.rst)
  ```
  cortx-motr
  cortx-motr-devel
  cortx-motr-debuginfo
  ```
  
  * Enable Motr-related systemd services
    * `m0singlenode activate`
  * Create the storage for Motr
    * `m0setup -Mv -s128`
  * Start Motr services
    * `m0singlenode start`
  * Check status of Motr services
    * `m0singlenode status`	
	```
	### Global state --------------------------------
	 State:                  loaded
	 Autostart:              disabled
	 LNet:                   active    Wed 2020-09-02 02:59:13 MDT
	 LNet Network ID:        10.230.242.169@tcp

	### Kernel --------------------------------------
	 motr-kernel             active    Wed 2020-09-02 02:59:13 MDT
	   m0ctl                  44181  0
	   m0tr                 5283132  7 m0ctl
	   galois                 22944  1 m0tr
	   lnet                  586244  3 m0tr,ksocklnd

	### Servers -------------------------------------
	 motr-server-ha          active    Wed 2020-09-02 02:59:13 MDT    PID:4536
	 motr-server-confd       active    Wed 2020-09-02 02:59:14 MDT    PID:4693
	 motr-server@mds         active    Wed 2020-09-02 02:59:14 MDT    PID:4877
	 motr-server@ios1        active    Wed 2020-09-02 02:59:15 MDT    PID:5059
	 motr-server@cas1        active    Wed 2020-09-02 02:59:15 MDT    PID:5251

	### Trace ---------------------------------------
	```

  ### Install pre-requisites packages for NFS Ganesha

  To install pre-requisites packages for NFS Ganesha refer to [install-pre-requisites-packages-for-nfs-ganesha](https://github.com/Seagate/cortx-posix/blob/dev/doc/CortxPosixNFSGanesha.md#install-pre-requisites-packages-for-nfs-ganesha)
  
  ### Install pre-requisites packages for CORTX-FS
  
  * Install following necessary packages 
    * `sudo yum install bison cmake flex krb5-devel krb5-libs userspace-rcu-devel libini_config libini_config-devel jemalloc-devel json-c-devel dbus-devel`
  
  * Install the following necessary packages for management Server
    * `sudo yum install cmake3 libevent-devel openssl-devel json-c-devel python-devel`
  
## Building the code
  ### Clone CORTX-POSIX repo
 
 * Accessing the code
  
  (For phase 1) The latest code which is getting evolved and contributed is on the Github server.
    CORTX-POSIX Contributors will be referencing, cloning and committing their code to/from this [Github](https://github.com/Seagate/cortx-posix).

  Following steps will make your access to server hassle free.
  
  1. From here on all the steps needs to be followed as the root user.
       * Set the root user password using `sudo passwd` and enter the required password.
       * Type `su -` and enter the root password to switch to the root user mode.
  2. Create SSH Public Key
       * [SSH generation](https://git-scm.com/book/en/v2/Git-on-the-Server-Generating-Your-SSH-Public-Key) will make your key generation super easy. Follow the instructions throughly.
  3. Add New SSH Public Key on [Github](https://github.com/settings/keys) and [Enable SSO](https://docs.github.com/en/github/authenticating-to-github/authorizing-an-ssh-key-for-use-with-saml-single-sign-on).
    
  * Clone repository
  
  1. `$ git clone git@github.com:Seagate/cortx-posix.git -b main`
    Note:If username prompted than enter github username and for password copy from [PAT](https://github.com/settings/tokens)
    or generate a new one using [Generate PAT](https://github.com/settings/tokens) and enable SSO ( It has been assumed that `git` is preinstalled ).
    Recommended git version is 2.x.x . Check your git version using `$ git --version` command.)
  2. `$ cd cortx-posix`	
    
  ### Build CORTX-FS
  * Build and install libevhtp
    * `./scripts/build-libevhtp.sh`
    * Install the libevhtp rpms from the  ~/rpmbuild/RPMS/x86_64/ directory
      * `cd ~/rpmbuild/RPMS/x86_64/`
      * `sudo yum install libevhtp-1.2.18-2.el7.x86_64.rpm libevhtp-devel-1.2.18-2.el7.x86_64.rpm`
  * Download sources for CORTXFS components
    * `./scripts/build.sh bootstrap`
  * Initialize the build folders
    * `./scripts/build.sh config`
  * Build binaries from the sources
    * `./scripts/build.sh make -j`
  * Generate RPMs
    * `./scripts/build.sh rpm-gen`
  
  ### Build NFS Ganesha
  
   To build NFS Ganesha refer to [build-nfs-ganesha](https://github.com/Seagate/cortx-posix/blob/dev/doc/CortxPosixNFSGanesha.md#build-nfs-ganesha)
    
## Installing the rpms/code
  
  ### Install CORTX-FS rpms
  * `sudo ./scripts/build.sh rpm-install`
  
  ### Install NFS Ganesha rpms
  To install NFS Ganesha rpms refer to [install-nfs-ganesha-rpms](https://github.com/Seagate/cortx-posix/blob/dev/doc/CortxPosixNFSGanesha.md#install-nfs-ganesha-rpms)
  
## Uninstalling the rpms/code
  
  ### Uninstall CORTX-FS rpms
  * remove cortx-fs rpms
    * `sudo ./scripts/build.sh rpm-uninstall`
  
  ### Uninstall NFS Ganesha rpms

  To uninstall NFS Ganesha rpms refer to [uninstall-nfs-ganesha-rpms](https://github.com/Seagate/cortx-posix/blob/dev/doc/CortxPosixNFSGanesha.md#uninstall-nfs-ganesha-rpms)
  
## Reinstalling the rpms/code
  
  ### Reinstall CORTX-FS rpms
  * reinstall cortx-fs rpms
    * `sudo ./scripts/build.sh reinstall`

## Running the tests
Refer to [CortxPosixTests](https://github.com/Seagate/cortx-posix/blob/dev/doc/CortxPosixTests.md)

## TroubleShooting
Refer to [CortxPosixTroubleShoot](https://github.com/Seagate/cortx-posix/blob/dev/doc/CortxPosixTroubleShoot.md)

## You're all set & You're awesome

In case of any queries, feel free to write to our [SUPPORT](SUPPORT.md).

Let's start without a delay to contribute to Seagate's open source initiative and join this movement with us, keeping a common goal of making data storage better, more efficient and more accessible.

Seagate welcomes You! :relaxed: