# Building Earth Enterprise (Fusion, Server) on Ubuntu 14.04 LTS and RHEL 7

## Common steps

Run these commands on either Ubuntu or RHEL/CentOS

1. Install git according to the instructions for your Linux OS below
2. Install git-lfs according to the instructions specified at https://git-lfs.github.com
3. Clone the gee-os repository with git
    1. Method 1: Clone and download LFS files in one step (may be slower and more error prone)
    >```git clone git@github.com:<username>/earthenterprise.git```
    1. Method 2: Clone and download LFS files in two steps
    >```GIT_LFS_SKIP_SMUDGE=1 git clone git@github.com:<username>/earthenterprise.git```
    
    >```cd earthenterprise```
 
    >```git lfs pull```
4. In the build instructions below, the scons commands for building GEE/Fusion have the following options:
    * `internal=1` - Build using non-optimized code, best for development and debugging
    * `optimize=1` - Build using optimized code, but with some debugging information
    * `release=1` - Build a release using optimized code and no debugging information
    * `-j#` - Specifies the number of simultaneous build jobs to use. Replace # with an integer. It should roughly match the number of processing cores available
    * `--debug=stacktrace` - If there is an error within a scons script, this will give you more detailed information on how to debug it
    * `--config=force` - If you accidentally delete the .sconf_temp directory or make some changes to your system build libraries, use this to force the configuration to run again, otherwise the scons build may complain about missing libraries
5. Build using the steps bellow for your target platform
6. Run unit tests
    > `cd NATIVE-OPT-x86_64/bin/tests`

    >`./RunAllTests.pl`

    Or run an individual test

## Steps for Building on Ubuntu 14.04.5 LTS:

1. Install git:
    > `sudo apt-get install git` (installs version 1.9.1)

    To get the most recent version of git, do the following (version 1.9.1 works for purposes of interacting with the repository, but if you want to download the latest version of git, the steps to do this have been outlined below):

    > `sudo -i`

    > `add-apt-repository ppa:git-core/ppa`

    > `apt-get update`

    > `apt-get install git`

    This will install version 2.11
    _Note: Be sure to exit "sudo" before proceeding_

2. Install the following packages (if they’re not installed already):
    > `sudo apt-get install gcc g++ scons automake autoconf libperl4-corelibs-perl libtool xorg-dev doxygen python-dev alien swig libgtest-dev libstdc++6 libxml2-dev gettext libxinerama-dev libxft-dev libxrandr-dev libxcursor-dev libgdbm-dev libc6 libc6-dev libmng-dev zlib1g-dev libcap-dev libpng12-0 libpng12-dev freeglut3-dev flex libx11-dev bison++ bisonc++ libjpeg-dev libjpeg8-dev python2.7 python2.7-dev libogdi3.2-dev libgif-dev libxerces-c-dev libgeos-dev libgeos++-dev libfreetype6 libfreetype6-dev python-imaging libproj-dev python-setuptools libgif-dev libxerces-c-dev libcap-dev libpq-dev openssl`

3. Build the third party library:
    For reference: GEEDIR=<new directory location>/googleclient/geo/earth_enterprise

    > `cd $GEEDIR/src`

    > `scons -j8 optimize=1 third_party`

4. Build Fusion/Earth Server from third_party/python/SConscript: remove -idirafter replacement for sandbox path
    > `scons -j8 optimize=1`

## Steps for building on RHEL 7

1. Install git

    Recommended: install the latest version of git:

    1. Enable the IUS repo [ [More Info] ](https://ius.io/GettingStarted/). 
    
        >```cd /tmp```

        >```wget https://rhel7.iuscommunity.org/ius-release.rpm```

        >```sudo yum install ius-release.rpm```
    1. Install git 2.x
        >```sudo yum install git2u-all```

    Or install the system default version of git (1.8):

    >```sudo yum install git```

1. Install EPEL repo
    >```sudo yum install epel-release```

    Note, if this command doesn't work, try the alternate methods listed [here](https://www.cyberciti.biz/faq/installing-rhel-epel-repo-on-centos-redhat-7-x/)
1. Enable optional repos
    >```sudo subscription-manager repos --enable=rhel-7-server-optional-rpms```

    >```sudo subscription-manager repos --enable=rhel-7-server-optional-source-rpms```
1. Install Development Tools
    >```sudo yum --setopt=group_package_types=mandatory,default,optional groupinstall "Development Tools"```
1. Install additional packages
    > `sudo yum install scons perl-Perl4-CoreLibs xorg-x11-server-devel python-devel perl-Alien-Packages gtest-devel openssl-devel libxml2-devel libXinerama-devel libXft-devel libXrandr-devel libXcursor-devel gdbm-devel libmng-devel libcap-devel libpng12-devel libXmu-devel freeglut-devel zlib-devel libX11-devel bison-devel openjpeg-devel openjpeg2-devel geos-devel proj-devel ogdi-devel giflib-devel xerces-c xerces-c-devel`
1. Build third-party libraries
    
    GEEDIR=<new directory location>/googleclient/geo/earth_enterprise
    >  `cd $GEEDIR/src`

    >  `scons -j8 optimize=1 third_party`
1. Build Fusion/Earth Server
    > `scons -j8 optimize=1`

## Build and deploy if you have access to the 5.1.3 installer
A build and deploy script is provided that can simplify the build and installation process if you have the GEE and Fusion 5.1.3 installers. In the future this script will be updated to work without the previous installers. 
1. Install GEE and Fusion 5.1.3
1. Clone repo as above
1. Build and deploy:
    > `cd $GEEDIR/src`

    > `./tmp/build_and_deploy_gee.sh --build`


# Building Portable Server on Linux and Windows
A Python script for building the Portable Server for Linux and Windows is being reviewed. Upon approval, and merging, Portable Server would be built as follows:

    earthenterprise/earth_enterprise/src/portableserver/build.py

If you run on Linux, the script will produce a compressed archive with a name that looks like `earthenterprise/earth_enterprise/src/portableserver/build/portableserver-linux-5.1.3-20170412.tar.gz`. The build date part of the file name will change depending on the day you build. On Windows the file name would look like `portableserver-linux-5.1.3-20170412.zip`.

To run Portable Server from one of these archive packages: 

    1. Extract all archive contents
    2. cd portableserver-linux-5.1.3-20170412/server/ #(substituting your extracted directory)
    3. python portable_server.py

You can edit `portableserver-linux-5.1.3-20170412/server/portable.cfg` and `portableserver-windows-5.1.3-20170412/server/remote.cfg` for your configuration needs before starting the server.

To clean build files, run with the `-c`, or `--clean` command-line parameter.