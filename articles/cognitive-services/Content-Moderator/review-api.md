---
title: Avaliações, fluxos de trabalho e conceitos de empregos - Moderador de conteúdo
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá sobre os conceitos centrais da ferramenta Revisão; revisões, fluxos de trabalho e empregos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221145"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Avaliações de moderação de conteúdo, fluxos de trabalho e empregos

O Moderador de Conteúdo combina moderação assistida por máquina com recursos humanos no loop para criar um processo ideal de moderação para cenários do mundo real. Ele faz isso através da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com)baseada em nuvem . Neste guia, você aprenderá sobre os conceitos principais da ferramenta Revisão: avaliações, fluxos de trabalho e empregos.

## <a name="reviews"></a>Análises

Em uma revisão, o conteúdo é carregado na ferramenta Revisão e aparece na guia **Revisão.** A partir daqui, os usuários podem alterar as tags aplicadas e aplicar suas próprias tags personalizadas conforme apropriado. Quando um usuário envia uma revisão, os resultados são enviados para um ponto final de retorno de chamada especificado e o conteúdo é removido do site.

![Revisar o site da ferramenta aberto em um navegador, na guia Revisar](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consulte o [guia da ferramenta Revisão](./review-tool-user-guide/review-moderated-images.md) para começar a criar avaliações ou consulte o guia da [API REST](./try-review-api-review.md) para saber como fazê-lo de forma programática.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho é um filtro personalizado baseado em nuvem para conteúdo. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar o conteúdo de diferentes maneiras e, em seguida, tomar as medidas apropriadas. Com o conector Moderador de Conteúdo, um fluxo de trabalho pode aplicar automaticamente tags de moderação e criar comentários com conteúdo enviado.

### <a name="view-workflows"></a>Ver fluxos de trabalho

Para visualizar seus fluxos de trabalho existentes, vá para a [ferramenta 'Revisar'](https://contentmoderator.cognitive.microsoft.com/) e selecione **'Fluxos de** > **trabalho '**

![Fluxo de trabalho padrão](images/default-workflow-listed.PNG)

Os fluxos de trabalho podem ser completamente descritos como strings JSON, o que os torna acessíveis programáticamente. Se você selecionar a opção **Editar** para o seu fluxo de trabalho e, em seguida, selecionar a guia **JSON,** você verá uma expressão JSON como a seguinte:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Consulte o [guia de ferramentas De revisão](./review-tool-user-guide/workflows.md) para começar a criar e usar fluxos de trabalho, ou consulte o guia rest [API](./try-review-api-workflow.md) para saber como fazê-lo de forma programática.

## <a name="jobs"></a>Trabalhos

Um trabalho de moderação serve como uma espécie de invólucro para a funcionalidade de moderação de conteúdo, fluxos de trabalho e avaliações. O trabalho verifica seu conteúdo usando a API de moderação de imagem do Moderador de Conteúdo ou a API de moderação de texto e, em seguida, verifica-o contra o fluxo de trabalho designado. Com base nos resultados do fluxo de trabalho, ele pode ou não criar uma revisão para o conteúdo na [ferramenta Revisão](./review-tool-user-guide/human-in-the-loop.md). Embora tanto as revisões quanto os fluxos de trabalho possam ser criados e configurados com suas respectivas APIs, a API de trabalho permite obter um relatório detalhado de todo o processo (que pode ser enviado para um ponto final de retorno de chamada especificado).

Consulte o [guia da API REST](./try-review-api-job.md) para começar a usar trabalhos.

## <a name="next-steps"></a>Próximas etapas

* Teste o [Console de API de Trabalho](try-review-api-job.md) e use os exemplos de código de API REST. Se você estiver familiarizado com Visual Studio e C#, consulte também o [Início rápido do .NET de Trabalhos](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [console de API de revisão](try-review-api-review.md) e use os exemplos de código da API REST. Em seguida, consulte a seção de revisões do [.NET quickstart](dotnet-sdk-quickstart.md).
* Para análises de vídeos, use o [Início rápido de análise de vídeo](video-reviews-quickstart-dotnet.md)e saiba como [adicionar transcrições para a análise de vídeo](video-transcript-reviews-quickstart-dotnet.md).
