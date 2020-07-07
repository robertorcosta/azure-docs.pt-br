---
title: Serviços de back-end seguros usando a autenticação de certificado do cliente
titleSuffix: Azure API Management
description: Saiba como garantir serviços de back-end usando autenticação de certificado do cliente no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80347099"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como garantir serviços de back-end usando autenticação de certificado do cliente no Gerenciamento de API do Azure

O gerenciamento de API permite que você proteja o acesso ao serviço de back-end de uma API usando certificados de cliente. Este guia mostra como gerenciar certificados na instância do serviço Gerenciamento de API do Azure no portal do Azure. Também explica como configurar uma API para usar um certificado para acessar um serviço back-end.

Para obter mais informações sobre gerenciamento de certificados usando a API REST de gerenciamento de API, consulte <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Entidade de certificado da API REST de Gerenciamento de API do Azure</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este guia mostra como configurar sua instância de serviço de Gerenciamento de API para usar a autenticação de certificado do cliente para acessar o serviço back-end para uma API. Antes de seguir as etapas neste artigo, você deve ter o serviço de back-end configurado para autenticação de certificado de cliente ([para configurar a autenticação de certificado no serviço Azure app consulte este artigo][to configure certificate authentication in Azure WebSites refer to this article]). É necessário acessar o certificado e a senha para enviar ao serviço de Gerenciamento de API.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Carregar um certificado

> [!NOTE]
> Em vez de um certificado carregado, você pode usar um certificado armazenado no serviço de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , conforme mostrado neste [exemplo](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Adicionar certificados do cliente](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Siga as etapas abaixo para fazer upload de um novo certificado do cliente. Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte o tutorial [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance].

1. Navegue até a instância de serviço do Gerenciamento de API do Azure no portal do Azure.
2. Selecione **certificados** no menu.
3. Clique no botão **+ Adicionar**.
    ![Adicionar certificados do cliente](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Procure o certificado, forneça a ID e senha.
5. Clique em **Criar**.

> [!NOTE]
> O certificado deve estar no formato **.pfx** . Os certificados autoassinados são permitidos.

Depois que o certificado for carregado, ele será exibido nos **certificados**.  Se você tiver muitos certificados, anote a impressão digital do certificado desejado para poder [Configurar uma API para usar um certificado do cliente para a autenticação de gateway][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Para desabilitar a validação da cadeia de certificados ao usar, por exemplo, um certificado autoassinado, siga as etapas descritas neste [item](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end) das Perguntas frequentes.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Excluir um certificado do cliente

Para excluir um certificado, clique no menu de contexto **...** e selecione **Excluir** ao lado do certificado.

![Excluir certificados do cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Se o certificado está em uso por uma API, então uma tela de aviso é exibida. Para excluir o certificado, você primeiro deve remover o certificado de quaisquer APIs que são configuradas para usá-la.

![Excluir falha nos certificados do cliente](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Configurar uma API para usar um certificado do cliente para a autenticação de gateway

1. Clique em **APIs** do menu **Gerenciamento de API ** à esquerda e navegue até a API.
    ![Habilitar certificados do cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na guia **Design**, clique em um ícone de lápis da seção **Back-end**.
3. Altere as **Credenciais do Gateway** para **Client cert** e selecione o certificado na lista suspensa.
    ![Habilitar certificados do cliente](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Clique em **Salvar**.

> [!WARNING]
> Essa alteração tem efeito imediato e chama para operações desta API, que usará o certificado para autenticar no servidor back-end.


> [!TIP]
> Quando um certificado é especificado para a autenticação de gateway para o serviço back-end de uma API, ele se torna parte da política dessa API e pode ser exibido no editor da política.

## <a name="self-signed-certificates"></a>Certificados autoassinados

Se você estiver usando certificados autoassinados, será necessário desabilitar a validação da cadeia de certificados para que o Gerenciamento de API comunique-se com o sistema back-end. Caso contrário, retornará um código de erro 500. Para configurar isso, você pode usar os [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlets do PowerShell (para novo back-end) ou [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (para back-end existente) e definir o `-SkipCertificateChainValidation` parâmetro como `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
