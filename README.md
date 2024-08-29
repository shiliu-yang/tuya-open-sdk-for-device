# tuya-open-sdk-for-device
English | [简体中文](README_zh.md)

## Overview
tuya-open-sdk-for-device is an open source IoT development framework that supports cross-chip platforms and operating systems. It is designed based on a universal southbound interface and supports communication protocols such as Bluetooth, Wi-Fi, and Ethernet. It provides core functionalities for IoT development, including pairing, activation, control, and upgrading.
The sdk has robust security and compliance capabilities, including device authentication, data encryption, and communication encryption, meeting data compliance requirements in various countries and regions worldwide.

IoT products developed using the tuya-open-sdk-for-device, if utilizing the functionality of the tuya_cloud_service component, can make use of the powerful ecosystem provided by the Tuya APP and cloud services, and achieve interoperability with Power By Tuya devices.

At the same time, the tuya-open-sdk-for-device will continuously expand, providing more cloud platform integration features, as well as voice, video, and facial recognition capabilities.

## Getting Start

### Prerequisites
Ubuntu and Debian

```sh
$ sudo apt-get install lcov cmake-curses-gui build-essential wget git python3 python3-pip python3-venv libc6-i386 libsystemd-dev
```

### Clone the repository

```sh
$ git clone https://github.com/tuya/tuya-open-sdk-for-device.git
$ git submodule update --init
```

## Setup and Compilation

### Setting Environment Variables
```sh
$ cd tuya-open-sdk-for-device
$ export PATH=$PATH:$PWD
```
Or add the tuya-open-sdk-for-device path to your system environment variables.


tuya-open-sdk-for-device can be compiled and debugged using the tos command, which will search for the tuya-open-sdk-for-device repository based on the path set in the environment variables and execute the corresponding operations.

For detailed usage of the tos command, please refer to [tos command](./docs/en/tos_guide.md).


### Configuration 
To configure the selected examples or apps project, run the following command in the corresponding project directory for menu-driven configuration:
```sh
$ cd examples/get-started/sample_project
$ tos menuconfig
```
Configure the current project, save and exit after configuration, and then compile the project.

### Compilation
Select the corresponding project for the current compilation in examples or apps, and then run the following command to compile:
```shell
$ cd examples/get-started/sample_project
$ tos build
```
After compilation, the target files will be located in the `examples/get-started/sample_project/.build/t2/bin/t2_1.0.0` directory.

## Multi-platform Configuration
The tos tool configures multi-platform compilation through the project_build.ini file in the project engineering directory. The configuration file format is as follows:
```ini
[project:switch_demo_t2]
platform = t2

[project:switch_demo_t3]
platform = t3
```

By default, there is only 1 project in project. If you need to compile multiple projects, you need to add multiple project configurations in the project_build.ini file.

When there are multiple projects in the configuration file, the tos build command will compile multiple projects in sequence.

### Supported platform list
| name |  |
| ---- | ---- |
| Ubuntu | Can be run directly on Linux hosts such as ubuntu. |
| t2 | [https://developer.tuya.com/en/docs/iot/T2-U-module-datasheet?id=Kce1tncb80ldq](https://developer.tuya.com/en/docs/iot/T2-U-module-datasheet?id=Kce1tncb80ldq) |
| t3 | [https://developer.tuya.com/en/docs/iot/T3-U-Module-Datasheet?id=Kdd4pzscwf0il](https://developer.tuya.com/en/docs/iot/T3-U-Module-Datasheet?id=Kdd4pzscwf0il) |
|

## Sample Projects
The tuya-open-sdk-for-device provides a variety of sample projects to facilitate developers in quickly getting started and understanding the usage of the tuya-open-sdk-for-device.

```shell
$ tuya-open-sdk-for-device
├── ai
│   └── llm_demo
├── ble
│   ├── ble_central
│   └── ble_peripher
├── get-started
│   └── sample_project
├── peripherals
│   ├── adc
│   ├── gpio
│   ├── i2c
│   ├── pwm
│   ├── spi
│   ├── timer
│   └── watchdog
├── protocols
│   ├── http_client
│   ├── mqtt
│   ├── tcp_client
│   └── tcp_server
├── system
│   ├── os_event
│   ├── os_kv
│   ├── os_mutex
│   ├── os_queue
│   ├── os_semaphore
│   ├── os_sw_timer
│   └── os_thread
└── wifi
    ├── ap
    ├── low_power
    ├── scan
    └── sta
```

Each sample project includes a README.md file that provides detailed instructions on configuring, compiling, and running the project.

## Tuya Cloud Applications Project

The Tuya Cloud Application is an application provided by the Tuya IoT platform, which allows developers to quickly implement features such as remote control and device management.

`switch_demo` demonstrates a simple, cross-platform, cross-system switch example that supports multiple connections. Through the Tuya APP and Tuya Cloud Service, this switch can be remotely controlled.

1. Create a product and obtain the product PID:

Refer to the documentation [https://developer.tuya.com/cn/docs/iot-device-dev/application-creation?id=Kbxw7ket3aujc](https://developer.tuya.com/cn/docs/iot-device-dev/application-creation?id=Kbxw7ket3aujc) to create a product on [https://iot.tuya.com](https://iot.tuya.com) and obtain the PID of the created product. Replace the `TUYA_PRODUCT_KEY` macro in the `apps/tuya_cloud/switch_demo/src/tuya_config.h` file with the PID.

2. Confirm the Tuya Cloud authorization code:
tuya-open-sdk-for-device uses a dedicated authorization code for tuya-open-sdk-for-device. Using other authorization codes will not allow normal connection to the Tuya Cloud.

```shell
[switch_demo.c:220] Replace the TUYA_DEVICE_UUID and TUYA_DEVICE_AUTHKEY contents, otherwise the demo cannot work
[switch_demo.c:222] uuid uuidxxxxxxxxxxxxxxxx, authkey keyxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

The dedicated authorization code for tuya-open-sdk-for-device can be obtained through the following methods:

- Method 1: Purchase a module with a tuya-open-sdk-for-device authorization code pre-burned. The authorization code is already burned in the corresponding module at the factory and will not be lost. tuya-open-sdk-for-device reads the authorization code through the tuya_iot_license_read() interface at startup. Please confirm whether the current device has a tuya-open-sdk-for-device authorization code pre-burned.

- Method 2: If the current module is not pre-burned with a tuya-open-sdk-for-device authorization code, you can purchase an Open SDK Authorization Code through the https://platform.tuya.com/purchase/index?type=6 page and replace TUYA_DEVICE_UUID and TUYA_DEVICE_AUTHKEY in the following code with the uuid and authkey obtained after successful purchase.

![authorization_code](docs/images/en/authorization_code.png)

```c
    tuya_iot_license_t license;

    if (OPRT_OK != tuya_iot_license_read(&license)) {
        license.uuid = TUYA_DEVICE_UUID;
        license.authkey = TUYA_DEVICE_AUTHKEY;
        PR_WARN("Replace the TUYA_DEVICE_UUID and TUYA_DEVICE_AUTHKEY contents, otherwise the demo cannot work");
    }
```

> If the `tuya_iot_license_read()` interface returns OPRT_OK, it indicates that the current device has a tuya-open-sdk-for-device authorization code pre-burned. Otherwise, it indicates that the current module is not pre-burned with a tuya-open-sdk-for-device authorization code.

## FAQ
1. The supported platform for tuya-open-sdk-for-device are dynamically downloaded through subrepositories. Updating the tuya-open-sdk-for-device repository itself will not automatically update the subrepositories. If you encounter any issues with compilation, please navigate to the corresponding directory in the "platform" folder and use the `git pull` command to update, or delete the corresponding directory in the "platform" folder and download it again.

2. Activation through QR code scanning requires the product PID to support the "Device Direct Cloud" feature. Otherwise, activation will result in an error and prevent normal activation.

![qrencode](docs/images/en/qrencode.png)


## License
Distributed under the Apache License Version 2.0. For more information, see `LICENSE`.

## Contribute Code
If you are interested in the tuya-open-sdk-for-device and wish to contribute to its development and become a code contributor, please first read the [Contribution Guide](./docs/en/contribute_guide.md).

## Disclaimer and Liability Clause

Users should be clearly aware that this project may contain submodules developed by third parties. These submodules may be updated independently of this project. Considering that the frequency of updates for these submodules is uncontrollable, this project cannot guarantee that these submodules are always the latest version. Therefore, if users encounter problems related to submodules when using this project, it is recommended to update them as needed or submit an issue to this project.

If users decide to use this project for commercial purposes, they should fully recognize the potential functional and security risks involved. In this case, users should bear all responsibility for any functional and security issues, perform comprehensive functional and safety tests to ensure that it meets specific business needs. Our company does not accept any liability for direct, indirect, special, incidental, or punitive damages caused by the user's use of this project or its submodules.