---
title: SKUs de VM com suporte para o Azure Policy
description: Um artigo descrevendo as SKUs VM suportadas (por Publisher, Image Offer e Image SKU) que são suportadas para as Políticas Incorporadas do Azure fornecidas pelo Backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980108"
---
# <a name="supported-vm-skus-for-azure-policy"></a>SKUs de VM com suporte para o Azure Policy

O Azure Backup fornece uma diretiva incorporada (usando a diretiva do Azure) que pode ser atribuída a **todas as VMs do Azure em um local especificado dentro de um grupo de assinatura ou de recursos**. Quando essa diretiva é atribuída a um determinado escopo, todas as novas VMs criadas nesse escopo são automaticamente configuradas para backup em um **cofre existente no mesmo local e assinatura**. A tabela abaixo lista todas as SKUs VM apoiadas por esta política.

### <a name="supported-vms"></a>**VMs com suporte**

**Nome da política:** Configure backup em VMs de um local para um cofre central existente no mesmo local

Editor de imagens | Oferta de imagem | SKU de imagem
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datacenter do Windows Server 2012 (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Datacenter do Windows Server 2012 (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centro de dados R2 do Windows Server 2012 (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centro de dados Windows Server 2012 R2 (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datacenter do Windows Server 2016 (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core-Smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centro de dados Windows Server 2016 (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo do servidor de data center do Windows Server 2019 com contêineres (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Remote Desktop Session Host 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Datacenter do Windows Server 2019 (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Núcleo de servidor de data center do Windows Server 2019 (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Núcleo do servidor de data center do Windows Server 2019 (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo do servidor de data center do Windows Server 2019 com contêineres (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Núcleo do servidor de data center do Windows Server 2019 com contêineres (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Datacenter do Windows Server 2019 (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centro de dados Windows Server 2019 com contêineres (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centro de dados Windows Server 2019 com contêineres (2019-Datacenter-com-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centro de Dados Windows Server 2019 (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnual | Datacenter-Core-1709-com-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnual | Datacenter-Core-1803-com-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Toda imagem Skus
MicrosoftSQLServer | SQL2016SP1-WS2016 | Toda imagem Skus
MicrosoftSQLServer | SQL2016-WS2016  | Toda imagem Skus
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Toda imagem Skus
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Toda imagem Skus
MicrosoftSQLServer | SQL2016-WS2012R2 | Toda imagem Skus
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Toda imagem Skus
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Toda imagem Skus
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Toda imagem Skus
MicrosoftSQLServer | SQL2014SP2-WS2012-BYOL | Toda imagem Skus
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Toda imagem Skus
MicrosoftRServer | MLServer-WS2016 | Toda imagem Skus
MicrosoftVisualStudio | Visualstudio | Toda imagem Skus
MicrosoftVisualStudio | Windows | Toda imagem Skus
MicrosoftDynamicsAX | Dynamics  | Pré-Req-AX7-Onebox-U8
microsoft-ads | windows-data-science-vm | Toda imagem Skus
MicrosoftWindowsDesktop | Windows-10 | Toda imagem Skus
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | Prioridade do SLES-HPC | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | Prioridade do SLES | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | Padrão SLES | 12.X
Canônico | UbuntuServer | 14.04.0-LTS
Canônico | UbuntuServer | 14.04.1-LTS
Canônico | UbuntuServer | 14.04.2-LTS
Canônico | UbuntuServer | 14.04.3-LTS
Canônico | UbuntuServer | 14.04.4-LTS
Canônico | UbuntuServer | 14.04.5-LTS-DIÁRIO
Canônico | UbuntuServer | 14.04.5-LTS
Canônico | UbuntuServer | 16.04-LTS-DIÁRIO
Canônico | UbuntuServer | 16.04-LTS
Canônico | UbuntuServer | 16.04.0-LTS
Canônico | UbuntuServer | 18.04-DAILY-LTS
Canônico | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | Centos-LVM | 6.X, 7.X
OpenLogic | CentOS-SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7.X
