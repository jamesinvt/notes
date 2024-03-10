**Partition the Disk**:
* run sudo `fdisk -l` to get devices
	* looking for the microsoft efi partion `nvme0n1p1`
- Use `fdisk`, `gdisk`, or `parted` to partition your disk. For example, using `parted`:

```sh
sudo fdisk /dev/sdX
```