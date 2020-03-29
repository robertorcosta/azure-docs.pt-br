---
title: Crie um recurso de Serviços Cognitivos no portal Azure
titleSuffix: Azure Cognitive Services
description: Comece com os Serviços Cognitivos do Azure criando e assinando um recurso no portal Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219473"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Crie um recurso de Serviços Cognitivos usando o portal Azure

Use este quickstart para começar a usar o Azure Cognitive Services. Depois de criar um recurso de Serviço Cognitivo no portal Azure, você terá um ponto final e uma chave para autenticar seus aplicativos.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Crie um novo recurso de Serviços Cognitivos Do Azure

1. Cria um recurso.

    #### <a name="multi-service-resource"></a>[Recurso de vários serviços](#tab/multiservice)
    
    O recurso multi-serviço é chamado **de Serviços Cognitivos** no portal. [Crie um recurso de Serviços Cognitivos](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    Neste momento, o recurso multi-serviço permite o acesso aos seguintes Serviços Cognitivos:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Visual Computacional  | Content Moderator                                    | Face               | Reconhecimento Vocal (LUIS) | Análise de texto   |
    | Conversor de Texto  | Bing Search v7 <br>(Web, Imagem, Notícias, Vídeo, Visual) | Pesquisa Personalizada do Bing | Pesquisa de Entidade do Bing            | Sugestão Automática do Bing |
    | Verificação Ortográfica do Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Recurso de serviço único](#tab/singleservice)

    Use os links abaixo para criar um recurso para os Serviços Cognitivos disponíveis:

    | Visão                      | Fala                  | Idioma                          | Decisão             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visão computacional](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Serviços de Fala](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Leitor imersivo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Detector de Anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Serviço de visão personalizado](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Reconhecimento do Locutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Reconhecimento Vocal (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Moderador de conteúdo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Pesquisa personalizada de Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [Fabricante de QnA](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entity Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Reconhecimento de Tinta Digital](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Verificação Ortográfica do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Texto do Tradutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Sugestão Automática do Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na página **Criar**, forneça as seguintes informações:

    #### <a name="multi-service-resource"></a>[Recurso de vários serviços](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. Por exemplo, *MyCognitiveServicesResource*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Local** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de runtime do seu recurso. |
    | **Nível de preços** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá o recurso de serviços Cognitivos. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Clique em **Criar**.

    #### <a name="single-service-resource"></a>[Recurso de serviço único](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o recurso de serviços cognitivos. Por exemplo, *TextAnalyticsResource*. |
    | **Assinatura** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Local** | A localização da sua instância de serviço cognitivo. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de runtime do seu recurso. |
    | **Nível de preços** | O custo da sua conta de Serviços Cognitivos depende das opções escolhidas e do seu uso. Para obter mais informações, consulte a API [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá o recurso de serviços Cognitivos. Você pode criar um grupo ou adicioná-lo a um grupo pré-existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Clique em **Criar**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Obtenha as chaves para o seu recurso

1. Depois que seu recurso for implantado com sucesso, clique em **Ir para recurso** em **Próximos Passos**.

    ![Pesquise pelos Serviços Cognitivos](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. A partir do painel quickstart que se abre, você pode acessar sua chave e ponto final.

    ![Obter chave e ponto final](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. A exclusão do grupo de recursos também exclui quaisquer outros recursos contidos no grupo.

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos que contém o recurso a ser excluído
3. Clique com o botão direito do mouse na lista do grupo de recursos. Selecione **Excluir grupo de recursos** e confirme.

## <a name="see-also"></a>Confira também

* [Autenticar solicitações para os Serviços Cognitivos do Azure](authentication.md)
* [O que é o Azure Cognitive Services?](Welcome.md)
* [Suporte à linguagem natural](language-support.md)
* [Suporte ao contêiner Docker](cognitive-services-container-support.md)
