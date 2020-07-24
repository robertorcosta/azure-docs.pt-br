---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136055"
---
## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regiões compatíveis

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para poder usar a criptografia no host para suas VMs ou conjuntos de dimensionamento de máquinas virtuais, você deve obter o recurso habilitado em sua assinatura. Envie um email para encryptionAtHost@microsoft. com com suas IDs de assinatura para obter o recurso habilitado para suas assinaturas.

Entre no portal do Azure usando o [link fornecido](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Você deve usar o [link fornecido](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia no host não está visível no momento no portal do Azure público sem usar o link.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Criar um Azure Key Vault e um conjunto de criptografia de disco

Depois que o recurso estiver habilitado, você precisará configurar um Azure Key Vault e um conjunto de criptografia de disco, se ainda não tiver feito isso.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Você deve implantar uma nova VM para habilitar a criptografia no host, ela não pode ser habilitada em VMs existentes.

1. Pesquise **máquinas virtuais** e selecione **+ Adicionar** para criar uma VM.
1. Crie uma nova máquina virtual, selecione uma região apropriada e um tamanho de VM com suporte.
1. Preencha os outros valores na folha **básica** como desejar e, em seguida, vá para a folha **discos** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="texto de exemplo":::

1. Na folha **discos** , selecione **Sim** para **criptografia no host**.
1. Faça as seleções restantes como desejar.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="texto de exemplo":::

1. Conclua o processo de implantação da VM, faça seleções que se ajustam ao seu ambiente.

Agora você implantou uma VM com criptografia no host habilitado, todos os discos associados serão criptografados usando a criptografia no host.