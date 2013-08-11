Title: VMware Tools on Ubuntu
Tags: Vmware, Vmware Tools, Ubuntu
Author: Marcelo Moreira
Summary: Installing VMware Tools on Ubuntu

VMware provide packages for Ubuntu on packages.vmware.com. The install procedure is documented in [VMware Tools Installation Guide: Operating System Specific Packages](http://www.vmware.com/pdf/osp_install_guide.pdf).

To add the repository and install the tools do:

    apt-add-repository 'deb http://packages.vmware.com/tools/esx/4.1latest/ubuntu lucid main restricted'
    wget http://packages.vmware.com/tools/VMWARE-PACKAGING-GPG-KEY.pub -q -O- | apt-key add -

    # The above links to the ESX "4.1latest" builds of VMware-tools; however,
    # these packages should be compatible with all VMware servers, not just ESX 4.1)

    apt-get update
    apt-get install vmware-open-vm-tools-kmod-source
    module-assistant prepare
    module-assistant build vmware-open-vm-tools-kmod-source
    module-assistant install vmware-open-vm-tools-kmod

    # EITHER: install tools for headless system
    apt-get install vmware-open-vm-tools-nox
    # OR: install for Xorg system
    apt-get install vmware-open-vm-tools
