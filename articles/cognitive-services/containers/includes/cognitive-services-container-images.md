---
title: Repositórios e imagens de contêineres
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Duas tabelas representando os registros de contêineres, repositórios e nomes de imagens para todas as ofertas do Serviço Cognitivo.
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081951"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contêineres

As tabelas abaixo são uma lista das imagens de contêineres disponíveis oferecidas pela Azure Cognitive Services. Para obter uma lista completa de todos os nomes de imagens de contêiner disponíveis e suas tags disponíveis, consulte [tags de imagem de contêiner do Cognitive Services](../container-image-tags.md). Atualmente, não existem contêineres de Serviços Cognitivos que estão geralmente disponíveis (GA). Por enquanto, até que novos anúncios sejam feitos -- os contêineres estão disponíveis como *Public Ungated* ou *Public Gated Preview*.

 - *Public Ungated*: os recipientes estão disponíveis publicamente sem um mecanismo de gating.
 - *Visualização pública :* os contêineres estão disponíveis publicamente, mas primeiro exigem solicitação formal para acessar o registro do contêiner.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Público "Ungated" (registro `mcr.microsoft.com`de contêiner: )

O Microsoft Container Registry (MCR) sindicaliza todos os contêineres "ungados" disponíveis publicamente para serviços cognitivos. Os contêineres também estão disponíveis diretamente do [hub Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

| Serviço | Contêiner | Registro de contêineres / Repositório / Nome da imagem |
|--|--|--|
| [Luis](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Análise de texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Frases-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Análise de texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Detecção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Análise de texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Análise de Sentimento | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Visualização pública "Gated" `containerpreview.azurecr.io`(registro de contêiner: )

O registro de visualização de contêineres hospeda todos os contêineres "fechados" disponíveis publicamente para serviços cognitivos. Esses contêineres exigem uma solicitação formal para acessá-los através de seu registro de contêineres.

| Serviço | Contêiner | Registro de contêineres / Repositório / Nome da imagem |
|--|--|--|
| [Detector de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) | Detector de Anomalias | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Pesquisa Visual Computacional](../../Computer-vision/computer-vision-how-to-install-containers.md) | Ler | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecimento de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecimento de Formulários | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de fala em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=cstt) | Discurso personalizado para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=ctts) | Texto-para-fala personalizado | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
