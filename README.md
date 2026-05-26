# Linux Device Driver Q&A

![Linux](https://img.shields.io/badge/Linux-Kernel-blue)
![Language](https://img.shields.io/badge/Language-C-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

A comprehensive collection of Linux Device Driver examples, Linux kernel subsystem tutorials, debugging techniques, Device Tree examples, and interview preparation material for Embedded Linux developers.

---

#  Table of Contents

- [Project Structure](#-project-structure)
- [Linux Driver Architecture](#-linux-driver-architecture)
- [Core Driver Concepts](#-core-driver-concepts)
- [Build and Compile](#-build-and-compile)
- [Driver Examples](#-driver-examples)
- [GPIO Example](#-gpio-example)
- [I2C Example](#-i2c-example)
- [Device Tree Example](#-device-tree-example)
- [Debugging Techniques](#-debugging-techniques)
- [Interview Questions](#-interview-questions)
- [Supported Platforms](#-supported-platforms)
- [References](#-references)

---

# Project Structure

| Driver | Description |
|--------|-------------|
| [Character Driver](https://github.com/jsramesh1990/Character-Device-Driver/blob/main/char-driver.md) | Character device driver examples |
| [GPIO Driver](https://github.com/jsramesh1990/virtual_sysfs_GPIO/blob/main/gpio-driver.md) | GPIO subsystem examples |
| [I2C Driver](https://github.com/jsramesh1990/I2C-Sensor-Data-Acquisition-and-Visualization-using-C-and-Python/blob/main/i2c-driver.md) | I2C communication examples |
| [SPI Driver](https://github.com/jsramesh1990/spi-communication-project/blob/main/spi-driver.md) | SPI subsystem examples |
| [Platform Driver](https://github.com/jsramesh1990/Device-Driver-samples/blob/main/platform-driver.md) | Platform driver implementation |
| [Interrupt Driver](https://github.com/jsramesh1990/Device-Driver-samples/blob/main/interrupt-driver.md) | Interrupt handling examples |
| [Procfs Example](https://github.com/jsramesh1990/Device-Driver-samples/blob/main/Procfs.md) | procfs interface examples |
| [Sysfs Example](https://github.com/jsramesh1990/virtual_sysfs_GPIO/blob/main/sysfs-example.md) | sysfs interface examples |
| [IOCTL Example](https://github.com/jsramesh1990/Device-Driver-samples/blob/main/ioctl-example.md) | ioctl communication examples |
| [Workqueue Example](https://github.com/jsramesh1990/Device-Driver-samples/blob/main/workqueue-example.md) | Deferred work examples |
| [Kernel Thread Example](https://github.com/jsramesh1990/linux-kernel-debugging-guide/blob/main/kernel-thread-example.md) | Kernel thread examples |
| [Device Tree Example](https://github.com/jsramesh1990/linux-device-driver-Q-A/blob/main/device-tree-example.md) | Device Tree examples |

---

#  Linux Driver Architecture

```text
User Space Application
        |
open/read/write/ioctl
        |
        v
Device File (/dev/xxx)
        |
        v
+---------------------------+
| Linux Kernel / VFS        |
+---------------------------+
        |
        v
+---------------------------+
| Device Driver             |
| - probe()                 |
| - open()                  |
| - read()                  |
| - write()                 |
+---------------------------+
        |
        v
Hardware Device
```

---

#  Core Driver Concepts

| Kernel Space | User Space |
|--------------|------------|
| Full hardware access | Restricted access |
| Drivers run here | Applications run here |
| Uses printk() | Uses printf() |

---


#  Driver Examples

| Driver Type | Usage |
|-------------|-------|
| Character Driver | Device file operations |
| GPIO Driver | GPIO subsystem |
| I2C Driver | I2C communication |
| SPI Driver | SPI subsystem |
| Platform Driver | Device matching |
| Interrupt Driver | IRQ handling |
| IOCTL | User-kernel communication |
| Workqueue | Deferred execution |
| Kernel Thread | Background tasks |
| Device Tree | Hardware description |

---

#  GPIO Example

```c
reset_gpio = devm_gpiod_get(dev, "reset", GPIOD_OUT_LOW);

gpiod_set_value(reset_gpio, 1);
msleep(20);
gpiod_set_value(reset_gpio, 0);
```

---

#  I2C Example

```c
ret = i2c_master_send(client, &reg, 1);

if (ret < 0)
    dev_err(&client->dev, "I2C error\n");
```

---

# 🌲 Device Tree Example

```dts
mydevice@20 {
    compatible = "myvendor,mydevice";
    reset-gpios = <&gpio 20 0>;
};
```

---

#  Debugging Techniques

## printk()

```c
printk(KERN_INFO "Driver Loaded\n");
```

## View Kernel Logs

```bash
dmesg | tail
```

## ftrace

```bash
echo function > /sys/kernel/debug/tracing/current_tracer
```

---

#  Interview Questions

## Difference between insmod and modprobe?

- `insmod` loads a single module
- `modprobe` loads dependencies automatically

---

## Why use copy_to_user()?

Used to safely transfer data from kernel space to user space.

---

## Why can't interrupts sleep?

Interrupt handlers run in atomic context and cannot sleep.

---

#  Supported Platforms

| Platform | Hardware Description |
|----------|----------------------|
| x86/x86_64 | ACPI / PCI |
| ARM/ARM64 | Device Tree |
| RISC-V | Device Tree |

---

