# Embedded Systems Interface Classes

![C++](https://img.shields.io/badge/C%2B%2B-11+-blue.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

A comprehensive C++11 library providing abstract base classes for embedded systems peripherals. This library enables hardware-agnostic firmware development by defining clean interfaces for communication protocols and device control.

## Features

✅ **Hardware-Agnostic Design** - Abstract interfaces allow easy porting between platforms  
✅ **Multiple Protocol Support** - I2C, SPI, UART, One-Wire, and timer interfaces  
✅ **Device-Level Abstractions** - GPIO, timers, and I2C/SPI device wrappers  
✅ **Method Chaining** - Fluent API design for cleaner code  
✅ **C++11 Compatible** - Works with C++11 and later standards  
✅ **MIT Licensed** - Free for commercial and personal use  

## Quick Start

### 1. Include the Headers

```cpp
#include "interfaces.hpp"  // Communication interfaces
#include "devices.hpp"     // Device classes
```

### 2. Implement an Interface

```cpp
class MyI2C : public I2C_InterfaceBase {
public:
    int Write(uint8_t device_addr, const uint8_t *reg_addr,
              size_t reg_addr_length, const uint8_t *data,
              size_t data_length) override {
        // Hardware-specific implementation
        return 0;
    }
    
    int Read(uint8_t device_addr, const uint8_t *reg_addr,
             size_t reg_addr_length, uint8_t *data,
             size_t data_length) override {
        // Hardware-specific implementation
        return 0;
    }
};
```

### 3. Use Device Abstractions

```cpp
MyI2C i2c_interface;

class BME280_Sensor : public I2C_DeviceBase {
public:
    BME280_Sensor(I2C_InterfaceBase &ifs) 
        : I2C_DeviceBase(ifs, 0x77) {}
    
    void ReadTemperature(uint8_t &temp) {
        Read(0xFA, temp);  // Method chaining supported
    }
};

BME280_Sensor sensor(i2c_interface);
uint8_t temperature;
sensor.ReadTemperature(temperature);
```

## Architecture

### Communication Interfaces

Low-level protocol abstractions for hardware communication:

- **`I2C_InterfaceBase`** - I²C protocol (read/write to device registers)
- **`SPI_InterfaceBase`** - SPI protocol (full-duplex data transfer)
- **`UART_InterfaceBase`** - UART protocol (serial communication)
- **`OneWire_InterfaceBase`** - One-Wire protocol (single wire communication)
- **`Time_InterfaceBase`** - Time measurement (microsecond precision)

### Device Classes

High-level device abstractions built on communication interfaces:

- **`GPIO_PinBase`** - Single GPIO pin control (input/output with pull-up/down)
- **`GPIO_PortBase<T>`** - GPIO port access via bitmasks (templated for port sizes)
- **`I2C_DeviceBase`** - Generic I2C device with register access
- **`SPI_DeviceBase`** - Generic SPI device communication
- **`Timer_DeviceBase`** - Timer/counter device control

## File Structure

```
├── interfaces.hpp      # Communication interface definitions
├── devices.hpp         # Device class definitions
├── README.md           # This file
└── LICENSE             # MIT License
```

## API Documentation

For detailed API documentation including method signatures, parameters, and usage examples, see [DOCUMENTATION.md](DOCUMENTATION.md).

### Quick API Reference

#### I2C Device Example
```cpp
I2C_DeviceBase device(i2c_interface, 0x50);
uint8_t buffer[16];

// Read 16 bytes from register 0x10
device.Read(0x10, buffer, sizeof(buffer));

// Write 4 bytes to register 0x20
device.Write(0x20, data, 4);
```

#### GPIO Pin Example
```cpp
GPIO_PinBase &led = create_pin();
led.Set(true);      // Turn on
bool state = led.Get();  // Check state
```

#### Timer Example
```cpp
Timer_DeviceBase timer(0);
timer.Init(1000)    // Initialize with 1000 tick period
     .Enable()      // Start timer
     .Wait(5000);   // Wait 5000 ticks
```

## Platform Integration

This library is designed to work with any embedded platform. Implement the interface classes for your target hardware:

### Example Platform Implementations

**STM32 HAL:**
```cpp
class STM32_I2C : public I2C_InterfaceBase {
    // Implementation using STM32 HAL functions
};
```

**Arduino:**
```cpp
class ArduinoI2C : public I2C_InterfaceBase {
    // Implementation using Arduino Wire library
};
```

**POSIX/Linux:**
```cpp
class LinuxI2C : public I2C_InterfaceBase {
    // Implementation using ioctl and device files
};
```

## Requirements

- **C++11** or higher compiler
- Embedded platform with peripherals (I2C, SPI, UART, GPIO, etc.)
- Standard C library (`<cstdint>`, `<cstddef>`)

### Supported Compilers

- GCC/G++ 5.0+
- Clang 3.8+
- MSVC 2015+
- Any C++11 compliant compiler

## Design Patterns

### Method Chaining (Builder Pattern)

Device methods return references to enable fluent API:

```cpp
device.Write(0x10, data1, 4)
      .Read(0x20, buffer, 8)
      .Write(0x30, data2, 2);
```

### Template Specialization

`GPIO_PortBase` is templated to support various port sizes:

```cpp
GPIO_PortBase<uint8_t>  port8;   // 8-bit port
GPIO_PortBase<uint16_t> port16;  // 16-bit port  
GPIO_PortBase<uint32_t> port32;  // 32-bit port
```

## Error Handling

### Return Codes

- **`0`** - Operation successful
- **Negative values** - Error occurred (implementation-specific error codes)

### One-Wire Result Enum

```cpp
enum class Result {
    Success = 0,
    NoDevice = 1,
    CrcMissmatch = 2,
    NotReady = 3,
    NotAllDevicesFound = 4
};
```

## Example Project Structure

```
my_embedded_project/
├── interfaces/
│   ├── interfaces.hpp
│   └── devices.hpp
├── platform/
│   ├── stm32_i2c.hpp      // Platform-specific I2C implementation
│   ├── stm32_gpio.hpp     // Platform-specific GPIO implementation
│   └── ...
├── drivers/
│   ├── bme280_sensor.hpp  // Sensor using I2C_DeviceBase
│   ├── lcd_display.hpp    // Display driver
│   └── ...
├── main.cpp
└── CMakeLists.txt
```

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs and feature requests.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

MIT License  
Copyright (c) 2025-2026 Pavel Nadein

## Contact

**Author:** Pavel Nadein  
**Email:** pavelnadein@gmail.com

## Changelog

### Version 1.0.0
- Initial release
- Communication interfaces: I2C, SPI, UART, One-Wire, Time
- Device classes: GPIO Pin/Port, I2C Device, SPI Device, Timer Device
- Comprehensive API documentation
- Support for C++11 and later
