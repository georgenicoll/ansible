Kernel Builds
=============

This directory contains playbooks to configure and build a kernel for the rPIs locally
(to include btf debug info and additionally build the vxlan module) and then apply the new kernel 
and modules to the rPIs.

The process for doing this is as follows:

1. Ensure the debian sources will be available on the *local* machine.
Instructions for doing this are contained in the comments at the top of
`kernel.01.build-kernel-locally-with-updated-config.yaml`.

1. Run `ansible-playbook kernel.01.build-kernel-locally-with-updated-config.yaml`.
The become password is for the *local* machine:
    
    this will copy the source across and configured it ready to build.

1. Run the commands listed in the `instructions` task (see ansible output
from the previous step for the directory in which to run these):

    ```
    export ARCH=arm64
    export $(dpkg-architecture -aarm64)
    export CROSS_COMPILE=aarch64-linux-gnu-
    fakeroot debian/rules clean
    debian/rules build
    fakeroot debian/rules binary
    ```
2. Run `ansible-playbook kernel.02.copy-kernel-to-pis.yaml`.
The become password is for the *local* machine:

    This will copy the debian packages to the rPIs.

1. Run `ansible-playbook kernel.03.apply-kernel.yaml` to install the new kernel and modules to the rPIs and reboot. 
The become password is for the *remote* machines.

NOTE: The process could be streamlined a bit but I haven't got around to doing it:
- only build modules actually required for my rPIs
