# DKMS fix for ALFA AWUS036AXML

This is but a temporary fix for the ALFA AWUS036AXML wifi adapter on
Ubuntu 22.04 kernel 6.5.0 that makes the `btusb` module ignore the
MediaTek chip used in the device. The device is not advertised to
support bluetooth, so this change is no limitation for the device.

However, it may be that other devices use the same usb identifier. The
bluetooth functionality of these devices will become unusable as long
the dkms fix is applied.

Unfixed, the `btusb` module will try to probe the device, fail, and
repeatedly reset it. This prevents the chip driver from probing and
using the device as a wifi adapter.

Note: I did not test the dkms signing because my machine does not use
UEFI, so I don't know if this works. That is also why I get the message:

```text
[  106.046878] btusb: loading out-of-tree module taints kernel.
[  106.046888] btusb: module verification failed: signature and/or required key missing - tainting kernel
```

## Installation

Note: This is only intended for Ubuntu 22.04 using HWE kernel 6.5.0!

```bash
git clone https://github.com/gschwaer/alfa-awus036axml-dkms alfa-awus036axml-1.0
sudo su
cp -r ./alfa-awus036axml-1.0 /usr/src/
dkms install alfa-awus036axml/1.0
```

This should output something along the lines of

```text
Creating symlink /var/lib/dkms/alfa-awus036axml/1.0/source -> /usr/src/alfa-awus036axml-1.0

Kernel preparation unnecessary for this kernel. Skipping...

Building module:
cleaning build area...
make -j8 KERNELRELEASE=6.5.0-28-generic -C /lib/modules/6.5.0-28-generic/build M=/var/lib/dkms/alfa-awus036axml/1.0/build...
Signing module:
 - /var/lib/dkms/alfa-awus036axml/1.0/6.5.0-28-generic/x86_64/module/btusb.ko
Secure Boot not enabled on this system.
cleaning build area...

btusb.ko:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/6.5.0-28-generic/updates/dkms/

depmod...
```

If successful, reboot.

## Uninstallation

```bash
sudo su
dkms uninstall alfa-awus036axml/1.0
# cleanup
dkms remove alfa-awus036axml/1.0 --all
rm -r /usr/src/alfa-awus036axml-1.0
rm -r ./alfa-awus036axml-1.0
reboot
```

## License

I only copied files from the Linux kernel, so all files are licensed as
they are in the kernel. I copied the kernel licensing documents to make
this clear.
