# ovmf-setting
Trying to make my PC a linux based envrionment with bunch of VMs for gaming, working etc.

Noting for the steps and researches done for fighting with PCI passthrough via OVMF...

## 20230507
Gave up Signle-GPU-Passthrough, using DisplayPort@Intel Graphic and HDMI@1080-ti

- [archlinux install steps](https://github.com/sjulianryo/ovmf-setting/blob/main/20230507/archlinux-install.md)
    - [Setting files](https://github.com/sjulianryo/ovmf-setting/tree/main/20230507)

References:

- https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF
- https://github.com/QaidVoid/Complete-Single-GPU-Passthrough
- https://github.com/ledisthebest/LEDs-single-gpu-passthrough/blob/main/README-cn.md
- https://blandmanstudios.medium.com/tutorial-the-ultimate-linux-laptop-for-pc-gamers-feat-kvm-and-vfio-dee521850385
- https://hardcoded.info/post/2023/03/04/pci-passthrough-using-vfio-pci-for-linux-kernel-version-6-solution/

Tried all ways to bind pci ids to vfio and make the GPU using vfio-pci driver though cannot boot up at all

Is it impossible to do that in only one GPU with host using the same GPU at the same time?

How did they do that in a laptop...? Because it switched automatically?

Okay, finally found the answers here

https://wiki.archlinux.org/title/QEMU/Guest_graphics_acceleration

### Todo
- [ ] Linux virtual machine setup
- [ ] Hackintosh
- [ ] GPU virtualization
