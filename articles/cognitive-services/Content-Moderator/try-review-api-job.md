---
title: Use trabalhos de moderação com o console REST API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Use as operações de trabalho da API de Análise para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdo de imagem ou texto no Content Moderator do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935965"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definir e usar trabalhos de moderação (REST)

Um trabalho de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdo, fluxos de trabalho e avaliações. Este guia mostra como usar as APIs rest do trabalho para iniciar e verificar trabalhos de moderação de conteúdo. Uma vez que você entenda a estrutura das APIs, você pode facilmente portar essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Faça login ou crie uma conta no site da [ferramenta Revisão](https://contentmoderator.cognitive.microsoft.com/) de Moderador de Conteúdo.
- (Opcional) [Defina um fluxo de trabalho personalizado](./Review-Tool-User-Guide/Workflows.md) para usar com o seu trabalho; você também pode usar o fluxo de trabalho padrão.

## <a name="create-a-job"></a>Criar um trabalho

Para criar um trabalho de moderação, vá para a página de referência [do Trabalho - Crie](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) a aPI e selecione o botão para sua região de assinatura (você pode encontrá-lo na URL Endpoint na página **Credenciais** da [ferramenta Revisão).](https://contentmoderator.cognitive.microsoft.com/) Isso inicia o console da API, onde você pode facilmente construir e executar chamadas de API REST.

![Trabalho - Criar uma seleção de região de página](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Digite parâmetros de chamada REST

Digite os seguintes valores para construir a chamada REST:

- **nome da equipe**: O ID da equipe que você criou quando você configurou sua conta [de ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **ID** na tela Credenciais da ferramenta Derevisão).
- **ConteúdoTipo**: Pode ser "Imagem", "Texto" ou "Vídeo".
- **ContentId**: Uma seqüência de identificadores personalizados. essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **Nome do fluxo**de trabalho : O nome do fluxo de trabalho que você criou anteriormente (ou "padrão" para o fluxo de trabalho padrão).
- **CallbackEndpoint**: (Opcional) A URL para receber informações de retorno de chamada quando a revisão for concluída.
- **Chave de assinatura Ocp-Apim**: Sua tecla moderadora de conteúdo. Você pode encontrá-lo na guia **Configurações** da [ferramenta 'Revisar'.](https://contentmoderator.cognitive.microsoft.com)

### <a name="fill-in-the-request-body"></a>Preencha o corpo de solicitação

O corpo da sua chamada REST contém um campo, **ContentValue**. Cole o conteúdo do texto bruto se estiver moderando o texto ou digite uma URL de imagem ou vídeo se estiver moderando a imagem/vídeo. Você pode usar a seguinte URL de imagem de exemplo:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Trabalho - Criar parâmetros de consulta de console, cabeçalhos e caixa do corpo da solicitação](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Enviar a solicitação

Selecione **Enviar**. Se a operação for bem-sucedida, o **status resposta** será `200 OK`e a caixa de conteúdo **Resposta** exibirá um ID para o trabalho. Copie esta ID para usar nas etapas a seguir.

![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obter status do trabalho

Para obter o status e os detalhes de um trabalho em execução ou concluído, vá para a página de referência [job - Obter](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) a aPI e selecionar o botão para a sua região (a região em que sua chave é administrada).

![Trabalho - Obter seleção de região](images/test-drive-region.png)

Digite os parâmetros de chamada REST como na seção acima. Para esta etapa, **JobId** é a seqüência de id única que você recebeu quando criou o trabalho. Selecione **Enviar**. Se a operação for bem-sucedida, o **status resposta** será `200 OK`e a caixa de conteúdo **Resposta** exibirá o trabalho no formato JSON, como o seguinte:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Trabalho - Obter resposta de chamada REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Examine as novas revisões

Se o seu trabalho de conteúdo resultou na criação de uma revisão, você pode visualizá-lo na [ferramenta Revisar](https://contentmoderator.cognitive.microsoft.com). Selecione **'Revisar** > **vídeo de texto de**/**imagem'**/(dependendo do conteúdo que você usou).**Video** O conteúdo deve aparecer, pronto para revisão humana. Depois que um moderador humano analisa as tags e os dados de previsão atribuídos automaticamente e envia uma decisão final de moderação, a API de empregos envia todas essas informações para o ponto final de retorno de chamada designado.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como criar e consultar trabalhos de moderação de conteúdo usando a API REST. Em seguida, integre os trabalhos em um cenário de moderação de ponta a ponta, como o tutorial de moderação do [e-commerce.](./ecommerce-retail-catalog-moderation.md)