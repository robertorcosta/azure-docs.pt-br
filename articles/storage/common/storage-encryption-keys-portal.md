---
title: Use o portal Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar o portal Azure para configurar chaves gerenciadas pelo cliente com o Azure Key Vault para criptografia de armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 192e58b101b824ca0cc0c732e02647838be6dc35
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456474"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configure chaves gerenciadas pelo cliente com o Azure Key Vault usando o portal Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o [portal Azure](https://portal.azure.com/). Para aprender como criar um cofre de chaves usando o portal Azure, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o portal Azure](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

O uso de chaves gerenciadas pelo cliente com criptografia de armazenamento Azure requer que duas propriedades sejam definidas no cofre de chaves, **Soft Delete** e Não **Purga .** Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou o Azure CLI em um cofre de chaves novo ou existente.

Para saber como habilitar essas propriedades em um cofre de chaves existente, consulte as seções intituladas **Habilitando a exclusão suave** e habilitando a **proteção contra expurgo** em um dos seguintes artigos:

- [Como usar soft-delete com PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Como usar soft-delete com CLI](../../key-vault/general/soft-delete-cli.md).

Apenas as chaves RSA e RSA-HSM de 2048 bits são suportadas com criptografia de armazenamento Azure. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento.
1. Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Selecione a **opção Chaves gerenciadas pelo cliente,** conforme mostrado na imagem a seguir.

    ![Captura de tela do portal mostrando opção de criptografia](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Especifique uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave para associar à conta de armazenamento.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como URI, siga estas etapas:

1. Para localizar a configuração URI chave no portal Azure, navegue até o cofre principal e selecione a configuração **Chaves.** Selecione a tecla desejada e clique na tecla para ver suas versões. Selecione uma versão-chave para visualizar as configurações dessa versão.
1. Copie o valor do campo **Identificador de Chaves,** que fornece o URI.

    ![Captura de tela mostrando a chave uri do cofre de chaves](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Nas **configurações de criptografia** para sua conta de armazenamento, escolha a opção **Inserir a opção INSERIR uri chave.**
1. Cole o URI que você copiou no campo **URI da chave.**

   ![Captura de tela mostrando como inserir o URI chave](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro certifique-se de que você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione o cofre de chaves contendo a chave que deseja usar.
1. Selecione a chave no cofre da chave.

   ![Captura de tela mostrando a opção-chave gerenciada pelo cliente](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Salve suas alterações.

## <a name="update-the-key-version"></a>Atualize a versão-chave

Quando você criar uma nova versão de uma chave, atualize a conta de armazenamento para usar a nova versão. Siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações **de criptografia.**
1. Digite o URI para a nova versão-chave. Alternativamente, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave usada para criptografia de armazenamento do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações **de criptografia.**
1. Digite o URI para a nova tecla. Alternativamente, você pode selecionar o cofre de chaves e escolher uma nova chave.
1. Salve suas alterações.

## <a name="disable-customer-managed-keys"></a>Desativar chaves gerenciadas pelo cliente

Quando você desativa chaves gerenciadas pelo cliente, sua conta de armazenamento é novamente criptografada com chaves gerenciadas pela Microsoft. Para desativar as chaves gerenciadas pelo cliente, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações **de criptografia.**
1. Desmarque a caixa de seleção ao lado da **configuração use sua própria configuração de chave.**

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
