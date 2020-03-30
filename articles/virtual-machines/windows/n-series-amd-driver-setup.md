---
title: Configuração do driver AMD da série Azure N para Windows
description: Como configurar drivers DE GPU AMD para VMs da série N executando o Windows Server ou o Windows no Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269433"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instale drivers DE GPU AMD em VMs da série N executando o Windows

Para aproveitar os recursos de GPU das novas VMs da série Azure NVv4 que executam o Windows, os drivers amd GPU devem ser instalados. A extensão do driver AMD estará disponível nas próximas semanas. Este artigo fornece sistemas operacionais suportados, drivers e etapas de instalação e verificação manuais.

Para especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows da GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

| Sistema operacional | Driver |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se por desktop remoto a cada VM da série NVv4.

2. Se você é um cliente de pré-visualização NVv4, por favor, pare a VM e espere que ela se mova para o estado Parado (Dealocado).

3. Por favor, inicie a VM e desinstale o driver de visualização executando "amdcleanuputility-x64.exe" localizado na pasta "...\AMDCleanUninstallUtility". O caminho exato variará de acordo com onde estão os arquivos de instalação do driver anteriores.  

4. Baixe e instale o driver mais recente.

5. Reinicialize a VM.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra a configuração bem sucedida da placa Radeon Instinct MI25 em um VM Azure NVv4.
<br />
![Propriedades do driver GPU](./media/n-series-amd-driver-setup/device-manager.png)

Você pode usar dxdiag para verificar as propriedades do display da GPU, incluindo a RAM de vídeo. O exemplo a seguir mostra uma partição de 1/8 da placa Radeon Instinct MI25 em um VM Azure NVv4.
<br />
![Propriedades do driver GPU](./media/n-series-amd-driver-setup/dxdiag.png)
