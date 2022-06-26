+++
title = "Operating System"
date = "2017-12-31"
categories = [
    "OS",
]

+++
## Lost grub bootloader

Some reasons lead to lost grub:

* Upgrade BIOS
* Reinstall windows while using dual OS
* Grub not installed in Linux installation progress

### Re-install grub bootloader

Use [boot-repair-disk](https://sourceforge.net/projects/boot-repair-cd/) as a rescue tool if can't boot into linux os, setup into the USB and boot it up

```bash
grub-install /dev/sda
update-grub
```

Check the disk with `fdisk -l` or `blkid`

### Using BOOTICE to modify

UEFI uses `efi` files in EFI partition to boot the OS, as Windows is `bootmgfw.efi`, Kali Linux is `grubx64.efi`,... Now setup a boot entry on the top of order. Go to `UEFI` tab >  `Edit boot entries`

Add a new one with:

* Menu title: `any`
* Boot disk: `primary disk`
* Boot part: `EFI SYSTEM partition`
* Media file: `EFI file (default of Kali is EFI\kali\grubx64.efi)`

![2d](../../sec.assets/note/bootice.png)

Check the EFI partition: use `Disk Management` or open `cmd` and:

````powershell
Diskpart
list disk
select disk 0
list partition/list volume
````

### Error unknown file system. grub rescue

This error can happen when using dual os and install some BIOS update.

```bash
ls
ls (hd0,msdos3) #to check the type of partitions
set boot=(hd0,msdos3)
set prefix=(hd0,msdos3)/boot/grub/
insmod normal
normal
```

Then re-install grub.

### Conflict partitions lead to slow booting or fail: swap, efi, ext

Run `blkid` to show list of partitions, then check if `/etc/fstab` information is correct. Use [boot-repair-disk](https://sourceforge.net/projects/boot-repair-cd/) in case the OS can't boot up

Example of fstab:

```bash
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/nvme0n1p3 during installation
UUID=689236a8-81bc-4eae-8888-12287e0a98ae /               ext4    errors=remount-ro 0       1
# /boot/efi was on /dev/nvme0n1p1 during installation
UUID=F662-2252  /boot/efi       vfat    umask=0077      0       1
# swap was on /dev/nvme0n1p2 during installation
UUID=934586aa-a8cc-484b-bbe2-6c5b09898b56 none            swap    sw              0       0
```



## Access EFI partition or system partition

On windows:

```powershell
Diskpart
list disk
select disk 0
select partition 1/select volume 1
assign letter=K
```

Access as Administrator: use **explorer++**

To unmount: `remove letter=K`

