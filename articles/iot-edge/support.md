---
title: Sistemas operacionais compatíveis, mecanismos de contêiner – Azure IoT Edge | Microsoft Docs
description: Saiba quais sistemas operacionais podem executar o runtime e o daemon do Azure IoT Edge, bem como os mecanismos de contêiner compatíveis com os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0fe4a13a33b6d93266d68e632864e0b61a7eaf29
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452516"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas compatíveis com o Azure IoT Edge

This article provides details about which systems and components are supported by IoT Edge, whether officially or in preview. 

If you experience problems while using the Azure IoT Edge service, there are several ways to seek support. Try one of the following channels for support:

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. As correções passam rapidamente do projeto para as atualizações do produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contêiner

Azure IoT Edge modules are implemented as containers, so IoT Edge needs a container engine to launch them. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. This container engine is based on the Moby open-source project. Docker CE e Docker EE são outros mecanismos de contêineres populares. They're also based on the Moby open-source project and are compatible with Azure IoT Edge. Microsoft provides best effort support for systems using those container engines; however, Microsoft can't ship fixes for issues in them. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.

<br>
<center>

![Moby as container runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operacionais
Azure IoT Edge runs on most operating systems that can run containers; however, all of these systems are not equally supported. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.
* Tier 1 systems are supported. For tier 1 systems, Microsoft:
    * tem esse sistema operacional em testes automatizados
    * fornece pacotes de instalação aos sistemas
* Tier 2 systems are compatible with Azure IoT Edge and can be used relatively easily. For tier 2 systems:
    * Microsoft has done ad hoc testing on the platforms or knows of a partner successfully running Azure IoT Edge on the platform
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família do sistema operacional de host sempre deverá corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Isso significa que você somente poderá usar contêineres do Linux no Linux e contêineres do Windows no Windows. When using Windows, only process isolated containers are supported, not Hyper-V isolated containers.  

<br>
<center>

![Host OS matches guest OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Camada 1

The systems listed in the following table are supported by Microsoft, either generally available or in public preview, and are tested with each new release. 

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Versão prévia pública  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Versão prévia pública |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


The Windows operating systems listed above are the requirements for devices that run Windows containers on Windows, which is the only supported configuration for production. The Azure IoT Edge installation packages for Windows allow the use of Linux containers on Windows; however, this configuration is for development and testing only. For more information, see [Use IoT Edge on Windows to run Linux containers](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Camada 2

The systems listed in the following table are considered compatible with Azure IoT Edge, but are not actively tested or maintained by Microsoft.

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 systems, including Raspian Buster, use a version of OpenSSL that IoT Edge doesn't support. Use the following command to install an earlier version before installing IoT Edge: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Máquinas virtuais
O Azure IoT Edge pode ser executado em máquinas virtuais. Using a virtual machine as an IoT Edge device is common when customers want to augment existing infrastructure with edge intelligence. A família do sistema operacional de host da VM deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. This requirement is the same as when Azure IoT Edge is run directly on a device. O Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs desenvolvidas por plataformas como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge in a VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
O Azure IoT Edge executa perfeitamente em dispositivos tão pequenos quanto um Raspberry Pi3 até um hardware de nível de servidor. Choosing the right hardware for your scenario depends on the workloads that you want to run. Tomar a decisão final do dispositivo pode ser complicado, no entanto, é possível criar facilmente protótipos de uma solução em laptops ou desktops tradicionais.

A experiência de prototipagem ajudará a guiar a seleção final do dispositivo. Questions you should consider include: 

* How many modules are in your workload?
* How many layers do your modules’ containers share?
* In what language are your modules written? 
* How much data will your modules be processing?
* Do your modules need any specialized hardware for accelerating their workloads?
* What are the desired performance characteristics of your solution?
* What is your hardware budget?
