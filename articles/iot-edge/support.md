---
title: Sistemas operacionais suportados, motores de contêineres - Azure IoT Edge
description: Saiba quais sistemas operacionais podem executar o runtime e o daemon do Azure IoT Edge, bem como os mecanismos de contêiner compatíveis com os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536931"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas compatíveis com o Azure IoT Edge

Este artigo fornece detalhes sobre quais sistemas e componentes são suportados pelo IoT Edge, oficialmente ou em pré-visualização.

Se você tiver problemas ao usar o serviço Azure IoT Edge, existem várias maneiras de buscar suporte. Experimente um dos seguintes canais de suporte:

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. As correções passam rapidamente do projeto para as atualizações do produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos através da [página Voz](https://feedback.azure.com/forums/907045-azure-iot-edge)do Usuário do produto .

## <a name="container-engines"></a>Mecanismos de contêiner

Os módulos Azure IoT Edge são implementados como contêineres, então o IoT Edge precisa de um motor de contêiner para lançá-los. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. Este motor de contêiner é baseado no projeto de código aberto Moby. Docker CE e Docker EE são outros mecanismos de contêineres populares. Eles também são baseados no projeto de código aberto Moby e são compatíveis com o Azure IoT Edge. A Microsoft oferece suporte de melhor esforço para sistemas que usam esses motores de contêineres; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.

<br>
<center>

![O motor Moby como tempo de execução do contêiner](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operacionais

O Azure IoT Edge é executado na maioria dos sistemas operacionais que podem executar contêineres; no entanto, nem todos esses sistemas são igualmente suportados. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.

* Os sistemas de nível 1 são suportados. Para sistemas de nível 1, Microsoft:
  * tem esse sistema operacional em testes automatizados
  * fornece pacotes de instalação aos sistemas
* Os sistemas de nível 2 são compatíveis com o Azure IoT Edge e podem ser usados relativamente facilmente. Para sistemas de nível 2:
  * A Microsoft fez testes informais nas plataformas ou sabe de um parceiro executando com sucesso o Azure IoT Edge na plataforma
  * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas

A família do sistema operacional de host sempre deverá corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Isso significa que você somente poderá usar contêineres do Linux no Linux e contêineres do Windows no Windows. Ao usar o Windows, apenas recipientes isolados de processo são suportados, não recipientes isolados Hyper-V.  

<br>
<center>

![Sistema operacional de host corresponde ao sistema operacional convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Camada 1

Os sistemas listados na tabela a seguir são suportados pela Microsoft, geralmente disponíveis ou em visualização pública, e são testados a cada nova versão. 

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Trecho Raspbian + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Servidor Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Servidor Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Versão prévia pública  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Servidor Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Versão prévia pública |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), build 17763 | ![Núcleo de IoT do Windows + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Os sistemas operacionais Windows listados acima são os requisitos para dispositivos que executam contêineres Windows no Windows, que é a única configuração suportada para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem o uso de contêineres Linux no Windows; no entanto, esta configuração é apenas para desenvolvimento e testes. Para obter mais informações, consulte [Use IoT Edge no Windows para executar contêineres Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Camada 2

Os sistemas listados na tabela a seguir são considerados compatíveis com o Azure IoT Edge, mas não são ativamente testados ou mantidos pela Microsoft.

| Sistema operacional | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Incorporado Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Incorporado Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Incorporado Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Incorporado Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Incorporado Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Incorporado Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rio Wind 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Sistemas Debian 10, incluindo Raspian Buster, usam uma versão do OpenSSL que o IoT Edge não suporta. Use o seguinte comando para instalar uma versão anterior antes de instalar o IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Lançamentos

Os ativos de versão do IoT Edge e as notas de lançamento estão disponíveis na página [de lançamentos do azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Esta seção reflete informações dessas notas de versão para ajudá-lo a visualizar os componentes de cada versão com mais facilidade.

Os componentes ioT edge podem ser instalados ou atualizados individualmente e são retrocompatíveis com componentes de versões mais antigas. A tabela a seguir lista os componentes incluídos em cada versão:

| Versão   | Daemon de segurança  | Hub de borda<br>Agente de borda | Libiotismo | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

O IoT Edge usa o Microsoft.Azure.Device.Client SDK. Para obter mais informações, consulte o [repo Azure IoT C# SDK GitHub](https://github.com/Azure/azure-iot-sdk-csharp) ou o [Azure SDK para conteúdo de referência .NET](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet). A lista a seguir mostra a versão do SDK do cliente que cada versão é testada contra:

* **IoT Edge 1.0.9**: Cliente SDK 1.21.1
* **IoT Edge 1.0.8**: Cliente SDK 1.20.3
* **IoT Edge 1.0.7**: Cliente SDK 1.20.1
* **IoT Edge 1.0.6**: Cliente SDK 1.17.1
* **IoT Edge 1.0.5**: Cliente SDK 1.17.1

## <a name="virtual-machines"></a>Máquinas Virtuais

O Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como um dispositivo IoT Edge é comum quando os clientes querem aumentar a infra-estrutura existente com inteligência de borda. A família do sistema operacional de host da VM deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Este requisito é o mesmo que quando o Azure IoT Edge é executado diretamente em um dispositivo. O Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs desenvolvidas por plataformas como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge em uma VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema

O Azure IoT Edge executa perfeitamente em dispositivos tão pequenos quanto um Raspberry Pi3 até um hardware de nível de servidor. Escolher o hardware certo para o seu cenário depende das cargas de trabalho que você deseja executar. Tomar a decisão final do dispositivo pode ser complicado, no entanto, é possível criar facilmente protótipos de uma solução em laptops ou desktops tradicionais.

A experiência de prototipagem ajudará a guiar a seleção final do dispositivo. As perguntas que você deve considerar incluem:

* Quantos módulos estão na sua carga de trabalho?
* Quantas camadas os contêineres de seus módulos compartilham?
* Em que idioma seus módulos estão escritos?
* Quantos dados seus módulos estarão processando?
* Seus módulos precisam de algum hardware especializado para acelerar suas cargas de trabalho?
* Quais são as características de desempenho desejadas da sua solução?
* Qual é o seu orçamento de hardware?
