---
title: Criar um recurso de serviços cognitivas no portal do Azure
titleSuffix: Azure Cognitive Services
description: Introdução aos serviços cognitivas do Azure criando e assinando um recurso no portal do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: 14157ed22ea40a2157f696e91c59af9b58110bf2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819941"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Criar um recurso de serviços cognitivas usando o portal do Azure

Use este guia de início rápido para começar a usar os serviços cognitivas do Azure. Depois de criar um recurso de serviço cognitiva no portal do Azure, você obterá um ponto de extremidade e uma chave para autenticar seus aplicativos.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso de serviços cognitivas do Azure

1. Cria um recurso.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Recurso de vários serviços](#tab/multiservice)
    
    O recurso de vários serviços é chamado de **Serviços cognitivas** no Portal. [Crie um recurso de serviços cognitivas](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    Neste momento, o recurso de vários serviços permite o acesso aos seguintes serviços cognitivas:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Visual computacional  | Content Moderator                                    | Face               | Reconhecimento Vocal (LUIS) | Análises de texto   |
    | Tradução de Texto  | Pesquisa do Bing v7 <br>(Web, imagem, notícias, vídeo, Visual) | Pesquisa Personalizada do Bing | Pesquisa de Entidade do Bing            | Sugestão Automática do Bing |
    | Verificação Ortográfica do Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Recurso de serviço único](#tab/singleservice)

    Use os links abaixo para criar um recurso para os serviços cognitivas disponíveis:

    | Visão                      | Fala                  | Idioma                          | Decisão             | Pesquisa                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visual computacional](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Serviços de Fala](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Leitor de imersão](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detector de anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [API de Pesquisa do Bing v7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Serviço de visão personalizada](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Reconhecimento do Locutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Reconhecimento vocal (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Pesquisa Personalizada do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Detecção Facial](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [O QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Pesquisa de Entidade do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Reconhecedor de tinta](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Verificação de Ortografia do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Tradução de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Sugestão Automática do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na página **Criar**, forneça as seguintes informações:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Recurso de vários serviços](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. Por exemplo, *MyCognitiveServicesResource*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do seu recurso. Lembre-se do local do Azure, pois você pode precisar dele ao chamar os serviços cognitivas do Azure. |
    | **Tipo de preço** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá seu recurso de serviços cognitivas. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Clique em **Criar**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Recurso de serviço único](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. Por exemplo, *TextAnalyticsResource*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Localidade** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do seu recurso. Lembre-se do local do Azure, pois você pode precisar dele ao chamar os serviços cognitivas do Azure. |
    | **Tipo de preço** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá seu recurso de serviços cognitivas. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Clique em **Criar**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Obter as chaves para seu recurso

1. Depois que o recurso for implantado com êxito, clique em **ir para o recurso** em **próximas etapas**.

    ![Pesquise pelos Serviços Cognitivos](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. No painel de início rápido que é aberto, você pode acessar sua chave e ponto de extremidade.

    ![Obter chave e ponto de extremidade](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos contidos no grupo.

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos que contém o recurso a ser excluído
3. Clique com o botão direito do mouse na listagem do grupo de recursos. Selecione **Excluir grupo de recursos** e confirme.

## <a name="see-also"></a>Consulte

* [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)
* [O que são os serviços cognitivas do Azure?](Welcome.md)
* [Suporte a idioma natural](language-support.md)
* [Suporte a contêiner do Docker](cognitive-services-container-support.md)
