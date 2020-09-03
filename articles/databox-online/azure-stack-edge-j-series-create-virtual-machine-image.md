---
title: Criar imagens de VM para o dispositivo de GPU Azure Stack Edge
description: Descreve como criar imagens de VM do Linux ou do Windows para usá-las com o dispositivo de GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 83332c3bfa0b2b99d7333fa679fb8d398aecf8bd
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268903"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Criar imagens de VM personalizadas para o dispositivo Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Para implantar VMs no dispositivo Azure Stack Edge, você precisará conseguir criar imagens de VM personalizadas que possam ser usadas para criar VMs. Este artigo descreve as etapas necessárias para criar imagens de VM personalizadas do Linux ou do Windows que você poderá usar para implantar VMs no dispositivo Azure Stack Edge.

## <a name="vm-image-workflow"></a>Fluxo de trabalho de imagem de VM

O fluxo de trabalho exige que você crie uma máquina virtual no Azure, personalize a VM, generalize-a e baixe o VHD correspondente a ela. Esse VHD generalizado é carregado no Azure Stack Edge, o disco gerenciado é criado com base nesse VHD, a imagem é criada com base no disco gerenciado e, por fim, as VMs são criadas com base nessa imagem.   

Para obter mais informações, acesse [Implantar uma VM no dispositivo Azure Stack Edge usando o Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Criar uma imagem de VM personalizada do Windows

Execute as etapas a seguir para criar uma imagem de VM do Windows.

1. Crie uma Máquina Virtual do Windows. Para obter mais informações, acesse [Tutorial: Criar e gerenciar VMs do Windows com o Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Baixe um disco de sistema operacional existente.

    - Siga as etapas descritas em [Baixar um VHD](../virtual-machines/windows/download-vhd.md).

    - Use o comando `sysprep` a seguir em vez do que foi descrito no procedimento anterior.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Confira também [Visão geral do Sysprep (Preparação do Sistema)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Agora use este VHD para criar e implantar uma VM no dispositivo Azure Stack Edge.

## <a name="create-a-linux-custom-vm-image"></a>Criar uma imagem de VM personalizada do Linux

Execute as etapas a seguir para criar uma imagem de VM do Linux.

1. Criar uma Máquina Virtual Linux. Para obter mais informações, acesse [Tutorial: Criar e gerenciar VMs do Linux com a CLI do Azure](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Baixe um disco de sistema operacional existente](../virtual-machines/linux/download-vhd.md).

Agora use este VHD para criar e implantar uma VM no dispositivo Azure Stack Edge. Use as duas seguintes imagens do Azure Marketplace para criar imagens personalizadas do Linux:

|Nome do item  |Descrição  |Editor  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |O Ubuntu Server é a distribuição do Linux mais popular do mundo para ambientes em nuvem.|Canônico|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |O Debian GNU/Linux é uma das distribuições mais populares do Linux.     |credativ|

Para obter uma lista completa das imagens do Azure Marketplace que podem funcionar (atualmente não testadas), acesse [Itens do Azure Marketplace disponíveis para o Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Próximas etapas

[Implantar VMs no dispositivo Azure Stack Edge](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
