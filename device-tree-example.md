
# Linux Device Tree Driver

# 1. Introduction

A Device Tree (DT) is a data structure used by the Linux kernel to describe:

- Hardware components
- Memory addresses
- Interrupts
- GPIOs
- Clocks
- Buses
- Peripheral devices

without hardcoding platform-specific information into drivers.

Device Tree is heavily used in:

- ARM
- ARM64
- RISC-V
- Embedded Linux systems

---

# 2. What is Device Tree?

Device Tree is a hardware description mechanism consisting of:

| File | Purpose |
|---|---|
| .dts | Device Tree Source |
| .dtsi | Shared include file |
| .dtb | Compiled binary blob |

The bootloader loads the:

```text
DTB (Device Tree Blob)
````

into memory before the Linux kernel starts.

The kernel then parses the DTB and initializes hardware.

---

# 3. Why Do We Use Device Tree?

Before Device Tree, drivers contained:

* Hardcoded addresses
* IRQ numbers
* GPIO numbers
* Board-specific logic

This caused:

* Poor portability
* Difficult maintenance
* Multiple kernel builds

Device Tree solves this problem.

---

# 4. Real-Time Examples

| Hardware        | Device Tree Usage         |
| --------------- | ------------------------- |
| GPIO Controller | Pin mapping               |
| I2C Devices     | Bus registration          |
| SPI Devices     | Chip-select configuration |
| UART            | Baud and interrupts       |
| LCD Panels      | Display timing            |
| Sensors         | Address and IRQ setup     |

---

# 5. Device Tree Architecture

```text id="t7m2vx"
+--------------------------------+
| Bootloader                     |
|--------------------------------|
| Loads DTB into RAM             |
+---------------+----------------+
                |
                v
+--------------------------------+
| Linux Kernel                   |
|--------------------------------|
| Parses Device Tree             |
+---------------+----------------+
                |
                v
+--------------------------------+
| Driver Matching                |
|--------------------------------|
| compatible = "vendor,device"   |
+---------------+----------------+
                |
                v
+--------------------------------+
| Driver probe()                 |
+--------------------------------+
```

---

# 6. Device Tree Flow

```text id="q5m8wy"
Bootloader Loads DTB
         ↓
Kernel Parses DTB
         ↓
Platform Device Created
         ↓
Driver Match Occurs
         ↓
probe() Called
```

---

# 7. Device Tree Syntax Basics

Example:

```dts id="x3m7vq"
/ {
    model = "My Board";

    my_device {
        compatible = "myvendor,mydevice";
        reg = <0x10000000 0x1000>;
        interrupt-parent = <&gpio1>;
        interrupts = <5 0>;
    };
};
```

---

# 8. Important Device Tree Concepts

## Node

Represents hardware device.

Example:

```dts id="f8m1wy"
uart0 {
};
```

---

## Property

Describes device attributes.

Example:

```dts id="p4m9qx"
compatible = "vendor,device";
```

---

## compatible

Used for driver matching.

---

## reg

Defines:

* Memory address
* Register size

---

## interrupts

IRQ configuration.

---

# 9. Important Header Files

```c id="v2m6wx"
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/platform_device.h>
#include <linux/of.h>
#include <linux/of_device.h>
#include <linux/of_gpio.h>
```

---

# 10. Device Tree Match Table

Drivers use:

```c id="r7m3qy"
of_match_table
```

for matching.

Example:

```c id="n1m8vx"
static const struct of_device_id my_driver_ids[] = {
    { .compatible = "myvendor,mydevice" },
    { }
};
MODULE_DEVICE_TABLE(of, my_driver_ids);
```

---

# 11. Full Device Tree Driver Example

## device_tree_driver.c

```c id="y4m7wx"
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/platform_device.h>
#include <linux/of.h>

static int my_probe(struct platform_device *pdev)
{
    printk(KERN_INFO "Device Tree Driver Probed\n");

    return 0;
}

static int my_remove(struct platform_device *pdev)
{
    printk(KERN_INFO "Device Tree Driver Removed\n");

    return 0;
}

static const struct of_device_id my_driver_ids[] = {
    { .compatible = "myvendor,mydevice" },
    { }
};

MODULE_DEVICE_TABLE(of, my_driver_ids);

static struct platform_driver my_platform_driver = {
    .probe  = my_probe,
    .remove = my_remove,
    .driver = {
        .name = "my_dt_driver",
        .of_match_table = my_driver_ids,
    },
};

module_platform_driver(my_platform_driver);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("Simple Device Tree Driver");
```

---

# 12. Device Tree Source Example

## my_device.dts

```dts id="k6m2vy"
/ {
    my_device {
        compatible = "myvendor,mydevice";

        reg = <0x10000000 0x1000>;

        status = "okay";
    };
};
```

---

# 13. Makefile

```Makefile id="u3m8qx"
obj-m += device_tree_driver.o

KDIR := /lib/modules/$(shell uname -r)/build
PWD  := $(shell pwd)

all:
	make -C $(KDIR) M=$(PWD) modules

clean:
	make -C $(KDIR) M=$(PWD) clean
```

---

# 14. Compile Driver

```bash id="x7m5wy"
make
```

Output:

```bash id="b9m1vx"
device_tree_driver.ko
```

---

# 15. Compile Device Tree

```bash id="h4m8qy"
dtc -I dts -O dtb -o my_device.dtb my_device.dts
```

---

# 16. Insert Driver

```bash id="r2m6wx"
sudo insmod device_tree_driver.ko
```

---

# 17. Check Kernel Logs

```bash id="m5m9qy"
dmesg | tail
```

Expected:

```text id="w8m3vx"
Device Tree Driver Probed
```

---

# 18. Important Device Tree APIs

## of_property_read_u32()

Reads integer property.

Example:

```c id="q1m7wy"
u32 value;

of_property_read_u32(node,
                     "clock-frequency",
                     &value);
```

---

## of_get_named_gpio()

Reads GPIO number.

Example:

```c id="z4m2qx"
gpio = of_get_named_gpio(node,
                         "reset-gpios",
                         0);
```

---

## of_find_node_by_name()

Finds DT node.

Example:

```c id="f7m5vx"
node = of_find_node_by_name(NULL,
                            "my_device");
```

---

# 19. GPIO Device Tree Example

```dts id="n8m1wy"
my_device {
    compatible = "myvendor,mydevice";

    reset-gpios = <&gpio1 5 GPIO_ACTIVE_HIGH>;
};
```

Driver:

```c id="y3m6qx"
gpio = of_get_named_gpio(node,
                         "reset-gpios",
                         0);
```

---

# 20. Interrupt Device Tree Example

```dts id="v5m8wx"
interrupt-parent = <&gpio1>;

interrupts = <5 IRQ_TYPE_EDGE_RISING>;
```

---

# 21. I2C Device Tree Example

```dts id="c2m7qy"
&i2c1 {

    sensor@68 {
        compatible = "vendor,sensor";
        reg = <0x68>;
    };
};
```

---

# 22. SPI Device Tree Example

```dts id="j9m4vx"
&spi0 {

    display@0 {
        compatible = "vendor,display";
        reg = <0>;
        spi-max-frequency = <1000000>;
    };
};
```

---

# 23. Advantages of Device Tree

| Advantage             | Description                   |
| --------------------- | ----------------------------- |
| Hardware Abstraction  | Drivers become generic        |
| Portability           | One kernel for many boards    |
| Easier Maintenance    | No hardcoded addresses        |
| Dynamic Configuration | Hardware described externally |
| Cleaner Drivers       | Less board-specific code      |

---

# 24. Disadvantages of Device Tree

| Disadvantage         | Description              |
| -------------------- | ------------------------ |
| Complex Syntax       | Learning curve           |
| Debugging Difficulty | DT parsing issues        |
| Runtime Errors       | Wrong DT causes failures |
| Platform Dependency  | Mainly ARM/RISC-V        |

---

# 25. Device Tree vs ACPI

| Feature              | Device Tree  | ACPI            |
| -------------------- | ------------ | --------------- |
| Common Platforms     | ARM/RISC-V   | x86             |
| Hardware Description | External DTB | Firmware tables |
| Human Readable       | Yes (.dts)   | No              |
| Embedded Usage       | Very common  | Rare            |

---

# 26. Common Interview Questions

## Q1. What is Device Tree?

A hardware description mechanism used by Linux kernel.

---

## Q2. Why Use Device Tree?

To avoid hardcoded hardware information inside drivers.

---

## Q3. What is compatible Property?

Used for matching drivers with devices.

---

## Q4. Difference Between DTS and DTB?

| File | Description          |
| ---- | -------------------- |
| DTS  | Source file          |
| DTB  | Compiled binary blob |

---

## Q5. What is probe() in DT Drivers?

Called when kernel matches DT node with driver.

---

# 27. Common Errors

## Error: probe() Never Called

Cause:

* compatible mismatch

Fix:

* Verify:

```dts id="x6m3wy"
compatible
```

string.

---

## Error: GPIO Not Found

Cause:

* Invalid GPIO property

Fix:

* Verify:

```dts id="u1m8qx"
reset-gpios
```

---

## Error: DTB Not Loaded

Cause:

* Bootloader issue

Fix:

* Verify boot arguments

---

# 28. Device Tree Debugging Techniques

## View Device Tree

```bash id="r4m9vx"
dtc -I fs /sys/firmware/devicetree/base
```

---

## Check Loaded Nodes

```bash id="g7m2wy"
ls /proc/device-tree/
```

---

## Kernel Logs

```bash id="m8m5qx"
dmesg | tail
```

---

# 29. Advanced Device Tree Topics

After learning basic DT drivers, move to:

* Device Tree overlays
* Pin control subsystem
* Clock framework
* Power domains
* Reserved memory
* DMA configuration

---

# 30. Device Tree Overlay Example

Used for runtime hardware changes.

Example:

```dts id="t3m7qy"
/plugin/;
```

Commonly used on:

* Raspberry Pi 5

---

# 31. Best Practices

## Keep Drivers Generic

Avoid board-specific logic.

---

## Use Descriptive compatible Strings

Example:

```dts id="w9m1vx"
vendor,device
```

---

## Validate DT Properties

Always check API return values.

---

## Document Device Tree Bindings

Use YAML bindings for upstream kernel.

---

# 32. Real Hardware Platforms

Device Tree is heavily used on:

* Raspberry Pi 5
* BeagleBone Black
* NVIDIA Jetson Nano
* STM32MP157
* ESP32-S3

---

# 33. Real Linux Device Tree Examples

| Device          | Usage                |
| --------------- | -------------------- |
| UART            | Serial ports         |
| I2C Devices     | Sensor configuration |
| SPI Flash       | Storage setup        |
| GPIO Controller | Pin configuration    |
| Ethernet MAC    | Network setup        |

---
