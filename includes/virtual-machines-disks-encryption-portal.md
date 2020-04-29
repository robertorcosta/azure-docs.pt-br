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
ms.openlocfilehash: b8073240bdda38757a5e4feee66c9f54746966c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632068"
---
### <a name="portal"></a>Portal

A configuração de chaves gerenciadas pelo cliente para seus discos exigirá que você crie recursos em uma ordem específica, se estiver fazendo isso pela primeira vez. Primeiro, será necessário criar e configurar um Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Configurando sua Azure Key Vault

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Procure e selecione **cofres de chaves**.

    [![SSE-Key-Vault-portal-Search. png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Seu cofre de chaves do Azure, conjunto de criptografia de disco, VM, discos e instantâneos devem estar na mesma região e assinatura para que a implantação tenha sucesso.

1. Selecione **+ Adicionar** para criar um novo Key Vault.
1. Crie um novo grupo de recursos.
1. Insira um nome de Key Vault, selecione uma região e selecione um tipo de preço.
1. Selecione **revisão + criar**, verifique suas opções e, em seguida, selecione **criar**.

    ![Captura de tela da experiência de criação de Azure Key Vault. Mostrando os valores específicos que você criar](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Depois que o cofre de chaves concluir a implantação, selecione-o.
1. Selecione **chaves** em **configurações**.
1. Selecione **gerar/importar**.

    ![Captura de tela do painel configurações de recurso Key Vault. Mostra o botão gerar/importar dentro das configurações.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Deixe o **tipo de chave** definido como **RSA** e o **tamanho da chave RSA** definido como **2048**.
1. Preencha as seleções restantes como desejar e, em seguida, selecione **criar**.

    ![Captura de tela da folha criar uma chave que aparece uma vez que o botão gerar/importar está selecionado](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Configurando o conjunto de criptografia de disco

1. Procure por **conjuntos de criptografia de disco** e selecione-o.
1. Na folha **conjuntos de criptografia de disco** , selecione **+ Adicionar**.

    ![Captura da tela principal do portal de criptografia de disco. Realçando o botão Adicionar](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecione seu grupo de recursos, nomeie o conjunto de criptografia e selecione a mesma região que o cofre de chaves.
1. Selecione o **cofre de chaves e a chave**.
1. Selecione o cofre de chaves e a chave que você criou anteriormente, bem como a versão.
1. Pressione **Selecionar**.
1. Selecione **revisar + criar** e **criar**.

    ![Captura de tela da folha de criação de criptografia de disco. Mostrando a assinatura, o grupo de recursos, o nome do conjunto de criptografia de disco, a região e o Key Vault + seletor de chave.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Abra o conjunto de criptografia de disco depois de concluir a criação e selecione o alerta que aparece.

    ![Captura de tela de alerta pop-up: ' para associar um disco, imagem ou instantâneo a um conjunto de criptografia de disco, você deve conceder permissões para o cofre de chaves '. Selecione este alerta para continuar](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Duas notificações devem ser exibidas e bem sucedidos. Isso permitirá que você use o conjunto de criptografia de disco com o cofre de chaves.

![Captura de tela de permissão e atribuição de função bem-sucedidas para o cofre de chaves.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Agora que você criou e configurou o cofre de chaves e o conjunto de criptografia de disco, você pode implantar uma VM usando a criptografia.
O processo de implantação de VM é semelhante ao processo de implantação padrão, as únicas diferenças são que você precisa implantar a VM na mesma região que seus outros recursos e optar por usar uma chave gerenciada pelo cliente.

1. Pesquise **máquinas virtuais** e selecione **+ Adicionar** para criar uma VM.
1. Na guia **básico** , selecione a mesma região que o conjunto de criptografia de disco e Azure Key Vault.
1. Preencha os outros valores na guia **básico** como desejar.

    ![Captura de tela da experiência de criação da VM, com o valor de região realçado.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na guia **discos** , selecione **criptografia em repouso com uma chave gerenciada pelo cliente**.
1. Selecione o conjunto de criptografia de disco na lista suspensa **conjunto de criptografia de disco** .
1. Faça as seleções restantes como desejar.

    ![Captura de tela da experiência de criação da VM, a folha discos. Com a lista suspensa conjunto de criptografia de disco realçada.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Habilitar em um disco existente

> [!CAUTION]
> Habilitar a criptografia de disco em qualquer disco anexado a uma VM exigirá que você interrompa a VM.
    
1. Navegue até uma VM que está na mesma região que um de seus conjuntos de criptografia de disco.
1. Abra a VM e selecione **parar**.

    ![Captura de tela da sobreposição principal para sua VM de exemplo. Com o botão parar realçado](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Após a interrupção da VM, selecione **discos** e, em seguida, selecione o disco que você deseja criptografar.

    ![Captura de tela de sua VM de exemplo, com a folha discos aberta. O disco do sistema operacional é realçado, como um disco de exemplo para você selecionar.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selecione **criptografia** e selecione **criptografia em repouso com uma chave gerenciada pelo cliente** e, em seguida, selecione o conjunto de criptografia de disco na lista suspensa.
1. Selecione **Salvar**.

    ![Captura de tela do seu exemplo de disco do so. A folha criptografia está aberta, a criptografia em repouso com uma chave gerenciada pelo cliente é selecionada, bem como seu Azure Key Vault de exemplo. Depois de fazer essas seleções, o botão salvar é selecionado.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Repita esse processo para todos os outros discos anexados à VM que você gostaria de criptografar.
1. Quando os discos terminarem de alternar para chaves gerenciadas pelo cliente, se não houver nenhum outro disco anexado que você queira criptografar, você poderá iniciar sua VM.
