---
title: Chaves gerenciadas pelo cliente ou BYOK (Bring Your Own Key) com os Serviços de Mídia
description: Este é um tutorial para usar chaves gerenciadas pelo cliente com uma conta de armazenamento dos Serviços de Mídia
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325829"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Tutorial: Usar chaves gerenciadas pelo cliente ou BYOK (Bring Your Own Key) com os Serviços de Mídia

Com a API 2020-05-01, você pode usar uma chave RSA gerenciada pelo cliente com uma conta dos Serviços de Mídia que tenha uma identidade gerenciada pelo sistema.  Este tutorial inclui uma coleção e um ambiente de do Postman para enviar solicitações REST para os serviços do Azure.  Serviços usados:

- Registro de aplicativo do Azure Active Directory para Postman
- API do Microsoft Graph
- Armazenamento do Azure
- Cofre de Chave do Azure
- Serviços de mídia

Neste tutorial, você aprenderá a usar o Postman para:

> [!div class="checklist"]
> * Obter tokens para uso com os serviços do Azure.
> * Criar um grupo de recursos e uma conta de armazenamento.
> * Criar uma conta dos Serviços de Mídia com uma identidade gerenciada pelo sistema
> * Criar um Key Vault para armazenar uma chave RSA (gerenciada pelo cliente) para uso com a conta de armazenamento.
> * Atualizar a conta dos Serviços de Mídia para usar a chave RSA com a conta de armazenamento.
> * Usar variáveis no Postman.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Registrar uma entidade de serviço com as permissões adequadas.
- Instalar o [Postman](https://www.postman.com).
- Baixe a coleção do Postman para este tutorial em [Exemplos do Azure: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrar uma entidade de serviço com as permissões necessárias

[Crie uma entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  Confira a [Opção Dois](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) para obter o segredo da entidade de serviço.  Anote o segredo em algum lugar, já que, depois que você deixar a página segredo no portal, ela ficará inacessível.

A entidade de serviço precisa das permissões a seguir para executar as tarefas neste tutorial.

![as permissões necessárias para a entidade de serviço](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Instale o Postman

Se você ainda não instalou o Postman para uso com o Azure, pode obtê-lo em [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Baixar e importar a coleção

Baixe a coleção do Postman para este tutorial em [Exemplos do Azure: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Instalação da coleção e do ambiente

1. Execute o Postman.
1. Selecione **Importar** .
1. Selecione **Carregar arquivos** .
1. Navegue até o local em que você salvou os arquivos de coleção e de ambiente.
1. Selecione o arquivo de coleção e ambiente.
1. Selecione **Abrir** .  Você verá um aviso de que os arquivos não serão importados como uma API, mas como coleções.  Está certo.  É o que você deseja.
1. Essa coleção agora aparecerá em suas Coleções como BYOK.
1. As variáveis de ambiente serão exibidas agora em seus ambientes.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Entender as solicitações da API REST na coleção

A coleção fornece as solicitações de API REST a seguir. As solicitações precisam ser enviadas na sequência fornecida, pois a maioria delas tem scripts de teste que criam dinamicamente variáveis globais para a próxima solicitação (ou a solicitação subsequente) na sequência. Não é necessário criar manualmente as variáveis globais.

No Postman, você verá essas variáveis contidas dentro de colchetes `{{ }}`.  Por exemplo, `{{bearerToken}}`.

1. Obter token do AAD – o teste define a variável global *bearerToken*
2. Obter o token do grafo – o teste define a variável global *graphToken*
3. Obter detalhes da entidade de serviço – o teste define a variável global *servicePrincipalObjectId*
4. Criar uma conta de armazenamento – o teste define a variável global *storageAccountId*
5. Criar uma conta dos Serviços de Mídia com uma Identidade Gerenciada pelo Sistema – o teste define a variável global *principalId*
6. Criar um Key Vault, concedendo acesso à entidade de serviço – o teste define a variável global *keyVaultId*
7. Obter um token do Key Vault – o teste define a variável global *keyVaultToken*
8. Criar chave RSA no cofre de chaves – o teste define a variável global *keyId*
9. Atualizar a conta dos Serviços de Mídia para usar a chave com a conta de armazenamento – não há um script de teste para essa solicitação.

## <a name="define-environment-variables"></a>Definir variáveis de ambiente

1. Alterne para o ambiente que você baixou selecionando a lista suspensa de ambientes.
1. Estabeleça suas variáveis de ambiente no Postman. Elas também são usadas como variáveis contidas dentro de colchetes `{{ }}`.  Por exemplo, `{{tenantId}}`.

    * *tenantId* = sua ID de locatário
    * *servicePrincipalId* = a ID da entidade de serviço que você estabelece com seu método favorito: portal, CLI e assim por diante.
    * *servicePrincipalSecret* = o segredo criado para a entidade de serviço
    * *subscription* = sua ID de assinatura
    * *storageName* = o nome que você deseja dar ao seu armazenamento
    * *accountName* = o nome da conta de Serviço de Mídia que você deseja usar
    * *keyVaultName* = o nome do Key Vault que você deseja usar
    * *resourceLocation* = centralus (ou onde quer que você deseje colocar seus recursos.  Essa coleção só foi testada com centralus.)
    * *resourceGroup* = o nome do grupo de recursos

    As variáveis a seguir são o padrão para trabalhar com recursos do Azure, portanto, não há necessidade de alterá-las.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>Enviar as solicitações

Depois que as variáveis de ambiente tiverem sido definidas, você poderá executar as solicitações, uma de cada vez, na sequência acima ou usar o executor do Postman para executar a coleção.

## <a name="change-the-key"></a>Alterar a chave

Os Serviços de Mídia farão a detecção automática quando a chave tiver sido alterada.  Para testar isso, crie outra versão de chave para a mesma chave. Os Serviços de Mídia devem detectar essa chave em menos de 15 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender continuar usando os recursos que criou e **não quiser continuar sendo cobrado** , exclua-os.

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber como...
> [!div class="nextstepaction"]
> [Codificar um arquivo remoto baseado em URL e transmitir o vídeo com o REST](stream-files-tutorial-with-rest.md)
