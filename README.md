[![License](https://img.shields.io/github/license/Arm-Examples/Middleware_USB_FS?label=License)](https://github.com/Arm-Examples/Middleware_USB_FS/blob/main/LICENSE)
[![Build Test](https://img.shields.io/github/actions/workflow/status/Arm-Examples/Middleware_USB_FS/mw-usb-fs-ci.yml?logo=arm&logoColor=0091bd&label=Build%20USB%20Middleware)](./.github/workflows/mw-usb-fs-ci.yml)


# Using MDK-Middleware with Keil Studio

[<img src="https://github.com/Arm-Examples/.github/raw/main/profile/MiddlewareVideo.png" alt="Developing with MDK-Middleware" width="318" height="205" align="left">](
https://armkeil.blob.core.windows.net/developer/Files/videos/KeilStudio/20250729_Working_with_STM32_devices.mp4?#t=07:34 "Developing with MDK-Middleware")

This repository explains how to extend an [MDK-Middleware example](https://arm-software.github.io/MDK-Middleware/latest/General/working_with_examples.html) to a user application. This application starts with the [USB Device MassStorage example](https://arm-software.github.io/MDK-Middleware/latest/USB/usbd_example_msc.html) and adds the [File System](https://arm-software.github.io/MDK-Middleware/latest/FileSystem/index.html) for data storage on an SD Card. The BUTTON0 switches data storage access between USB Host (SD Card appears as a USB memory device on the Host computer) and the embedded application for file access.

Initial development starts on the [STM32F746G-DISCO board](https://www.keil.arm.com/packs/stm32f746g-disco_bsp-keil) with a compatible [board layer](.\Board\STM32F746G-DISCO). A [copy of this layer](.\Board\CustomHW) is then modified for the user hardware. In [USB_Device.csolution.yml](./USB_Device.csolution.yml) two target-types enable development on the DISCO board or CustomHW.

The device peripherals are configured using STM32CubeMX, and with it, A/D converter functionality is added. For debugging ST-LINK, a CMSIS-DAP adapter, or J-Link may be used.

**[Watch this video to learn more...](https://armkeil.blob.core.windows.net/developer/Files/videos/KeilStudio/20250729_Working_with_STM32_devices.mp4?#t=07:34 "Using MDK-Middleware with USB Device and File System")**

## Quick Start

1. Install [Keil Studio for VS Code](https://marketplace.visualstudio.com/items?itemName=Arm.keil-studio-pack) from the VS Code marketplace.
2. Clone this repository (for example using [Git in VS Code](https://code.visualstudio.com/docs/sourcecontrol/intro-to-git)) or download the ZIP file. Then open the base folder in VS Code.
3. Open the [CMSIS View](https://mdk-packs.github.io/vscode-cmsis-solution-docs/userinterface.html#2-main-area-of-the-cmsis-view). This should show the **USB_Device** application with the selected project **MassStorage**.
4. The related tools and software packs are downloaded and installed. Review progress with View - Output - CMSIS Solution.
5. In the CMSIS view, use the [Action buttons](https://github.com/ARM-software/vscode-cmsis-csolution?tab=readme-ov-file#action-buttons) to build, load and debug the example on the hardware.

> [!TIP]
> Examples can be explored using the [Keil MDK Community edition](https://www.keil.arm.com/keil-mdk/#mdk-v6-editions), which is free-to-use for training and evaluation.

## How it works

- Initial State (USB owns the media):

  When the application starts, `USBD_MSC0_SetMediaOwnerUSB()` is called in the function `app_main_thread()`. The storage media is then controlled by the USB interface and the host PC can read/write files to the device.

- When the Button is pressed:

  The application calls `USBD_MSC0_SetMediaOwnerFS()` which transfers media ownership from USB to the user File System interface. Now the embedded application can safely access the file system. The application reads the file `Test.txt` and once this is complete, it calls `USBD_MSC0_SetMediaOwnerUSB()` to return ownership back to USB.

## Git Workflow

This repository is configured as a Git workspace for VS Code and can be effectively used with the [VS Code Git integration](https://code.visualstudio.com/docs/sourcecontrol/overview) via the following files:

- [vcpkg-configuration.json](./vcpkg-configuration.json) controls the required tools with the [Arm Tools Environment Manager](https://marketplace.visualstudio.com/items?itemName=Arm.environment-manager).
- `.gitignore` excludes files that contain user specific setup, for example `.vscode` and `*.cbuild*.yml`

## Files and Directories

This is a list of the relevant files and directories:

| File/Directory | Description |
|----------------|-------------|
| [`.github/workflows/`](./.github/workflows)    | GitHub Action file [`mw-usb-fs-ci.yml`](./.github/workflows/mw-usb-fs-ci.yml) builds the HID, MassStorage, and VirtualCOM projects. |
| [vcpkg-configuration.json](./vcpkg-configuration.json) | List of tools installed (by Arm Tools Environment Manager) in VS Code |
| [USB_Device.csolution.yml](./USB_Device.csolution.yml) | Main solution file containing USB Device example projects |
| [HID](./HID) | USB HID Device example project  (not used for the application)  |
| [MassStorage](./MassStorage) | USB Mass Storage Device example project (adapted for user application) |
| [VirtualCOM](./VirtualCOM) | USB Virtual COM Port Device example project (not used for the application) |
| [Board](./Board) | Board support layers for STM32F746G-DISCO and custom hardware |


## References

- [Keil Studio Documentation](https://mdk-packs.github.io/vscode-cmsis-solution-docs/)
- [MDK-Middleware Pack](https://www.keil.arm.com/packs/mdk-middleware-keil)
- [MDK-Middleware Documentation](https://arm-software.github.io/MDK-Middleware/latest/General/index.html)
- [CMSIS-Toolbox - Reference Applications](https://open-cmsis-pack.github.io/cmsis-toolbox/ReferenceApplications/)
