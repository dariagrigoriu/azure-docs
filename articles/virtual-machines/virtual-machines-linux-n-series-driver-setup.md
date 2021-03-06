---
title: Azure N-series driver setup for Linux | Microsoft Docs
description: How to set up NVIDIA GPU drivers for N-series VMs running Linux in Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017

---

# Set up GPU drivers for N-series VMs running Linux

To take advantage of the GPU capabilities of Azure N-series VMs running a supported Linux distribution, you must install NVIDIA graphics drivers on each VM after deployment. Driver setup information is also available for [Windows VMs](windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


> [!IMPORTANT]
> Currently, Linux GPU support is only available on Azure NC VMs running Ubuntu Server 16.04 LTS.
> 

For N-series VM specs, storage capacities, and disk details, see [Sizes for virtual machines](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). See also [General considerations for N-series VMs](#general-considerations-for-n-series-vms).



## Install NVIDIA CUDA drivers

Here are steps to install NVIDIA drivers on Linux NC VMs from the NVIDIA CUDA Toolkit 8.0. C and C++ developers can optionally install the full Toolkit to build GPU-accelerated applications. For more information, see the [CUDA Installation Guide](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Driver download links provided here are current at time of publication. For the latest drivers, visit the [NVIDIA](http://www.nvidia.com/) website.

To install CUDA Toolkit, make an SSH connection to each VM. To verify that the system has a CUDA-capable GPU, run the following command:

```bash
lspci | grep -i NVIDIA
```
You will see output similar to the following example (showing an NVIDIA Tesla K80 card):

![lspci command output](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

Then run commands specific for your distribution.

### Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
The installation can take several minutes.

To optionally install the complete CUDA toolkit, type:

```bash
sudo apt-get install cuda
```

Reboot the VM and proceed to verify the installation.

## Verify driver installation


To query the GPU device state, SSH to the VM and run the [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) command-line utility installed with the driver. 

![NVIDIA device status](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## CUDA driver updates

We recommend that you periodically update CUDA drivers after deployment.

### Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

After the update completes, restart the VM.


[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

* We don't recommend installing X server or other systems that use the nouveau driver on Ubuntu NC VMs. Before installing NVIDIA GPU drivers, you need to disable the nouveau driver.  

* If you want to capture an image of a Linux VM on which you installed NVIDIA drivers, see [How to generalize and capture a Linux virtual machine](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## Next steps

* For more information about the NVIDIA GPUs on the N-series VMs, see:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (for Azure NC VMs)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (for Azure NV VMs)

