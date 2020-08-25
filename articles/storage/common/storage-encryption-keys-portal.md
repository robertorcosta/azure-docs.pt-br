---
title: Usar o portal do Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault para a criptografia de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799155"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente com o Azure Key Vault, usando o portal do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o [portal do Azure](https://portal.azure.com/). Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

O uso de chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure requer que duas propriedades sejam definidas no cofre de chaves, **exclusão reversível** e **não sejam limpas**. Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou CLI do Azure em um cofre de chaves novo ou existente.

Para saber como habilitar essas propriedades em um cofre de chaves existente, confira as seções intituladas **Como habilitar a exclusão temporária** e **Como habilitar a proteção contra a limpeza** em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Como usar a exclusão reversível com a CLI](../../key-vault/general/soft-delete-cli.md).

A criptografia de armazenamento do Azure dá suporte às chaves RSA e RSA-HSM de tamanhos 2048, 3072 e 4096. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue para sua conta de armazenamento.
1. Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a opção **chaves gerenciadas pelo cliente** , conforme mostrado na imagem a seguir.

    ![Captura de tela do portal mostrando a opção de criptografia](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave a ser associada à conta de armazenamento. Você também pode indicar se o armazenamento do Azure deve atualizar automaticamente a chave gerenciada pelo cliente para a versão mais recente ou se você atualizará a versão de chave manualmente.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Quando você seleciona uma chave gerenciada pelo cliente de um cofre de chaves, a atualização automática da versão de chave é habilitada. Para gerenciar manualmente a versão da chave, especifique o URI da chave e inclua a versão da chave. Para obter mais informações, consulte [especificar uma chave como um URI](#specify-a-key-as-a-uri).

Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione **selecionar um cofre de chaves e uma chave**.
1. Selecione o cofre de chaves que contém a chave que você deseja usar.
1. Selecione a chave no cofre de chaves.

   ![Captura de tela mostrando como selecionar o cofre de chaves e a chave](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Salve suas alterações.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

O armazenamento do Azure pode atualizar automaticamente a chave gerenciada pelo cliente que é usada para criptografia para usar a versão de chave mais recente. Quando a chave gerenciada pelo cliente é girada no Azure Key Vault, o armazenamento do Azure começará automaticamente a usar a versão mais recente da chave para criptografia.

> [!NOTE]
> Para girar uma chave, crie uma nova versão da chave em Azure Key Vault. O armazenamento do Azure não manipula a rotação da chave no Azure Key Vault, portanto, você precisará girar a chave manualmente ou criar uma função para girá-la em um agendamento.

Quando você especifica o URI de chave, omita a versão de chave do URI para habilitar a atualização automática para a versão de chave mais recente. Se você incluir a versão de chave no URI de chave, a atualização automática não estará habilitada e você deverá gerenciar a versão de chave por conta própria. Para obter mais informações sobre como atualizar a versão da chave, consulte [atualizar manualmente a versão da chave](#manually-update-the-key-version).

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas versões. Selecione uma versão de chave para exibir as configurações dessa versão.
1. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nas configurações de **chave de criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. Cole o URI que você copiou no campo **URI da chave** . Omita a versão de chave do URI para habilitar a atualização automática da versão de chave.

   ![Captura de tela mostrando como inserir o URI da chave](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

Depois de especificar a chave, o portal do Azure indica se a atualização automática da versão de chave está habilitada e exibe a versão de chave atualmente em uso para criptografia.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Captura de tela mostrando a atualização automática da versão de chave habilitada":::

## <a name="manually-update-the-key-version"></a>Atualizar manualmente a versão de chave

Por padrão, quando você cria uma nova versão de uma chave gerenciada pelo cliente no Key Vault, o armazenamento do Azure usa automaticamente a nova versão para criptografia com chaves gerenciadas pelo cliente, conforme descrito nas seções anteriores. Se você optar por gerenciar a versão de chave por conta própria, deverá atualizar a versão de chave que está associada à conta de armazenamento sempre que criar uma nova versão da chave.

Para atualizar a conta de armazenamento para usar a nova versão de chave, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Insira o URI para a nova versão da chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="switch-to-a-different-key"></a>Alternar para uma chave diferente

Para alterar a chave usada para a criptografia de armazenamento do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Insira o URI para a nova chave. Como alternativa, você pode selecionar o cofre de chaves e escolher uma nova chave.
1. Salve suas alterações.

## <a name="disable-customer-managed-keys"></a>Desabilitar chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, sua conta de armazenamento é novamente criptografada com chaves gerenciadas pela Microsoft. Para desabilitar as chaves gerenciadas pelo cliente, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Desmarque a caixa de seleção ao lado da configuração **usar sua própria chave** .

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
