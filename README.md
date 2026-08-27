# TWRP-PORT-
I have ported custom twrp recovery for itel a22 pro snapdragon 210 android 8.1


# Steps
1.Unlock the Bootloader
2.adb reboot fastboot
3.fastboot flash recovery port_itela22twrp_bykaarthikrishaanth.img
4.fastboot reboots

# Custom TWRP Recovery for itel A22 Pro

A custom **TWRP recovery port for the itel A22 Pro**, created using a donor TWRP recovery and the stock recovery from the device.

This project was mainly created to learn and experiment with **Android recovery porting**, kernel images, ramdisks, device trees, and TWRP.

## How I Ported TWRP

I used **Android Image Kitchen (AIK)** to unpack, modify, and repack the recovery images.

### 1. Unpack the images

First, I unpacked both the **stock recovery** from the itel A22 Pro and the **donor TWRP recovery** using Android Image Kitchen.

I kept both images in separate folders so that the stock files could be compared with and transferred to the donor recovery where needed.

### 2. Swap the kernel

The donor TWRP's kernel was replaced with the kernel from the stock recovery.

Specifically, I replaced:

```text
split_img/recovery.img-kernel
```

(or `zImage`, depending on the recovery image format)

with the corresponding kernel binary from the stock recovery.

This helps TWRP use the kernel that was built for the itel A22 Pro.

### 3. Transfer the Qualcomm DTB and cmdline

From the stock recovery's `split_img` directory, I copied the device-specific files:

```text
recovery.img-dtb
recovery.img-cmdline
```

into the corresponding ported recovery structure.

The **DTB (Device Tree Blob)** contains hardware information needed by the kernel, while the **cmdline** contains kernel boot parameters.

### 4. Update the partition table

I then checked and modified the TWRP filesystem configuration so that it matched the actual partitions on the itel A22 Pro.

Depending on the TWRP version, this can be found in:

```text
ramdisk/etc/recovery.fstab
```

or:

```text
ramdisk/etc/twrp.fstab
```

The block device paths were changed to match the partition layout of the stock recovery/device.

### 5. Update device properties

I also modified the relevant device properties inside the ramdisk.

For example:

```text
ro.board.platform=msm8909
```

I updated the device-related properties to match the itel A22 Pro and made sure the required recovery properties were configured correctly, including:

```text
ro.secure=0
```

These changes help TWRP identify and work with the target device correctly.

### 6. Repack the recovery

After making the required changes, I repacked the recovery using Android Image Kitchen's `repackimg` script.

This generated the new recovery image:

```text
image-new.img
```

### 7. Testing

Before permanently flashing the recovery, the image can be tested using fastboot:

```bash
fastboot boot image-new.img
```

This is useful for testing because it allows the recovery to be booted temporarily without immediately overwriting the device's recovery partition.

## Tools Used

* **Android Image Kitchen (AIK)**
* **TWRP**
* **Fastboot**
* Stock `recovery.img` from itel A22 Pro

## Device Information

| Property       | Value                       |
| -------------- | --------------------------- |
| Device         | itel A22 Pro                |
| Platform       | Qualcomm MSM8909            |
| Recovery       | Custom TWRP                 |
| Porting Method | Donor TWRP + Stock Recovery |
| Main Tool      | Android Image Kitchen       |

## Disclaimer

This is a personal/custom recovery port and is **not an official TWRP build**.

Flashing a recovery image incorrectly can result in a bootloop or, in the worst case, a bricked device. Always keep a backup of your original stock recovery and verify that the image is intended for the correct device before flashing.

Use it at your own risk.
