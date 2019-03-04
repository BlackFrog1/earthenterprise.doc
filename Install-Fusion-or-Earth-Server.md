## **Supported Operating Systems**
The installer has been successfully tested on Ubuntu 14.04, Ubuntu 16.04, Red Hat Enterprise Linux 6, Red Hat Enterprise Linux 7, CentOS 6, and CentOS 7.

## Enable Additional Repositories for CentOS & RHEL

For CentOS and RHEL, Specific Yum repositories need to be enabled for different distributions to
install the required development tools. Note: this section is also contained in the Build instructions.

### CentOS 7

```bash
sudo yum install epel-release
```

### RHEL 7

```bash
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

### CentOS 6

```bash
sudo wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
sudo yum install -y epel-release
```

### RHEL 6

__NOTE:__ The EPEL URL below assumes that your RHEL 6 installation has
the latest updates.

```bash
# For RHEL 6 Workstation:
sudo subscription-manager repos --enable=rhel-x86_64-workstation-dts-2

# For RHEL 6 Server:
sudo subscription-manager repos --enable=rhel-server-dts2-6-rpms

# For all RHEL 6 Editions:
sudo subscription-manager repos --enable=rhel-6-server-optional-rpms
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
```

### **Building Open GEE**
Note: if you have pre-built RPMs, then you can skip this section.

To install Fusion or Earth Server, you must prepare the install package from a successful build of the source (which contains Fusion and Earth Server source code) and Third Party packages.  For more information on building Fusion, please review the following link:

[Building Earth Enterprise Fusion and Server](Build-Instructions)

### **Uninstalling Previous Versions**
If you have a previous version of GEE installed, we recommend that you uninstall the previous version before installing Open GEE.

## **Building the Install Package**
You will need to build the install package.  The install package is built using scons.  Please note that the default temporary staging area for the install package is /tmp/fusion_os_install.  This can be changed by specifying a different **installdir** parameter. Please note that all steps must use the same directory location.

In the commands below, you may need to change "release=1" to "optimize=1", depending on which type you used when building the source code.  You should use the same flag for both the build and install commands.

1. (Optional) Prepare the tutorial files
    ```
    cd earthenterprise/earth_enterprise/tutorial
    wget http://data.opengee.org/FusionTutorial-Full.tar.gz
    tar -xvzf FusionTutorial-Full.tar.gz -C FusionTutorial
    ```

1. (Mandatory) Create the install package
    ```
    cd earthenterprise/earth_enterprise
    scons -j8 release=1 stage_install
    ```

At this point, you have fully built the install package.  The Fusion and Earth Server installers use this package to install their respective components.

## **Installing Fusion**
To install fusion run the following command:

    cd earth_enterprise/src/installer
    sudo ./install_fusion.sh

The installer can use the default install directory of /tmp/fusion_os_install.  If you placed the install package in a different location, you can pass that location as a parameter to the installer.

Run the following to get an explanation of all available customizations:

`sudo ./install_fusion.sh -h`

**Note** The installer adds the `/opt/google/bin` to your path by adding a file to /etc/profile.d. However, these files are only read when you first log in, so you need to log out and back in or reboot before this will occur. Until then, you can temporarily add the bin directory to your path by executing the following command in each bash shell you wish to run Fusion from:

    export PATH=$PATH:/opt/google/bin

## **Installing Earth Server**
To install GEE server run the following command:

    cd earth_enterprise/src/installer
    sudo ./install_server.sh

The installer can use the default install directory of /tmp/fusion_os_install.  If you placed the install package in a different location, you can pass that location as a parameter to the installer.

Run the following to get an explanation of all available customizations:

`sudo ./install_server.sh -h`

### Install Run-time Requirements

Install the Python `PIL.Image` module. On Ubuntu you can install the `python-pil` package, on Red Hat platforms—`python-imaging`.

## **Installing Tutorial Data**
To install Tutorial Data after installing Fusion, run the following command:

    sudo /opt/google/share/tutorials/fusion/download_tutorial.sh

## **Uninstalling GEE fusion and server**
Run uninstall scripts from src/installer
    
    sudo earth_enterprise/src/installer/uninstall_fusion.sh
    sudo earth_enterprise/src/installer/uninstall_server.sh