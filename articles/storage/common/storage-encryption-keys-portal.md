---
title: Usar o portal do Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault para a criptografia de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 4af70a4e2a698bd280c8c41018bc5aaa1bfa27f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512550"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente com o Azure Key Vault usando o portal do Azure

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

1. Navegue até sua conta de armazenamento.
1. Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a opção **chaves gerenciadas pelo cliente** , conforme mostrado na imagem a seguir.

    ![Captura de tela do portal mostrando a opção de criptografia](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave a ser associada à conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas versões. Selecione uma versão de chave para exibir as configurações dessa versão.
1. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nas configurações de **criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. Cole o URI que você copiou no campo **URI da chave** .

   ![Captura de tela mostrando como inserir o URI da chave](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro verifique se você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione o cofre de chaves que contém a chave que você deseja usar.
1. Selecione a chave no cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Salve suas alterações.

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, atualize a conta de armazenamento para usar a nova versão. Siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Insira o URI para a nova versão de chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="use-a-different-key"></a>Usar uma chave diferente

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
