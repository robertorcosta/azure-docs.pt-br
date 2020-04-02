---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6077db0a09b09f7e4bfb859902da53b173845e55
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520754"
---
### <a name="portal"></a>Portal

A configuração de chaves gerenciadas pelo cliente para seus discos exigirá que você crie recursos em um pedido específico, se você estiver fazendo isso pela primeira vez. Primeiro, você precisará criar e configurar um Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Configurando seu Cofre de Chaves Azure

1. Entre no [portal do Azure](https://portal.azure.com/) e procure por Key Vault
1. Procure e selecione **Key Vaults**.

    [![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > O cofre de chaves do Azure, o conjunto de criptografia de disco, a VM, os discos e os snapshots devem estar todos na mesma região e assinatura para que a implantação seja bem sucedida.

1. Selecione **+Adicionar** para criar um novo Key Vault.
1. Criar um novo grupo de recursos
1. Digite um nome de cofre de chave, selecione uma região e selecione um nível de preço.
1. Selecione **'Revisar + Criar',** verificar suas escolhas e, em seguida, selecione **Criar**.

    ![Captura de tela da experiência de criação do Azure Key Vault. Mostrando os valores particulares que você cria](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Assim que o cofre principal terminar de ser implantado, selecione-o.
1. Selecione **Teclas** em **Configurações**.
1. Selecione **Gerar/Importar**

    ![Captura de tela do painel de configurações do Key Vault. Mostra o botão gerar/importar dentro das configurações.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Deixe ambos os **tipos de tecla** definidos para **RSA** e **RSA Key Size** definido como **2048**.
1. Preencha as seleções restantes como quiser e selecione **Criar**.

    ![Captura de tela da criar uma lâmina de chave que aparece uma vez que o botão gerar/importar é selecionado](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Configuração do seu conjunto de criptografia de disco

Para criar e configurar conjuntos de criptografia de https://aka.ms/diskencryptionsetsdisco, você deve usar o seguinte link: . Se você estiver nas regiões do Governo Do Microsoft Azure, você deve usar este link em vez disso: [https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff). A criação do conjunto de criptografia de disco ainda não está disponível no portal global do Azure.

1. Abra o link de criptografia de disco apropriado para sua região:

    Regiões públicas:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiões do Governo Azure:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Selecione **+Adicionar**.

    ![Captura de tela da tela principal do portal de criptografia de disco. Destacando o botão Adicionar](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Selecione seu grupo de recursos, nomeie seu conjunto de criptografia e selecione a mesma região que seu cofre de chaves.
1. Selecione **o cofre e a chave da chave**.
1. Selecione o cofre de chaves e a chave que você criou anteriormente, bem como a versão.
1. Pressione **Selecionar**.
1. Selecione **Create** **'Revisar + Criar'**

    ![Captura de tela da lâmina de criação de criptografia de disco. Mostrando a assinatura, grupo de recursos, nome do conjunto de criptografia de disco, região e cofre de chaves + seletor de chaves.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Abra o conjunto de criptografia de disco assim que terminar de criar e selecione o alerta que aparece.

    ![Captura de tela do pop-up de alerta: "Para associar um disco, imagem ou instantâneo a um conjunto de criptografia de disco, você deve conceder permissões ao cofre de chaves". Selecione este alerta para continuar](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Duas notificações devem aparecer e ter sucesso. Fazer isso permitirá que você use o conjunto de criptografia de disco com seu cofre de chaves.

![Captura de tela de permissão e atribuição de função bem-sucedidas para o cofre da chave.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Implantar uma máquina virtual

Agora que você criou e configurou seu cofre de chaves e o conjunto de criptografia de disco, você pode implantar uma VM usando a criptografia.
O processo de implantação de VM é semelhante ao processo de implantação padrão, as únicas diferenças são que você precisa implantar a VM na mesma região que seus outros recursos e você opta por usar uma chave gerenciada pelo cliente.

1. Abra o link de criptografia de disco apropriado para sua região:

    Regiões públicas:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiões do Governo Azure:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)

1. Procure **máquinas virtuais** e selecione **+ Adicione** para criar uma VM.
1. Na guia **Basic,** selecione a mesma região que o conjunto de criptografia de disco e o Cofre de Chaves do Azure.
1. Preencha os outros valores na guia **Básico** como quiser.

    ![Captura de tela da experiência de criação da VM, com o valor da região destacado.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Na guia **Discos,** selecione **Criptografia em repouso com uma chave gerenciada pelo cliente**.
1. Selecione o conjunto de criptografia de disco no conjunto de **criptografia de disco.**
1. Faça as seleções restantes como quiser.

    ![Captura de tela da experiência de criação de VM, a lâmina de discos. Com o conjunto de criptografia de disco destacado.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Habilitar em um disco existente

Para gerenciar e configurar a criptografia de disco nos discos existentes, você deve usar o seguinte link: https://aka.ms/diskencryptionsets. A ativação de chaves gerenciadas pelo cliente em discos existentes ainda não está disponível no portal global do Azure.

> [!CAUTION]
> Ativar a criptografia de disco em qualquer disco conectado a uma VM exigirá que você pare a VM.

1. Abra o link de criptografia de disco apropriado para sua região:

    Regiões públicas:[https://aka.ms/diskencryptionsets](https://aka.ms/diskencryptionsets)

    Regiões do Governo Azure:[https://aka.ms/diskencryptionsetsff](https://aka.ms/diskencryptionsetsff)
    
1. Navegue até uma VM que esteja na mesma região que um de seus conjuntos de criptografia de disco.
1. Abra a VM e selecione **Stop**.

    ![Captura de tela da sobreposição principal para o seu exemplo VM. Com o botão Stop destacado](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Depois que a VM terminar de parar, selecione **Discos** e selecione o disco que deseja criptografar.

    ![Captura de tela do seu exemplo VM, com a lâmina discos aberta. O disco do SISTEMA OPERACIONAL é destacado como um exemplo de disco para você selecionar.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Selecione **Criptografia** e selecione **Criptografia em repouso com uma chave gerenciada pelo cliente** e, em seguida, selecione o conjunto de criptografia de disco na lista de parada.
1. Clique em **Salvar**.

    ![Captura de tela do seu exemplo de disco do SISTEMA OPERACIONAL. A lâmina de criptografia está aberta, a criptografia em repouso com uma chave gerenciada pelo cliente é selecionada, bem como o seu exemplo Azure Key Vault. Depois de fazer essas seleções, o botão salvar é selecionado.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Repita este processo para quaisquer outros discos anexados à VM que você gostaria de criptografar.
1. Quando seus discos terminarem de mudar para chaves gerenciadas pelo cliente, se não houver outros discos conectados que você gostaria de criptografar, você pode iniciar sua VM.
