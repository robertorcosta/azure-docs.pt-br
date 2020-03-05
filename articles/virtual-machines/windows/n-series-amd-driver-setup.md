---
title: Instalação do driver AMD GPU da série N do Azure para Windows
description: Como configurar drivers de GPU AMD para VMs da série N que executam o Windows Server ou Windows no Azure
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269433"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalar drivers do AMD GPU em VMs da série N que executam o Windows

Para aproveitar os recursos de GPU das novas VMs da série Azure NVv4 que executam o Windows, os drivers de GPU AMD devem ser instalados. A extensão do driver AMD estará disponível nas próximas semanas. Este artigo fornece sistemas operacionais, drivers e etapas de instalação e verificação manuais com suporte.

Para especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows da GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

| Sistema operacional | Driver |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10-Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se por Área de Trabalho Remota a cada VM da série NVv4.

2. Se você for um cliente do NVv4 Preview, interrompa a VM e aguarde até que ela se mova para o estado parado (desalocado).

3. Inicie a VM e desinstale o driver de visualização executando "amdcleanuputility-x64. exe" localizado na pasta ". ..\AMDCleanUninstallUtility". O caminho exato variará com base em onde estão os arquivos de instalação do driver anteriores.  

4. Baixe e instale o driver mais recente.

5. Reinicialize a VM.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra a configuração bem-sucedida da placa MI25 do Radeon instinto em uma VM NVv4 do Azure.
<br />
![Propriedades do driver GPU](./media/n-series-amd-driver-setup/device-manager.png)

Você pode usar o DxDiag para verificar as propriedades de exibição de GPU, incluindo a RAM de vídeo. O exemplo a seguir mostra uma partição 1/oitavo da placa MI25 Radeon instinto em uma VM NVv4 do Azure.
<br />
![Propriedades do driver GPU](./media/n-series-amd-driver-setup/dxdiag.png)
