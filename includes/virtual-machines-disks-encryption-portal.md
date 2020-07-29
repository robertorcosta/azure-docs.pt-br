---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87176996"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Agora que você criou e configurou o cofre de chaves e o conjunto de criptografia de disco, você pode implantar uma VM usando a criptografia.
O processo de implantação de VM é semelhante ao processo de implantação padrão, as únicas diferenças são que você precisa implantar a VM na mesma região que seus outros recursos e optar por usar uma chave gerenciada pelo cliente.

1. Pesquise **máquinas virtuais** e selecione **+ Adicionar** para criar uma VM.
1. Na folha **básico** , selecione a mesma região que o conjunto de criptografia de disco e Azure Key Vault.
1. Preencha os outros valores na folha **básica** como desejar.

    ![Captura de tela da experiência de criação da VM, com o valor de região realçado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Na folha **discos** , selecione **criptografia em repouso com uma chave gerenciada pelo cliente**.
1. Selecione o conjunto de criptografia de disco na lista suspensa **conjunto de criptografia de disco** .
1. Faça as seleções restantes como desejar.

    ![Captura de tela da experiência de criação da VM, a folha discos. Com a lista suspensa conjunto de criptografia de disco realçada.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Habilitar em um disco existente

> [!CAUTION]
> Habilitar a criptografia de disco em qualquer disco anexado a uma VM exigirá que você interrompa a VM.
    
1. Navegue até uma VM que está na mesma região que um de seus conjuntos de criptografia de disco.
1. Abra a VM e selecione **parar**.

    ![Captura de tela da sobreposição principal para sua VM de exemplo, com o botão parar realçado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Após a interrupção da VM, selecione **discos** e, em seguida, selecione o disco que você deseja criptografar.

    ![Captura de tela de sua VM de exemplo, com a folha discos aberta. O disco do sistema operacional é realçado, como um disco de exemplo para você selecionar.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecione **criptografia** e selecione **criptografia em repouso com uma chave gerenciada pelo cliente** e, em seguida, selecione o conjunto de criptografia de disco na lista suspensa.
1. Clique em **Salvar**.

    ![Captura de tela do seu exemplo de disco do so. A folha criptografia está aberta, a criptografia em repouso com uma chave gerenciada pelo cliente é selecionada, bem como seu Azure Key Vault de exemplo. Depois de fazer essas seleções, o botão salvar é selecionado.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Repita esse processo para todos os outros discos anexados à VM que você gostaria de criptografar.
1. Quando os discos terminarem de alternar para chaves gerenciadas pelo cliente, se não houver nenhum outro disco anexado que você queira criptografar, você poderá iniciar sua VM.
