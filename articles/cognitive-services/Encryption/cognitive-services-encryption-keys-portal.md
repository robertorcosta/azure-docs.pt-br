---
title: Usar o portal do Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Cognitive Services
description: Saiba como usar o portal do Azure para configurar chaves gerenciadas pelo cliente com Azure Key Vault. As chaves gerenciadas pelo cliente permitem criar, girar, desabilitar e revogar controles de acesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455250"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente com o Azure Key Vault usando o portal do Azure

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. O recurso de serviços cognitivas e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o [portal do Azure](https://portal.azure.com/). Para saber como criar um cofre de chaves usando o portal do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

O uso de chaves gerenciadas pelo cliente requer que duas propriedades sejam definidas no cofre de chaves, **exclusão reversível** e **não sejam limpas**. Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou CLI do Azure em um cofre de chaves novo ou existente.

> [!IMPORTANT]
> Se você não tiver a **exclusão reversível** e **não limpar** as propriedades habilitadas e excluir sua chave, não será possível recuperar os dados em seu recurso de serviço cognitiva.

Para saber como habilitar essas propriedades em um cofre de chaves existente, consulte as seções intituladas **habilitar a exclusão reversível** e **habilitar a proteção de limpeza** em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Como usar a exclusão reversível com a CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Somente as chaves RSA de tamanho 2048 têm suporte com a criptografia de armazenamento do Azure. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o recurso de serviços cognitivas.
1. Na folha **configurações** do recurso serviços cognitivas, clique em **criptografia**. Selecione a opção **chaves gerenciadas pelo cliente** , conforme mostrado na figura a seguir.

    ![Captura de tela mostrando como selecionar chaves gerenciadas pelo cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de habilitar as chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave a ser associada ao recurso de serviços cognitivas.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas versões. Selecione uma versão de chave para exibir as configurações dessa versão.
1. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas configurações de **criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. Cole o URI que você copiou no campo **URI da chave** .

   ![Captura de tela mostrando como inserir o URI da chave](../media/cognitive-services-encryption/ssecmk2.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro verifique se você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione o cofre de chaves que contém a chave que você deseja usar.
1. Selecione a chave no cofre de chaves.

   ![Captura de tela mostrando a opção de chave gerenciada pelo cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Salve suas alterações.

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Quando você cria uma nova versão de uma chave, atualize o recurso de serviços cognitivas para usar a nova versão. Siga estas etapas:

1. Navegue até o recurso de serviços cognitivas e exiba as configurações de **criptografia** .
1. Insira o URI para a nova versão de chave. Como alternativa, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="use-a-different-key"></a>Usar uma chave diferente

Para alterar a chave usada para criptografia, siga estas etapas:

1. Navegue até o recurso de serviços cognitivas e exiba as configurações de **criptografia** .
1. Insira o URI para a nova chave. Como alternativa, você pode selecionar o cofre de chaves e escolher uma nova chave.
1. Salve suas alterações.

## <a name="disable-customer-managed-keys"></a>Desabilitar chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, seu recurso de serviços cognitivas é criptografado com chaves gerenciadas pela Microsoft. Para desabilitar as chaves gerenciadas pelo cliente, siga estas etapas:

1. Navegue até o recurso de serviços cognitivas e exiba as configurações de **criptografia** .
1. Desmarque a caixa de seleção ao lado da configuração **usar sua própria chave** .

## <a name="next-steps"></a>Próximas etapas

* [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formulário de solicitação de chave gerenciada pelo cliente de serviços cognitivas](https://aka.ms/cogsvc-cmk)
* [Criptografia de serviços de face de dados em repouso](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker criptografia de dados em repouso](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Criptografia do serviço de Reconhecimento vocal de dados em repouso](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator criptografia de dados em repouso](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Criptografia do tradutor de dados em repouso](../translator/translator-encryption-of-data-at-rest.md)
* [Criptografia do personalizador de dados em repouso](../personalizer/personalizer-encryption-of-data-at-rest.md)
