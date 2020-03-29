---
title: Crie avaliações de moderação com o console REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Use as APIs de revisão de conteúdo do Azure para criar avaliações de imagem ou texto para moderação humana.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757135"
---
# <a name="create-human-reviews-rest"></a>Criar revisões humanas (REST)

[Avaliações](./review-api.md#reviews) armazenam e exibem conteúdo para moderadores humanos avaliarem. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto final de retorno de chamada especificado. Neste guia, você aprenderá como configurar avaliações usando as APIs rest de revisão através do console da API. Uma vez que você entenda a estrutura das APIs, você pode facilmente portar essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Faça login ou crie uma conta no site da [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderador de Conteúdo.

## <a name="create-a-review"></a>Criar uma revisão

Para criar uma revisão, vá para a página de referência **[Da Revisão - Crie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** a API e selecione o botão para sua região-chave (você pode encontrá-lo na URL Endpoint na página **Credenciais** da [ferramenta Revisão).](https://contentmoderator.cognitive.microsoft.com/) Isso inicia o console da API, onde você pode facilmente construir e executar chamadas de API REST.

![Revisão - Obter seleção de região](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Digite parâmetros de chamada REST

Digite valores para **teamName**e **Ocp-Apim-Subscription-Key**:

- **nome da equipe**: O ID da equipe que você criou quando você configurou sua conta [de ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **ID** na tela Credenciais da ferramenta Derevisão).
- **Chave de assinatura Ocp-Apim**: Sua tecla moderadora de conteúdo. Você pode encontrá-lo na guia **Configurações** da [ferramenta 'Revisar'.](https://contentmoderator.cognitive.microsoft.com)

### <a name="enter-a-review-definition"></a>Insira uma definição de revisão

Editar a **caixa de corpo solicitar** para inserir a solicitação JSON com os seguintes campos:

- **Metadados**: Pares personalizados de valor de chave a serem devolvidos ao ponto final de retorno de chamada. Se a chave for um código curto definido na [ferramenta 'Revisão',](https://contentmoderator.cognitive.microsoft.com)ela será exibida como uma tag.
- **Conteúdo**: No caso do conteúdo de Imagem e Vídeo, esta é uma seqüência de URL apontando para o conteúdo. Para o conteúdo do texto, esta é a seqüência de texto real.
- **ContentId**: Uma seqüência de identificadores personalizados. essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **CallbackEndpoint**: (Opcional) A URL para receber informações de retorno de chamada quando a revisão for concluída.

O órgão de solicitação padrão mostra exemplos dos diferentes tipos de avaliações que você pode criar:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **status resposta** será `200 OK`e a caixa de conteúdo **Resposta** exibirá um ID para a revisão. Copie esta ID para usar nas etapas a seguir.

![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examine a nova revisão

Na [ferramenta Revisão,](https://contentmoderator.cognitive.microsoft.com)selecione **Revisar** > **vídeo** **de texto de**/**imagem**/(dependendo do conteúdo que você usou). O conteúdo que você enviou deve aparecer, pronto para revisão humana.

![Imagem da ferramenta de análise de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obter detalhes de revisão

Para recuperar detalhes sobre uma revisão existente, vá para a página de referência [da API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) e selecione o botão para sua região (a região em que sua chave é administrada).

![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

Digite os parâmetros de chamada REST como na seção acima. Para esta etapa, **reviewId** é a seqüência de id única que você recebeu quando criou a revisão.

![Análise - Criar console Obter resultados](images/test-drive-review-3.PNG)
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **status resposta** será `200 OK`e a caixa de conteúdo **Resposta** exibirá os detalhes da revisão no formato JSON, como o seguinte:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Observe os seguintes campos na resposta:

- **status**
- **reviewerResultTags**: Isso aparece se alguma tag tiver sido adicionada manualmente pela equipe de revisão humana (mostrada no campo **criadoBy).**
- **metadados**: Isso mostra as tags que foram inicialmente adicionadas na revisão, antes que a equipe de revisão humana fizesse alterações.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como criar avaliações de moderação de conteúdo usando a API REST. Em seguida, integre os comentários em um cenário de moderação de ponta a ponta, como o tutorial de moderação do [e-commerce.](./ecommerce-retail-catalog-moderation.md)