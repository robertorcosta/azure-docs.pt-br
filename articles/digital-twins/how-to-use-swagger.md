---
title: Como usar a documentação de referência Swagger - Azure Digital Twins | Microsoft Docs
description: Noções básicas sobre como usar a documentação de referência do Swagger de Gêmeos Digitais do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023286"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentação de referência do Swagger de Gêmeos Digitais do Azure

Cada instância de Gêmeos Digitais do Azure provisionada inclui sua própria documentação de referência do Swagger gerada automaticamente.

[Swagger](https://swagger.io/), ou [OpenAPI](https://www.openapis.org/), une informações complexas de API em um recurso de referência interativo e agnóstico de linguagem. O Swagger fornece material de referência crítico sobre quais cargas úteis, métodos HTTP e pontos de extremidade específicos do JSON devem ser usados para executar operações em uma API.

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

[![Resumo de Swagger e informações de visão geral da API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Os modelos de objeto da API de Gerenciamento também são listados.

[![Modelos swagger listados na parte inferior da Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Você pode selecionar cada modelo de objeto listados para um resumo detalhado dos atributos de chave.

[![Modelos swagger expandidos para ler o conteúdo dos modelos](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Os modelos de objeto Swagger gerados são convenientes para ler todos os [objetos e APIs](./concepts-objectmodel-spatialgraph.md)disponíveis do Azure Digital Twins . Os desenvolvedores podem usar esse recurso ao criarem soluções nos Gêmeos Digitais do Azure.

## <a name="endpoint-summary"></a>Resumo de ponto de extremidade

O Swagger também fornece uma visão geral completa de todos os pontos de extremidade que compõem as APIs de Gerenciamento.

Cada terminal listado também inclui as informações de solicitação necessárias, como:

* Parâmetros obrigatórios.
* Tipos de dados de parâmetro necessários.
* Método HTTP para acessar o recurso.

[![Pontos finais de Swagger exibidos em Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Selecione cada recurso para exibir seus conteúdos adicionais para obter uma visão geral mais detalhada.

## <a name="use-swagger-to-test-endpoints"></a>Use Swagger para testar endpoints

Uma das funcionalidades poderosas que o Swagger oferece é a capacidade de testar um endpoint da API diretamente através da interface do usuário da documentação.

Depois de selecionar um ponto final específico, o botão **Experimentá-lo** será exibido.

[![Swagger Experimente o botão](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Expanda essa seção para exibir os campos de entrada para cada parâmetro obrigatório e opcional. Insira os valores corretos e selecione **Executar**.

[![Swagger Experimente o exemplo de resultado](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Depois de executar o teste, você pode validar os dados de resposta.

## <a name="swagger-response-data"></a>Dados de resposta do Swagger

Cada ponto de extremidade listado também inclui dados de corpo de resposta para validar seu desenvolvimento e testes. Esses exemplos incluem os códigos de status e JSON para solicitações HTTP bem-sucedidas.

[![Exemplo de resposta de Swagger JSON](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Os exemplos também incluem os códigos de erro para ajudar a depurar ou melhorar os testes com falha.

## <a name="swagger-oauth-20-authorization"></a>Autorização OAuth 2.0 do Swagger

> [!NOTE]
> * O principal usuário que criou o recurso Azure Digital Twins terá uma atribuição de função administrador de espaço e poderá criar atribuições adicionais de funções para outros usuários. Esses usuários e suas funções podem ser autorizados a chamar as APIs.

1. Siga as etapas [do Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) para criar e configurar um aplicativo do Azure Active Directory. Alternativamente, você pode reutilizar um registro de aplicativo existente.

1. Adicione o seguinte **Uri de redirecionamento** ao registro do aplicativo Azure Active Directory:

    [![Registre url de redirecionamento de Swagger no AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nome  | Substitua por | Exemplo |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Sua URL de documentação da API de rest de gerenciamento encontrada no portal  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Selecione a caixa de seleção de**tokens** **de concessão** > implícita para permitir que o fluxo de subvenção implícita OAuth 2.0 seja usado. Selecione **Configurar** **e**salvar .

1. Copie o **ID** do cliente do seu aplicativo Azure Active Directory.

Depois de concluir o registro do Diretório Ativo do Azure:

1. Selecione o botão **Autorizar** em sua página de swagger.

    [![Selecione o botão de autorização de Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Cole o ID da aplicação no campo **client_id.**

    [![Campo de client_id de Swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Em seguida, você será redirecionado para o seguinte modal de sucesso.

    [![Swagger redireciona o modal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Para saber mais sobre as solicitações de teste interativamente protegidas pelo OAuth 2.0, leia a [documentação oficial](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Próximas etapas

- Para ler mais sobre os modelos de objetos dos Gêmeos Digitais do Azure e o gráfico de inteligência espacial, leia [Entender os modelos de objetos do Azure Digital Twins](./concepts-objectmodel-spatialgraph.md).

- Para saber como se autenticar com sua API de gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).
