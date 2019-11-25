---
title: How to use reference Swagger documentation - Azure Digital Twins | Microsoft Docs
description: Noções básicas sobre como usar a documentação de referência do Swagger de Gêmeos Digitais do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 3d4470a89455fd4b49fb7bb5ae26375f9cf9884c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456991"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentação de referência do Swagger de Gêmeos Digitais do Azure

Cada instância de Gêmeos Digitais do Azure provisionada inclui sua própria documentação de referência do Swagger gerada automaticamente.

[Swagger](https://swagger.io/) ou [OpenAPI](https://www.openapis.org/), une informações de API complexas em um recurso de referência interativo e agnóstico de idioma. O Swagger fornece material de referência crítico sobre quais cargas úteis, métodos HTTP e pontos de extremidade específicos do JSON devem ser usados para executar operações em uma API.

## <a name="swagger-summary"></a>Resumo do Swagger

O Swagger fornece um resumo interativo da sua API, que inclui:

* API e informações do modelo de objeto.
* Endpoints da API REST que especificam os payloads, cabeçalhos, parâmetros, caminhos de contexto e métodos HTTP necessários para solicitações.
* Teste das funcionalidades de API.
* Exemplo de informações de resposta usadas para validar e confirmar respostas HTTP.
* Informações de código de erro.

O Swagger é uma ferramenta conveniente para auxiliar no desenvolvimento e teste de chamadas feitas às APIs de Gerenciamento de Gêmeos Digitais do Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Material de referência

O material de referência do Swagger gerado automaticamente fornece uma visão geral rápida de conceitos importantes, pontos de extremidade de API de Gerenciamento disponíveis e uma descrição de cada modelo de objeto para auxiliar o desenvolvimento e teste.

Um resumo descreve a API.

[![Swagger summary and API overview information](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Os modelos de objeto da API de Gerenciamento também são listados.

[![Swagger models listed at bottom of Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Você pode selecionar cada modelo de objeto listados para um resumo detalhado dos atributos de chave.

[![Swagger models expanded to see contents of models](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Os modelos de objeto Swagger gerados são convenientes para ver todos os [objetos e APIs](./concepts-objectmodel-spatialgraph.md) dos Gêmeos Digitais do Azure. Os desenvolvedores podem usar esse recurso ao criarem soluções nos Gêmeos Digitais do Azure.

## <a name="endpoint-summary"></a>Resumo de ponto de extremidade

O Swagger também fornece uma visão geral completa de todos os pontos de extremidade que compõem as APIs de Gerenciamento.

Cada terminal listado também inclui as informações de solicitação necessárias, como:

* Parâmetros obrigatórios.
* Tipos de dados de parâmetro necessários.
* Método HTTP para acessar o recurso.

[![Swagger endpoints displayed in Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Para ver uma visão geral mais detalhada, selecione cada recurso.

## <a name="use-swagger-to-test-endpoints"></a>Use Swagger para testar endpoints

Uma das funcionalidades poderosas que o Swagger oferece é a capacidade de testar um endpoint da API diretamente através da interface do usuário da documentação.

Depois de selecionar um endpoint específico, você verá **Try it out**.

[![Swagger Try it out button](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Expanda essa seção para exibir os campos de entrada para cada parâmetro obrigatório e opcional. Insira os valores corretos e selecione **Executar**.

[![Swagger Try it out result example](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Depois de executar o teste, você pode validar os dados de resposta.

## <a name="swagger-response-data"></a>Dados de resposta do Swagger

Cada ponto de extremidade listado também inclui dados de corpo de resposta para validar seu desenvolvimento e testes. Esses exemplos incluem os códigos de status e o JSON que você deseja ver para solicitações HTTP bem-sucedidas.

[![Swagger JSON response example](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Os exemplos também incluem os códigos de erro para ajudar a depurar ou melhorar os testes com falha.

## <a name="swagger-oauth-20-authorization"></a>Autorização OAuth 2.0 do Swagger

> [!NOTE]
> * The user principal that created the Azure Digital Twins resource will have a Space Administrator role assignment and will be able to create additional role assignments for other users. Such users and their roles can be authorized to call the APIs.

1. Follow the steps in [this quickstart](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) to create and configure an Azure AD application. Alternatively, you can reuse an existing app registration.

1. Add the following **Redirect url** to your Azure AD app registration:

    [![Register Swagger redirect url in AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | name  | Substitua por | Exemplo |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Your Management REST API documentation URL found in the portal  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Copy the **Client ID** of your Azure AD app.

After completing the Azure Active Directory registration:

1. Select the **Authorize** button on your swagger page.

    [![Select the Swagger authorize button](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Paste the application ID into the **client_id** field.

    [![Swagger client_id field](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. You will then be redirected to the following success modal.

    [![Swagger redirect modal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Para saber mais sobre solicitações de teste interativas protegidas pelo OAuth 2.0, consulte a [documentação oficial](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Próximos passos

- Para ler mais sobre os modelos de objetos dos Gêmeos Digitais do Azure e o gráfico de inteligência espacial, leia [Entender os modelos de objetos do Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

- Para saber como se autenticar com sua API de gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).