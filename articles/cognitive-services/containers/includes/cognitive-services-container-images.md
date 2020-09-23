---
title: Repositórios e imagens de contêiner
services: cognitive-services
author: aahill
manager: nitinme
description: Duas tabelas que representam os registros de contêiner, repositórios e nomes de imagem para todas as ofertas de serviço cognitiva.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: d514460f3cd80b5b85604251743abbbbcd1adc8c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906996"
---
### <a name="container-repositories-and-images"></a>Repositórios e imagens de contêiner

As tabelas a seguir são uma lista das imagens de contêiner disponíveis oferecidas pelos serviços cognitivas do Azure. Para obter uma lista completa de todos os nomes de imagem de contêiner disponíveis e suas marcas disponíveis, consulte [marcas de imagem de contêiner de serviços cognitivas](../container-image-tags.md). 

#### <a name="generally-available"></a>Disponível para o público geral 

O MCR (registro de contêiner da Microsoft) distribui todos os contêineres geralmente disponíveis para serviços cognitivas. Os contêineres também estão disponíveis diretamente do [Hub do Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Consulte [como executar e instalar contêineres do Luis](../../LUIS/luis-container-howto.md) para obter mais informações.

**Análise de Texto**

| Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|
| Análise de Sentimento v3 (inglês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Análise de Sentimento v3 (espanhol) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Análise de Sentimento v3 (francês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Análise de Sentimento v3 (italiano) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Análise de Sentimento v3 (alemão) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Análise de Sentimento v3 (chinês simplificado) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Análise de Sentimento v3 (chinês tradicional) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Análise de Sentimento v3 (japonês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Análise de Sentimento v3 (Português) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Análise de Sentimento v3 (Holandês) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Consulte [como executar e instalar contêineres de análise de texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) para obter mais informações.

**Detector de Anomalias** 

| Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|
| Detector de Anomalias | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Consulte [como executar e instalar contêineres do detector de anomalias](../../anomaly-detector/anomaly-detector-container-howto.md) para obter mais informações.

**Serviço de fala**

> [!NOTE]
> Para usar os contêineres de fala, você precisará concluir um [formulário de solicitação online](https://aka.ms/csgate).

| Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|
| [Conversão de fala em texto](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Fala Personalizada para texto](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Conversão de texto em fala](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Visualização "não restringida" 

Os seguintes contêineres de visualização estão disponíveis publicamente. O MCR (registro de contêiner da Microsoft) distribui todos os contêineres não restritos disponíveis publicamente para serviços cognitivas. Os contêineres também estão disponíveis diretamente do [Hub do Docker](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Serviço | Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|--|
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extração de Frases-Chave | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Análise de Texto](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Detecção de Idioma | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Visualização "restrita"

Anteriormente, os contêineres de visualização restringida eram hospedados no `containerpreview.azurecr.io` repositório. A partir de setembro de 22 2020, esses contêineres (exceto Análise de Texto de integridade) são hospedados no MCR (registro de contêiner da Microsoft) e o download deles não requer o uso do comando Docker login. Para usar o contêiner, você precisará:

1. Preencha um [formulário de solicitação](https://aka.ms/csgate) com sua ID de assinatura e cenário de usuário do Azure. 
2. Após a aprovação, baixe o contêiner do MCR. 
3. Use a chave e o ponto de extremidade de um recurso do Azure apropriado para autenticar o contêiner em tempo de execução. 

| Serviço | Contêiner | Registro de contêiner/repositório/nome da imagem |
|--|--|--|
| [Pesquisa Visual Computacional](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leia v 3.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| [Pesquisa Visual Computacional](../../Computer-vision/computer-vision-how-to-install-containers.md) | Leia v 3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | Análise espacial | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=ctts) | Conversão de texto em fala personalizada | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=lid) | Detecção de Idioma | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [API do Serviço de Fala](../../speech-service/speech-container-howto.md?tab=ntts) | Texto em fala neural | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Análise de Texto para integridade](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Análise de Texto para integridade | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

