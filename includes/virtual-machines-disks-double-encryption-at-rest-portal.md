---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f96bc753d14d53584aec6d9661e52adcd21ab764
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136042"
---
## <a name="supported-regions"></a>Regiões compatíveis

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Introdução

1. Entre no [portal do Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Você deve usar o [link fornecido](https://aka.ms/diskencryptionupdates) para acessar o portal do Azure. A criptografia dupla em repouso não está visível no momento no portal do Azure público sem usar o link.

1. Procure e selecione **conjuntos de criptografia de disco**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="texto de exemplo":::

1. Selecione **+ Adicionar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="texto de exemplo":::

1. Selecione uma das regiões com suporte.
1. Para **tipo de criptografia**, selecione **criptografia dupla com chaves**gerenciadas por plataforma e gerenciadas pelo cliente.

    > [!NOTE]
    > Depois de criar um conjunto de criptografia de disco com um tipo de criptografia específico, ele não pode ser alterado. Se você quiser usar um tipo de criptografia diferente, deverá criar um novo conjunto de criptografia de disco.

1. Preencha as informações restantes.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="texto de exemplo":::

1. Selecione um Azure Key Vault e uma chave ou crie um novo, se necessário.

    > [!NOTE]
    > Se você criar uma instância de Key Vault, deverá habilitar a exclusão reversível e limpar a proteção. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados e protegê-lo contra a perda de dados devido à exclusão acidental.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="texto de exemplo":::

1. Selecione **Criar**.
1. Navegue até o conjunto de criptografia de disco que você criou e selecione o erro que é exibido. Isso irá configurar o conjunto de criptografia de disco para funcionar.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="texto de exemplo":::

    Uma notificação deve aparecer e ter sucesso. Isso permitirá que você use o conjunto de criptografia de disco com o cofre de chaves.
    
    ![Captura de tela de permissão e atribuição de função bem-sucedidas para o cofre de chaves.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Navegue até o disco.
1. Selecione **criptografia**.
1. Para **tipo de criptografia**, selecione **criptografia dupla com chaves**gerenciadas por plataforma e gerenciadas pelo cliente.
1. Selecione o conjunto de criptografia de disco.
1. Selecione **Salvar**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="texto de exemplo":::

Agora você habilitou a criptografia dupla em repouso no disco gerenciado.
