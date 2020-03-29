---
title: Use o portal Azure para configurar chaves gerenciadas pelo cliente
titleSuffix: Cognitive Services
description: Saiba como usar o portal Azure para configurar chaves gerenciadas pelo cliente com o Azure Key Vault. As chaves gerenciadas pelo cliente permitem criar, girar, desativar e revogar controles de acesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 22bd3afcf30b8b8ebce18b22d5419d49ec8c3b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053598"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configure chaves gerenciadas pelo cliente com o Azure Key Vault usando o portal Azure

Você deve usar o Azure Key Vault para armazenar suas chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Active Directory (Azure AD), mas eles podem estar em assinaturas diferentes. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o [portal Azure](https://portal.azure.com/). Para aprender como criar um cofre de chaves usando o portal Azure, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o portal Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

O uso de chaves gerenciadas pelo cliente requer que duas propriedades sejam definidas no cofre da chave, **Soft Delete** e Não **Purga .** Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou o Azure CLI em um cofre de chaves novo ou existente.

> [!IMPORTANT]
> Se você não tiver as propriedades **Soft Delete** e **Não Purgar** ativadas e excluir sua chave, você não poderá recuperar os dados em seu recurso de Serviço Cognitivo.

Para saber como habilitar essas propriedades em um cofre de chaves existente, consulte as seções intituladas **Habilitando a exclusão suave** e habilitando a **proteção contra expurgo** em um dos seguintes artigos:

- [Como usar soft-delete com PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Como usar soft-delete com CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Apenas as chaves RSA do tamanho 2048 são suportadas com criptografia de armazenamento Azure. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente

Para habilitar as chaves gerenciadas pelo cliente no portal Azure, siga estas etapas:

1. Navegue até o recurso dos Serviços Cognitivos.
1. Na lâmina Configurações para o recurso **Serviços** Cognitivos, clique **em Criptografia**. Selecione a **opção Chaves gerenciadas pelo cliente,** conforme mostrado na figura a seguir.

    ![Captura de tela mostrando como selecionar chaves gerenciadas pelo cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Especifique uma chave

Depois de habilitar chaves gerenciadas pelo cliente, você terá a oportunidade de especificar uma chave para se associar ao recurso serviços cognitivos.

### <a name="specify-a-key-as-a-uri"></a>Especificar uma chave como URI

Para especificar uma chave como URI, siga estas etapas:

1. Para localizar a configuração URI chave no portal Azure, navegue até o cofre principal e selecione a configuração **Chaves.** Selecione a tecla desejada e clique na tecla para ver suas versões. Selecione uma versão-chave para visualizar as configurações dessa versão.
1. Copie o valor do campo **Identificador de Chaves,** que fornece o URI.

    ![Captura de tela mostrando a chave uri do cofre de chaves](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas **configurações de criptografia** para sua conta de armazenamento, escolha a opção **Inserir a opção INSERIR uri chave.**
1. Cole o URI que você copiou no campo **URI da chave.**

   ![Captura de tela mostrando como inserir o URI chave](../media/cognitive-services-encryption/ssecmk2.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especificar uma chave de um cofre de chaves

Para especificar uma chave de um cofre de chaves, primeiro certifique-se de que você tem um cofre de chaves que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estas etapas:

1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione o cofre de chaves contendo a chave que deseja usar.
1. Selecione a chave no cofre da chave.

   ![Captura de tela mostrando a opção-chave gerenciada pelo cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Salve suas alterações.

## <a name="update-the-key-version"></a>Atualize a versão-chave

Quando você criar uma nova versão de uma chave, atualize o recurso serviços cognitivos para usar a nova versão. Siga estas etapas:

1. Navegue até o recurso dos Serviços Cognitivos e exiba as configurações **de criptografia.**
1. Digite o URI para a nova versão-chave. Alternativamente, você pode selecionar o cofre de chaves e a chave novamente para atualizar a versão.
1. Salve suas alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave usada para criptografia, siga estas etapas:

1. Navegue até o recurso dos Serviços Cognitivos e exiba as configurações **de criptografia.**
1. Digite o URI para a nova tecla. Alternativamente, você pode selecionar o cofre de chaves e escolher uma nova chave.
1. Salve suas alterações.

## <a name="disable-customer-managed-keys"></a>Desativar chaves gerenciadas pelo cliente

Quando você desativa chaves gerenciadas pelo cliente, seu recurso de Serviços Cognitivos é então criptografado com chaves gerenciadas pela Microsoft. Para desativar as chaves gerenciadas pelo cliente, siga estas etapas:

1. Navegue até o recurso dos Serviços Cognitivos e exiba as configurações **de criptografia.**
1. Desmarque a caixa de seleção ao lado da **configuração use sua própria configuração de chave.**

## <a name="next-steps"></a>Próximas etapas

* [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulário de solicitação de chave gerenciado pelo cliente de serviços cognitivos](https://aka.ms/cogsvc-cmk)
* [Criptografia de dados do Face Services em repouso](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker criptografia de dados em repouso](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Criptografia de serviço de compreensão de idiomas de dados em repouso](../LUIS/luis-encryption-of-data-at-rest.md)
* [Criptografia moderador de conteúdo de dados em repouso](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Criptografia tradutora de dados em repouso](../translator/translator-encryption-of-data-at-rest.md)
* [Criptografia personalizada de dados em repouso](../personalizer/personalizer-encryption-of-data-at-rest.md)
