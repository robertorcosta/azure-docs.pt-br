---
title: Repositórios e imagens de contêiner
services: cognitive-services
author: aahill
manager: nitinme
description: Duas tabelas que representam os registros de contêiner, repositórios e nomes de imagem para todas as ofertas de serviço cognitiva.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 9025e016725a966061c557f16b610d8897c04c11
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590649"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contêiner

As tabelas a seguir são uma lista das imagens de contêiner disponíveis oferecidas pelos serviços cognitivas do Azure. Para obter uma lista completa de todos os nomes de imagem de contêiner disponíveis e suas marcas disponíveis, consulte [marcas de imagem de contêiner de serviços cognitivas](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponível para o público geral 

O MCR (registro de contêiner da Microsoft) distribui todos os contêineres geralmente disponíveis para serviços cognitivas. Os contêineres também estão disponíveis diretamente do [Hub do Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Contêiner LUIS | Registro de contêiner/repositório/nome da imagem |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Consulte [como executar e instalar contêineres do Luis](../../LUIS/luis-container-howto.md) para obter mais informações.

#### <a name="text-analytics"></a>[Análise de Texto](#tab/text-analytics)

| Contêiner de Análise de Texto | Registro de contêiner/repositório/nome da imagem |
|--|--|
| Análise de Sentimento v3 (inglês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Análise de Sentimento v3 (espanhol) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Análise de Sentimento v3 (francês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Análise de Sentimento v3 (italiano) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Análise de Sentimento v3 (alemão) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Análise de Sentimento v3 (chinês simplificado) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Análise de Sentimento v3 (chinês tradicional) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Análise de Sentimento v3 (japonês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Análise de Sentimento v3 (Português) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Análise de Sentimento v3 (Holandês) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Consulte [como executar e instalar contêineres de análise de texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) para obter mais informações.

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Visualização pública "não restrita" (registro de contêiner: `mcr.microsoft.com` )

Os seguintes contêineres de visualização estão disponíveis publicamente. O MCR (registro de contêiner da Microsoft) distribui todos os contêineres não restritos disponíveis publicamente para serviços cognitivas. Os contêineres também estão disponíveis diretamente do [Hub do Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Serviço | Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|--|
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Frases-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Detecção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Detector de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) | Detector de Anomalias | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Visualização pública "restrita" (registro de contêiner: `containerpreview.azurecr.io` )

Os seguintes contêineres de visualização restringida são hospedados no registro de visualização de contêiner e exigem que um aplicativo acesse. Consulte [processo de retenção de serviços cognitivas](../../cognitive-services-gating-process.md) para obter mais informações.

| Serviço | Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|--|
| [Pesquisa Visual Computacional](../../Computer-vision/computer-vision-how-to-install-containers.md) | Ler | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Detecção Facial](../../face/face-how-to-install-containers.md) | Detecção Facial | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Reconhecedor de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Reconhecimento de Formulários | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=stt) | Conversão de fala em texto | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=cstt) | Fala Personalizada para texto | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=tts) | Conversão de texto em fala | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=ctts) | Conversão de texto em fala personalizada | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
