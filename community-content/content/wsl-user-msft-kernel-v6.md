---
title: How-to use the Microsoft Linux kernel v6 on WSL2
description: Know how to build and use the Microsoft Linux kernel v6 on WSL2 instead of the stock kernel
author: nunix
ms.author: shchow
ms.service: azure
ms.topic: how-to
ms.date: 07/07/2023
ms.custom: template-how-to-pattern
content_well_notification: 
  - Human-created-Community
---

# How-to use the Microsoft Linux kernel v6 on Windows Subsystem for Linux version 2 (WSL2)

---

**Principal author**: [Nuno do Carmo](https://learn.microsoft.com/en-us/users/corsair/)

---

Windows Subsystem for Linux version 2 (WSL2) uses by default a long term support (lts) Linux kernel. While the current WSL2 kernel is based on a version 5.x of the Linux kernel, the latest lts version is the version 6.x.

> [!NOTE]
> you can find additional information about the Microsoft Linux kernel on the [WSL2 Kernel GitHub page](https://github.com/microsoft/WSL2-Linux-Kernel).

While the kernel version 5.x could be enough for the majority of users, there's specific cases where you might need to have the latest version due to additional features your applications use.

You'll see in this guide, how-to build the Microsoft Linux kernel from the version 6.x branch on GitHub and configure WSL to use your kernel instead of the stock one.

> [!WARNING]
> If you have multiple distros installed, they will all use the same kernel.

## Prerequisites

To follow this guide, you should have the following prerequisites installed and configured:

* Updated Windows 10 or 11 machine

* Windows Subsystem for Linux installed from the [Microsoft App Store](https://apps.microsoft.com/store/detail/windows-subsystem-for-linux/9P9TQF7MRM4R) or with the [MSIX package from GitHub](https://github.com/microsoft/WSL/releases/latest)

* A WSL distro installed (for example: [Ubuntu](https://apps.microsoft.com/store/detail/ubuntu/9PDXGNCFSCZV))

* [Optional] Windows Terminal installed from the [Microsoft App Store](https://apps.microsoft.com/store/detail/windows-terminal/9n0dx20hk701) or with the [MSIX package from GitHub](https://github.com/microsoft/terminal/releases/latest)

> [!NOTE]
> Ubuntu is taken as the default option, however you can use another [distro from the Microsoft App Store](https://apps.microsoft.com/store/search/linux) and adapt the commands described in this guide to the distro of your choice.

## 1 - Building the Microsoft Linux kernel v6.1.x

The first step will be to build the Microsoft Linux kernel from the version 6.1.x branch on GitHub as follows:

1. Open a WSL terminal window and launch the distro of your choice (for example: Ubuntu)

2. Clone the Microsoft Linux kernel repository from GitHub:

    ```bash
    git clone https://github.com/microsoft/WSL2-Linux-Kernel.git
    ```

3. Install the required packages to build the kernel:

    ```bash
    sudo apt update && sudo apt install build-essential flex bison libssl-dev libelf-dev
    ```

4. Change directory to the kernel source code:

    ```bash
    cd WSL2-Linux-Kernel
    ```

5. Checkout the version 6.1.x branch:

    ```bash
    git checkout v6.1.x
    ```

6. Build the kernel:

    ```bash
    make -j$(nproc) KCONFIG_CONFIG=Microsoft/config-wsl
    ```

7. Install the kernel modules and headers:

    ```bash
    sudo make modules_install headers_install
    ```

8. Copy the kernel image to the Windows file system:

    ```bash
    cp arch/x86/boot/bzImage /mnt/c/
    ```

9. Exit the WSL terminal window

## 2 - Installing the Microsoft Linux kernel v6.1.x

Once the Microsoft Linux kernel is built, you can configure WSL to use it instead of the stock kernel as follows:

1. Create or edit the file `%USERPROFILE%\.wslconfig` with the following content:

    ```bash
    [wsl2]
    kernel=C:\\bzImage
    ```

2. Open a PowerShell terminal window as Administrator

3. Stop the WSL instance:

    ```powershell
    wsl --shutdown
    ```

## 3 - Using the Microsoft Linux kernel v6.1.x

With the new Microsoft Linux kernel v6.1.x installed and configured, you can now launch your WSL2 distro and check the kernel version as follows:

1. Open a WSL terminal window and launch the distro of your choice (for example: Ubuntu)

2. Check the kernel version:

    ```bash
    uname -r
    ```

    You should see the following output:

    ```bash
    6.1.x-WSL2-Microsoft
    ```

Congratulations, you have now the latest Microsoft Linux kernel v6.1.x installed and configured on your WSL2 instance.

## Next steps

You can learn more about the WSL2 capabilities and other configurations at the [Microsoft Learn website](https://learn.microsoft.com/en-us/windows/wsl).
