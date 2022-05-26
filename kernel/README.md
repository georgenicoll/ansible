Kernel Builds
=============

This directory contains play books to configure and build a kernel for the rPIs locally
(to include btf debug info and additionally build the vxlan module) and then apply the new kernel 
and modules to the rPIs.

The process for doing this is as follows:

1. Run `ansible-playbook kernel.01.build-kernel-locally-with-updated-config.yml` using become password for the *local* machine:
    
    the first run will fail but will have copied the source across and configured it ready to build.

1. Run the commands listed in the `run build commands` task (see ansible output
from the previous step for the directory in which to run these):

    ```
    export ARCH=arm64
    export $(dpkg-architecture -aarm64)
    export CROSS_COMPILE=aarch64-linux-gnu-
    fakeroot debian/rules clean
    debian/rules build
    fakeroot debian/rules binary
    ```
1. Re-run `ansible-playbook kernel.01.build-kernel-locally-with-updated-config.yml` using become password for the *local* machine:

    This will copy the debian packages to the rPIs.

1. Run `kernel.02.apply-kernel.yml` to install the new kernel and modules to the rPIs and reboot using become password for the *remote* machines.

NOTE: The process could be streamlined a bit but I haven't got around to doing it:
- only build modules actually required for my rPIs
- separate out the build and copy from local to rPIs steps into separate playbooks (to prevent re-unpacking the source)