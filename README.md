# ICM-42688-P Breakout Board

## Overview

### What is the ICM-42688-P?
The **ICM-42688-P** is a high-performance **6-axis Inertial Measurement Unit (IMU)** that integrates a **3-axis gyroscope** and a **3-axis accelerometer** in a compact package. It offers **low noise, high stability, and precision motion tracking**, making it ideal for applications like **drones, robotics, and industrial motion sensing**.  

The sensor supports both **I2C and SPI** communication, features **programmable digital filters, FIFO buffering, and low power consumption**, and includes **two interrupt pins** for efficient data handling. Its **temperature stability and low drift** make it well-suited for demanding applications requiring **accurate inertial measurements**.

### About This Project
This project is a **custom breakout board** for the ICM-42688-P, designed using **KiCad 8.0**. It includes:
- **Hardware**: A compact PCB with **I2C & SPI support**, onboard **3.3V LDO**, and **interrupt pins**.
- **Firmware**: Example code using **PlatformIO (Arduino framework)** for easy testing.
- **Testing Guide**: Steps to set up and read sensor data over I2C & SPI.

The driver used for interfacing with the **ICM-42688-P** is an open-source implementation developed by [Inhwan Wee ](https://github.com/finani) and is hosted on [https://github.com/finani/ICM42688](https://github.com/finani/ICM42688). This driver provides a well-structured and efficient way to communicate with the sensor over I2C or SPI, enabling seamless integration with Arduino and PlatformIO environments.

## Hardware Design
### Features:
- **Power Supply**: 3.3V LDO regulator onboard
- **Communication**: Supports both **I2C (SDA/SCL)** and **SPI (SCK, SDI, SDO, CS)**
- **Interrupts**: `INT1` and `INT2` available
- **Jumpers**: Select between I2C and SPI modes
- **Designed in**: KiCad 8.0
- **Tested on**: PlatformIO with Arduino framework

### Schematic
![ICM-42688-P Schematic](/Hardware/ICM-42688-P-Schematic.PNG)

### PCB Layout
![ICM-42688-P PCB](/Hardware/ICM-42688-P-3d-Pic.PNG)

## Software

### Fixing `<cstdint>` Issues with AVR-GCC  

When compiling for AVR-based boards (e.g., **Arduino Uno, Mega, etc.**) using **AVR-GCC**, you may encounter the following error:  

```sh
fatal error: cstdint: No such file or directory
```

This happens because **AVR-GCC does not fully support C++ standard headers like `<cstdint>`**. This issue is common when using **PlatformIO** or **VS Code with IntelliSense**.  


### Solution: Use `<stdint.h>` Instead  
The best solution is to replace `<cstdint>` with `<stdint.h>` in the `registers.h` file of the driver src code.  

❌ **Incorrect:**  
```cpp
#include <cstdint>  // This may cause issues with AVR-GCC
```
✅ **Correct:**  
```cpp
#ifdef AVR  // If compiling for AVR, define NO_CSTDINT to prevent including <cstdint>
# define NO_CSTDINT 1
#endif

// If NO_CSTDINT is NOT defined, include the standard C++ <cstdint>
#ifndef NO_CSTDINT
# include <cstdint>
#else
# include <stdint.h>

// If <cstdint> is unavailable, manually provide the needed types.
namespace std {
  using ::int8_t;           
  using ::uint8_t;         
                     
  using ::int16_t;         
  using ::uint16_t;       
                     
  using ::int32_t;         
  using ::uint32_t;       
}

#endif
```
✅ `<stdint.h>` is the **C version of `<cstdint>`** and is fully compatible with AVR-GCC.
