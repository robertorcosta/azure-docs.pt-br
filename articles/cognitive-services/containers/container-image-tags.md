---
title: Marcas de imagem de contêiner dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma listagem abrangente de todas as marcas de imagem de contêiner de serviço cognitiva.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 786fde90e1bcf47e08857c26235a27862c5b3d75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878758"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Marcas de imagem de contêiner de serviços cognitivas do Azure

Os serviços cognitivas do Azure oferecem muitas imagens de contêiner. Os registros de contêiner e os repositórios correspondentes variam entre as imagens de contêiner. Cada nome de imagem de contêiner oferece várias marcas. Uma marca de imagem de contêiner é um mecanismo de controle de versão da imagem de contêiner. Este artigo destina-se a ser usado como uma referência abrangente para listar todas as imagens de contêiner de serviços cognitivas e suas marcas disponíveis.

> [!TIP]
> Ao usar [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)o, preste muita atenção à capitalização do registro de contêiner, do repositório, do nome da imagem de contêiner e da marca correspondente, pois eles diferenciam **maiúsculas de minúsculas**.

## <a name="anomaly-detector"></a>Detector de Anomalias

A imagem de contêiner do [detector de anomalias][ad-containers] pode ser `containerpreview.azurecr.io` encontrada no registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-anomaly-detector`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Visual Computacional

A imagem de contêiner [Pesquisa Visual computacional][cv-containers] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-read`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-read`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

A imagem de contêiner de [face][fa-containers] pode ser encontrada `containerpreview.azurecr.io` no registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-face`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-face`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Reconhecimento de Formulários

A imagem de contêiner do [reconhecedor de formulário][fr-containers] pode `containerpreview.azurecr.io` ser encontrada no registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-form-recognizer`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Reconhecimento Vocal (LUIS)

A imagem de contêiner [Luis][lu-containers] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `luis`. O nome da imagem de contêiner totalmente qualificado `mcr.microsoft.com/azure-cognitive-services/luis`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Fala Personalizada para texto

A imagem de contêiner de [fala personalizada para texto][sp-cstt] pode ser encontrada no registro `containerpreview.azurecr.io` de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-custom-speech-to-text`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem            | Observações |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Conversão de texto em fala personalizada

A imagem de contêiner de [texto em fala personalizado][sp-ctts] pode ser encontrada no registro `containerpreview.azurecr.io` de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-custom-text-to-speech`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem            | Observações |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Conversão de fala em texto

A imagem de contêiner de [fala em texto][sp-stt] pode ser encontrada no registro `containerpreview.azurecr.io` de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-speech-to-text`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                  | Observações                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Imagem de contêiner com `en-US` a localidade. |
| `2.1.1-amd64-ar-ae-preview` | Imagem de contêiner com `ar-AE` a localidade. |
| `2.1.1-amd64-ar-eg-preview` | Imagem de contêiner com `ar-EG` a localidade. |
| `2.1.1-amd64-ar-kw-preview` | Imagem de contêiner com `ar-KW` a localidade. |
| `2.1.1-amd64-ar-qa-preview` | Imagem de contêiner com `ar-QA` a localidade. |
| `2.1.1-amd64-ar-sa-preview` | Imagem de contêiner com `ar-SA` a localidade. |
| `2.1.1-amd64-ca-es-preview` | Imagem de contêiner com `ca-ES` a localidade. |
| `2.1.1-amd64-da-dk-preview` | Imagem de contêiner com `da-DK` a localidade. |
| `2.1.1-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `2.1.1-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `2.1.1-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `2.1.1-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `2.1.1-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `2.1.1-amd64-en-nz-preview` | Imagem de contêiner com `en-NZ` a localidade. |
| `2.1.1-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `2.1.1-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `2.1.1-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `2.1.1-amd64-fi-fi-preview` | Imagem de contêiner com `fi-FI` a localidade. |
| `2.1.1-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `2.1.1-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `2.1.1-amd64-gu-in-preview` | Imagem de contêiner com `gu-IN` a localidade. |
| `2.1.1-amd64-hi-in-preview` | Imagem de contêiner com `hi-IN` a localidade. |
| `2.1.1-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `2.1.1-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `2.1.1-amd64-ko-kr-preview` | Imagem de contêiner com `ko-KR` a localidade. |
| `2.1.1-amd64-mr-in-preview` | Imagem de contêiner com `mr-IN` a localidade. |
| `2.1.1-amd64-nb-no-preview` | Imagem de contêiner com `nb-NO` a localidade. |
| `2.1.1-amd64-nl-nl-preview` | Imagem de contêiner com `nl-NL` a localidade. |
| `2.1.1-amd64-pl-pl-preview` | Imagem de contêiner com `pl-PL` a localidade. |
| `2.1.1-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `2.1.1-amd64-pt-pt-preview` | Imagem de contêiner com `pt-PT` a localidade. |
| `2.1.1-amd64-ru-ru-preview` | Imagem de contêiner com `ru-RU` a localidade. |
| `2.1.1-amd64-sv-se-preview` | Imagem de contêiner com `sv-SE` a localidade. |
| `2.1.1-amd64-ta-in-preview` | Imagem de contêiner com `ta-IN` a localidade. |
| `2.1.1-amd64-te-in-preview` | Imagem de contêiner com `te-IN` a localidade. |
| `2.1.1-amd64-th-th-preview` | Imagem de contêiner com `th-TH` a localidade. |
| `2.1.1-amd64-tr-tr-preview` | Imagem de contêiner com `tr-TR` a localidade. |
| `2.1.1-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `2.1.1-amd64-zh-hk-preview` | Imagem de contêiner com `zh-HK` a localidade. |
| `2.1.1-amd64-zh-tw-preview` | Imagem de contêiner com `zh-TW` a localidade. |
| `2.1.0-amd64-ar-ae-preview` | Imagem de contêiner com `ar-AE` a localidade. |
| `2.1.0-amd64-ar-eg-preview` | Imagem de contêiner com `ar-EG` a localidade. |
| `2.1.0-amd64-ar-kw-preview` | Imagem de contêiner com `ar-KW` a localidade. |
| `2.1.0-amd64-ar-qa-preview` | Imagem de contêiner com `ar-QA` a localidade. |
| `2.1.0-amd64-ar-sa-preview` | Imagem de contêiner com `ar-SA` a localidade. |
| `2.1.0-amd64-ca-es-preview` | Imagem de contêiner com `ca-ES` a localidade. |
| `2.1.0-amd64-da-dk-preview` | Imagem de contêiner com `da-DK` a localidade. |
| `2.1.0-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `2.1.0-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `2.1.0-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `2.1.0-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `2.1.0-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `2.1.0-amd64-en-nz-preview` | Imagem de contêiner com `en-NZ` a localidade. |
| `2.1.0-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `2.1.0-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `2.1.0-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `2.1.0-amd64-fi-fi-preview` | Imagem de contêiner com `fi-FI` a localidade. |
| `2.1.0-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `2.1.0-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `2.1.0-amd64-gu-in-preview` | Imagem de contêiner com `gu-IN` a localidade. |
| `2.1.0-amd64-hi-in-preview` | Imagem de contêiner com `hi-IN` a localidade. |
| `2.1.0-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `2.1.0-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `2.1.0-amd64-ko-kr-preview` | Imagem de contêiner com `ko-KR` a localidade. |
| `2.1.0-amd64-mr-in-preview` | Imagem de contêiner com `mr-IN` a localidade. |
| `2.1.0-amd64-nb-no-preview` | Imagem de contêiner com `nb-NO` a localidade. |
| `2.1.0-amd64-nl-nl-preview` | Imagem de contêiner com `nl-NL` a localidade. |
| `2.1.0-amd64-pl-pl-preview` | Imagem de contêiner com `pl-PL` a localidade. |
| `2.1.0-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `2.1.0-amd64-pt-pt-preview` | Imagem de contêiner com `pt-PT` a localidade. |
| `2.1.0-amd64-ru-ru-preview` | Imagem de contêiner com `ru-RU` a localidade. |
| `2.1.0-amd64-sv-se-preview` | Imagem de contêiner com `sv-SE` a localidade. |
| `2.1.0-amd64-ta-in-preview` | Imagem de contêiner com `ta-IN` a localidade. |
| `2.1.0-amd64-te-in-preview` | Imagem de contêiner com `te-IN` a localidade. |
| `2.1.0-amd64-th-th-preview` | Imagem de contêiner com `th-TH` a localidade. |
| `2.1.0-amd64-tr-tr-preview` | Imagem de contêiner com `tr-TR` a localidade. |
| `2.1.0-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `2.1.0-amd64-zh-hk-preview` | Imagem de contêiner com `zh-HK` a localidade. |
| `2.1.0-amd64-zh-tw-preview` | Imagem de contêiner com `zh-TW` a localidade. |
| `2.0.3-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `2.0.2-amd64-ar-ae-preview` | Imagem de contêiner com `ar-AE` a localidade. |
| `2.0.2-amd64-ar-eg-preview` | Imagem de contêiner com `ar-EG` a localidade. |
| `2.0.2-amd64-ar-kw-preview` | Imagem de contêiner com `ar-KW` a localidade. |
| `2.0.2-amd64-ar-qa-preview` | Imagem de contêiner com `ar-QA` a localidade. |
| `2.0.2-amd64-ar-sa-preview` | Imagem de contêiner com `ar-SA` a localidade. |
| `2.0.2-amd64-ca-es-preview` | Imagem de contêiner com `ca-ES` a localidade. |
| `2.0.2-amd64-da-dk-preview` | Imagem de contêiner com `da-DK` a localidade. |
| `2.0.2-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `2.0.2-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `2.0.2-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `2.0.2-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `2.0.2-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `2.0.2-amd64-en-nz-preview` | Imagem de contêiner com `en-NZ` a localidade. |
| `2.0.2-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `2.0.2-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `2.0.2-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `2.0.2-amd64-fi-fi-preview` | Imagem de contêiner com `fi-FI` a localidade. |
| `2.0.2-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `2.0.2-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `2.0.2-amd64-gu-in-preview` | Imagem de contêiner com `gu-IN` a localidade. |
| `2.0.2-amd64-hi-in-preview` | Imagem de contêiner com `hi-IN` a localidade. |
| `2.0.2-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `2.0.2-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `2.0.2-amd64-ko-kr-preview` | Imagem de contêiner com `ko-KR` a localidade. |
| `2.0.2-amd64-mr-in-preview` | Imagem de contêiner com `mr-IN` a localidade. |
| `2.0.2-amd64-nb-no-preview` | Imagem de contêiner com `nb-NO` a localidade. |
| `2.0.2-amd64-nl-nl-preview` | Imagem de contêiner com `nl-NL` a localidade. |
| `2.0.2-amd64-pl-pl-preview` | Imagem de contêiner com `pl-PL` a localidade. |
| `2.0.2-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `2.0.2-amd64-pt-pt-preview` | Imagem de contêiner com `pt-PT` a localidade. |
| `2.0.2-amd64-ru-ru-preview` | Imagem de contêiner com `ru-RU` a localidade. |
| `2.0.2-amd64-sv-se-preview` | Imagem de contêiner com `sv-SE` a localidade. |
| `2.0.2-amd64-ta-in-preview` | Imagem de contêiner com `ta-IN` a localidade. |
| `2.0.2-amd64-te-in-preview` | Imagem de contêiner com `te-IN` a localidade. |
| `2.0.2-amd64-th-th-preview` | Imagem de contêiner com `th-TH` a localidade. |
| `2.0.2-amd64-tr-tr-preview` | Imagem de contêiner com `tr-TR` a localidade. |
| `2.0.2-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `2.0.2-amd64-zh-hk-preview` | Imagem de contêiner com `zh-HK` a localidade. |
| `2.0.2-amd64-zh-tw-preview` | Imagem de contêiner com `zh-TW` a localidade. |
| `2.0.1-amd64-ar-ae-preview` | Imagem de contêiner com `ar-AE` a localidade. |
| `2.0.1-amd64-ar-eg-preview` | Imagem de contêiner com `ar-EG` a localidade. |
| `2.0.1-amd64-ar-kw-preview` | Imagem de contêiner com `ar-KW` a localidade. |
| `2.0.1-amd64-ar-qa-preview` | Imagem de contêiner com `ar-QA` a localidade. |
| `2.0.1-amd64-ar-sa-preview` | Imagem de contêiner com `ar-SA` a localidade. |
| `2.0.1-amd64-ca-es-preview` | Imagem de contêiner com `ca-ES` a localidade. |
| `2.0.1-amd64-da-dk-preview` | Imagem de contêiner com `da-DK` a localidade. |
| `2.0.1-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `2.0.1-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `2.0.1-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `2.0.1-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `2.0.1-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `2.0.1-amd64-en-nz-preview` | Imagem de contêiner com `en-NZ` a localidade. |
| `2.0.1-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `2.0.1-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `2.0.1-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `2.0.1-amd64-fi-fi-preview` | Imagem de contêiner com `fi-FI` a localidade. |
| `2.0.1-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `2.0.1-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `2.0.1-amd64-gu-in-preview` | Imagem de contêiner com `gu-IN` a localidade. |
| `2.0.1-amd64-hi-in-preview` | Imagem de contêiner com `hi-IN` a localidade. |
| `2.0.1-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `2.0.1-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `2.0.1-amd64-ko-kr-preview` | Imagem de contêiner com `ko-KR` a localidade. |
| `2.0.1-amd64-mr-in-preview` | Imagem de contêiner com `mr-IN` a localidade. |
| `2.0.1-amd64-nb-no-preview` | Imagem de contêiner com `nb-NO` a localidade. |
| `2.0.1-amd64-nl-nl-preview` | Imagem de contêiner com `nl-NL` a localidade. |
| `2.0.1-amd64-pl-pl-preview` | Imagem de contêiner com `pl-PL` a localidade. |
| `2.0.1-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `2.0.1-amd64-pt-pt-preview` | Imagem de contêiner com `pt-PT` a localidade. |
| `2.0.1-amd64-ru-ru-preview` | Imagem de contêiner com `ru-RU` a localidade. |
| `2.0.1-amd64-sv-se-preview` | Imagem de contêiner com `sv-SE` a localidade. |
| `2.0.1-amd64-ta-in-preview` | Imagem de contêiner com `ta-IN` a localidade. |
| `2.0.1-amd64-te-in-preview` | Imagem de contêiner com `te-IN` a localidade. |
| `2.0.1-amd64-th-th-preview` | Imagem de contêiner com `th-TH` a localidade. |
| `2.0.1-amd64-tr-tr-preview` | Imagem de contêiner com `tr-TR` a localidade. |
| `2.0.1-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `2.0.1-amd64-zh-hk-preview` | Imagem de contêiner com `zh-HK` a localidade. |
| `2.0.1-amd64-zh-tw-preview` | Imagem de contêiner com `zh-TW` a localidade. |
| `2.0.0-amd64-ar-eg-preview` | Imagem de contêiner com `ar-EG` a localidade. |
| `2.0.0-amd64-ca-es-preview` | Imagem de contêiner com `ca-ES` a localidade. |
| `2.0.0-amd64-da-dk-preview` | Imagem de contêiner com `da-DK` a localidade. |
| `2.0.0-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `2.0.0-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `2.0.0-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `2.0.0-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `2.0.0-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `2.0.0-amd64-en-nz-preview` | Imagem de contêiner com `en-NZ` a localidade. |
| `2.0.0-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `2.0.0-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `2.0.0-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `2.0.0-amd64-fi-fi-preview` | Imagem de contêiner com `fi-FI` a localidade. |
| `2.0.0-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `2.0.0-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `2.0.0-amd64-hi-in-preview` | Imagem de contêiner com `hi-IN` a localidade. |
| `2.0.0-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `2.0.0-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `2.0.0-amd64-ko-kr-preview` | Imagem de contêiner com `ko-KR` a localidade. |
| `2.0.0-amd64-nb-no-preview` | Imagem de contêiner com `nb-NO` a localidade. |
| `2.0.0-amd64-nl-nl-preview` | Imagem de contêiner com `nl-NL` a localidade. |
| `2.0.0-amd64-pl-pl-preview` | Imagem de contêiner com `pl-PL` a localidade. |
| `2.0.0-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `2.0.0-amd64-pt-pt-preview` | Imagem de contêiner com `pt-PT` a localidade. |
| `2.0.0-amd64-ru-ru-preview` | Imagem de contêiner com `ru-RU` a localidade. |
| `2.0.0-amd64-sv-se-preview` | Imagem de contêiner com `sv-SE` a localidade. |
| `2.0.0-amd64-th-th-preview` | Imagem de contêiner com `th-TH` a localidade. |
| `2.0.0-amd64-tr-tr-preview` | Imagem de contêiner com `tr-TR` a localidade. |
| `2.0.0-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `2.0.0-amd64-zh-hk-preview` | Imagem de contêiner com `zh-HK` a localidade. |
| `2.0.0-amd64-zh-tw-preview` | Imagem de contêiner com `zh-TW` a localidade. |
| `1.2.0-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `1.2.0-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `1.2.0-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `1.2.0-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `1.2.0-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `1.2.0-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `1.2.0-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `1.2.0-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `1.2.0-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `1.2.0-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `1.2.0-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `1.2.0-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `1.2.0-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `1.2.0-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `1.1.3-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `1.1.3-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `1.1.3-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `1.1.3-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `1.1.3-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `1.1.3-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `1.1.3-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `1.1.3-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `1.1.3-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `1.1.3-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `1.1.3-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `1.1.3-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `1.1.3-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `1.1.3-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `1.1.2-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `1.1.2-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `1.1.2-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `1.1.2-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `1.1.2-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `1.1.2-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `1.1.2-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `1.1.2-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `1.1.2-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `1.1.2-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `1.1.2-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `1.1.2-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `1.1.2-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `1.1.2-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `1.1.1-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `1.1.1-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `1.1.1-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `1.1.1-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `1.1.1-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `1.1.1-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `1.1.1-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `1.1.1-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `1.1.1-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `1.1.1-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `1.1.1-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `1.1.1-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `1.1.1-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `1.1.1-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `1.1.0-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `1.1.0-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `1.1.0-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `1.1.0-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `1.1.0-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `1.1.0-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `1.1.0-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `1.1.0-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `1.1.0-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `1.1.0-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `1.1.0-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `1.1.0-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `1.1.0-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `1.1.0-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |
| `1.0.0-amd64-de-de-preview` | Imagem de contêiner com `de-DE` a localidade. |
| `1.0.0-amd64-en-au-preview` | Imagem de contêiner com `en-AU` a localidade. |
| `1.0.0-amd64-en-ca-preview` | Imagem de contêiner com `en-CA` a localidade. |
| `1.0.0-amd64-en-gb-preview` | Imagem de contêiner com `en-GB` a localidade. |
| `1.0.0-amd64-en-in-preview` | Imagem de contêiner com `en-IN` a localidade. |
| `1.0.0-amd64-en-us-preview` | Imagem de contêiner com `en-US` a localidade. |
| `1.0.0-amd64-es-es-preview` | Imagem de contêiner com `es-ES` a localidade. |
| `1.0.0-amd64-es-mx-preview` | Imagem de contêiner com `es-MX` a localidade. |
| `1.0.0-amd64-fr-ca-preview` | Imagem de contêiner com `fr-CA` a localidade. |
| `1.0.0-amd64-fr-fr-preview` | Imagem de contêiner com `fr-FR` a localidade. |
| `1.0.0-amd64-it-it-preview` | Imagem de contêiner com `it-IT` a localidade. |
| `1.0.0-amd64-ja-jp-preview` | Imagem de contêiner com `ja-JP` a localidade. |
| `1.0.0-amd64-pt-br-preview` | Imagem de contêiner com `pt-BR` a localidade. |
| `1.0.0-amd64-zh-cn-preview` | Imagem de contêiner com `zh-CN` a localidade. |

## <a name="text-to-speech"></a>Conversão de texto em fala

A imagem de contêiner de [conversão de texto em fala][sp-tts] pode ser encontrada `containerpreview.azurecr.io` no registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-text-to-speech`. O nome da imagem de contêiner totalmente qualificado `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com `en-US` a localidade `en-US-JessaRUS` e a voz.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Imagem de contêiner com `ar-EG` a localidade `ar-EG-Hoda` e a voz.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Imagem de contêiner com `ar-SA` a localidade `ar-SA-Naayf` e a voz.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Imagem de contêiner com `bg-BG` a localidade `bg-BG-Ivan` e a voz.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Imagem de contêiner com `ca-ES` a localidade `ca-ES-HerenaRUS` e a voz.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Imagem de contêiner com `cs-CZ` a localidade `cs-CZ-Jakub` e a voz.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Imagem de contêiner com `da-DK` a localidade `da-DK-HelleRUS` e a voz.        |
| `1.3.0-amd64-de-at-michael-preview`         | Imagem de contêiner com `de-AT` a localidade `de-AT-Michael` e a voz.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Imagem de contêiner com `de-CH` a localidade `de-CH-Karsten` e a voz.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com `de-DE` a localidade `de-DE-Hedda` e a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com `de-DE` a localidade `de-DE-Hedda` e a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com `de-DE` a localidade `de-DE-HeddaRUS` e a voz.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com `de-DE` a localidade `de-DE-Stefan-Apollo` e a voz.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Imagem de contêiner com `el-GR` a localidade `el-GR-Stefanos` e a voz.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com `en-AU` a localidade `en-AU-Catherine` e a voz.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com `en-AU` a localidade `en-AU-HayleyRUS` e a voz.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Imagem de contêiner com `en-CA` a localidade `en-CA-HeatherRUS` e a voz.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Imagem de contêiner com `en-CA` a localidade `en-CA-Linda` e a voz.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com `en-GB` a localidade `en-GB-George-Apollo` e a voz.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com `en-GB` a localidade `en-GB-HazelRUS` e a voz.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com `en-GB` a localidade `en-GB-Susan-Apollo` e a voz.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Imagem de contêiner com `en-IE` a localidade `en-IE-Sean` e a voz.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com `en-IN` a localidade `en-IN-Heera-Apollo` e a voz.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com `en-IN` a localidade `en-IN-PriyaRUS` e a voz.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com `en-IN` a localidade `en-IN-Ravi-Apollo` e a voz.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-BenjaminRUS` e a voz.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com `en-US` a localidade `en-US-Guy24kRUS` e a voz.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-Jessa24kRUS` e a voz.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com `en-US` a localidade `en-US-JessaRUS` e a voz.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com `en-US` a localidade `en-US-ZiraRUS` e a voz.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com `es-ES` a localidade `es-ES-HelenaRUS` e a voz.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com `es-ES` a localidade `es-ES-Laura-Apollo` e a voz.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com `es-ES` a localidade `es-ES-Pablo-Apollo` e a voz.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com `es-MX` a localidade `es-MX-HildaRUS` e a voz.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com `es-MX` a localidade `es-MX-Raul-Apollo` e a voz.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Imagem de contêiner com `fi-FI` a localidade `fi-FI-HeidiRUS` e a voz.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com `fr-CA` a localidade `fr-CA-Caroline` e a voz.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com `fr-CA` a localidade `fr-CA-HarmonieRUS` e a voz.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Imagem de contêiner com `fr-CH` a localidade `fr-CH-Guillaume` e a voz.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com `fr-FR` a localidade `fr-FR-HortenseRUS` e a voz.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com `fr-FR` a localidade `fr-FR-Julie-Apollo` e a voz.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com `fr-FR` a localidade `fr-FR-Paul-Apollo` e a voz.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Imagem de contêiner com `he-IL` a localidade `he-IL-Asaf` e a voz.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Imagem de contêiner com `hi-IN` a localidade `hi-IN-Hemant` e a voz.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contêiner com `hi-IN` a localidade `hi-IN-Kalpana-Apollo` e a voz.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contêiner com `hi-IN` a localidade `hi-IN-Kalpana` e a voz.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de contêiner com `hi-IN` a localidade `hi-IN-Kalpana` e a voz.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Imagem de contêiner com `hr-HR` a localidade `hr-HR-Matej` e a voz.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Imagem de contêiner com `hu-HU` a localidade `hu-HU-Szabolcs` e a voz.        |
| `1.3.0-amd64-id-id-andika-preview`          | Imagem de contêiner com `id-ID` a localidade `id-ID-Andika` e a voz.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com `it-IT` a localidade `it-IT-Cosimo-Apollo` e a voz.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com `it-IT` a localidade `it-IT-LuciaRUS` e a voz.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com `ja-JP` a localidade `ja-JP-Ayumi-Apollo` e a voz.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com `ja-JP` a localidade `ja-JP-HarukaRUS` e a voz.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com `ja-JP` a localidade `ja-JP-Ichiro-Apollo` e a voz.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com `ko-KR` a localidade `ko-KR-HeamiRUS` e a voz.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Imagem de contêiner com `ms-MY` a localidade `ms-MY-Rizwan` e a voz.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Imagem de contêiner com `nb-NO` a localidade `nb-NO-HuldaRUS` e a voz.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Imagem de contêiner com `nl-NL` a localidade `nl-NL-HannaRUS` e a voz.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Imagem de contêiner com `pl-PL` a localidade `pl-PL-PaulinaRUS` e a voz.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com `pt-BR` a localidade `pt-BR-Daniel-Apollo` e a voz.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com `pt-BR` a localidade `pt-BR-HeloisaRUS` e a voz.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Imagem de contêiner com `pt-PT` a localidade `pt-PT-HeliaRUS` e a voz.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Imagem de contêiner com `ro-RO` a localidade `ro-RO-Andrei` e a voz.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de contêiner com `ru-RU` a localidade `ru-RU-EkaterinaRUS` e a voz.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de contêiner com `ru-RU` a localidade `ru-RU-Irina-Apollo` e a voz.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de contêiner com `ru-RU` a localidade `ru-RU-Pavel-Apollo` e a voz.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Imagem de contêiner com `sk-SK` a localidade `sk-SK-Filip` e a voz.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Imagem de contêiner com `sl-SI` a localidade `sl-SI-Lado` e a voz.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Imagem de contêiner com `sv-SE` a localidade `sv-SE-HedvigRUS` e a voz.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Imagem de contêiner com `ta-IN` a localidade `ta-IN-Valluvar` e a voz.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Imagem de contêiner com `te-IN` a localidade `te-IN-Chitra` e a voz.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Imagem de contêiner com `th-TH` a localidade `th-TH-Pattara` e a voz.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Imagem de contêiner com `tr-TR` a localidade `tr-TR-SedaRUS` e a voz.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Imagem de contêiner com `vi-VN` a localidade `vi-VN-An` e a voz.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com `zh-CN` a localidade `zh-CN-HuihuiRUS` e a voz.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Kangkang-Apollo` e a voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Yaoyao-Apollo` e a voz.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de contêiner com `zh-HK` a localidade `zh-HK-Danny-Apollo` e a voz.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de contêiner com `zh-HK` a localidade `zh-HK-Tracy-Apollo` e a voz.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Imagem de contêiner com `zh-HK` a localidade `zh-HK-TracyRUS` e a voz.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de contêiner com `zh-TW` a localidade `zh-TW-HanHanRUS` e a voz.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de contêiner com `zh-TW` a localidade `zh-TW-Yating-Apollo` e a voz.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de contêiner com `zh-TW` a localidade `zh-TW-Zhiwei-Apollo` e a voz.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com `de-DE` a localidade `de-DE-Hedda` e a voz.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com `de-DE` a localidade `de-DE-HeddaRUS` e a voz.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com `de-DE` a localidade `de-DE-Stefan-Apollo` e a voz.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com `en-AU` a localidade `en-AU-Catherine` e a voz.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com `en-AU` a localidade `en-AU-HayleyRUS` e a voz.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com `en-GB` a localidade `en-GB-George-Apollo` e a voz.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com `en-GB` a localidade `en-GB-HazelRUS` e a voz.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com `en-GB` a localidade `en-GB-Susan-Apollo` e a voz.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com `en-IN` a localidade `en-IN-Heera-Apollo` e a voz.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com `en-IN` a localidade `en-IN-PriyaRUS` e a voz.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com `en-IN` a localidade `en-IN-Ravi-Apollo` e a voz.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-BenjaminRUS` e a voz.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com `en-US` a localidade `en-US-Guy24kRUS` e a voz.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-Jessa24kRUS` e a voz.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com `en-US` a localidade `en-US-JessaRUS` e a voz.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com `en-US` a localidade `en-US-ZiraRUS` e a voz.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com `es-ES` a localidade `es-ES-HelenaRUS` e a voz.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com `es-ES` a localidade `es-ES-Laura-Apollo` e a voz.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com `es-ES` a localidade `es-ES-Pablo-Apollo` e a voz.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com `es-MX` a localidade `es-MX-HildaRUS` e a voz.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com `es-MX` a localidade `es-MX-Raul-Apollo` e a voz.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com `fr-CA` a localidade `fr-CA-Caroline` e a voz.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com `fr-CA` a localidade `fr-CA-HarmonieRUS` e a voz.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com `fr-FR` a localidade `fr-FR-HortenseRUS` e a voz.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com `fr-FR` a localidade `fr-FR-Julie-Apollo` e a voz.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com `fr-FR` a localidade `fr-FR-Paul-Apollo` e a voz.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com `it-IT` a localidade `it-IT-Cosimo-Apollo` e a voz.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com `it-IT` a localidade `it-IT-LuciaRUS` e a voz.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com `ja-JP` a localidade `ja-JP-Ayumi-Apollo` e a voz.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com `ja-JP` a localidade `ja-JP-HarukaRUS` e a voz.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com `ja-JP` a localidade `ja-JP-Ichiro-Apollo` e a voz.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com `ko-KR` a localidade `ko-KR-HeamiRUS` e a voz.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com `pt-BR` a localidade `pt-BR-Daniel-Apollo` e a voz.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com `pt-BR` a localidade `pt-BR-HeloisaRUS` e a voz.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com `zh-CN` a localidade `zh-CN-HuihuiRUS` e a voz.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Kangkang-Apollo` e a voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Yaoyao-Apollo` e a voz.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com `de-DE` a localidade `de-DE-Hedda` e a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com `de-DE` a localidade `de-DE-Hedda` e a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com `de-DE` a localidade `de-DE-HeddaRUS` e a voz.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com `de-DE` a localidade `de-DE-Stefan-Apollo` e a voz.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com `en-AU` a localidade `en-AU-Catherine` e a voz.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com `en-AU` a localidade `en-AU-HayleyRUS` e a voz.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com `en-GB` a localidade `en-GB-George-Apollo` e a voz.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com `en-GB` a localidade `en-GB-HazelRUS` e a voz.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com `en-GB` a localidade `en-GB-Susan-Apollo` e a voz.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com `en-IN` a localidade `en-IN-Heera-Apollo` e a voz.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com `en-IN` a localidade `en-IN-PriyaRUS` e a voz.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com `en-IN` a localidade `en-IN-Ravi-Apollo` e a voz.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-BenjaminRUS` e a voz.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com `en-US` a localidade `en-US-Guy24kRUS` e a voz.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-Jessa24kRUS` e a voz.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com `en-US` a localidade `en-US-JessaRUS` e a voz.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com `en-US` a localidade `en-US-ZiraRUS` e a voz.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com `es-ES` a localidade `es-ES-HelenaRUS` e a voz.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com `es-ES` a localidade `es-ES-Laura-Apollo` e a voz.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com `es-ES` a localidade `es-ES-Pablo-Apollo` e a voz.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com `es-MX` a localidade `es-MX-HildaRUS` e a voz.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com `es-MX` a localidade `es-MX-Raul-Apollo` e a voz.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com `fr-CA` a localidade `fr-CA-Caroline` e a voz.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com `fr-CA` a localidade `fr-CA-HarmonieRUS` e a voz.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com `fr-FR` a localidade `fr-FR-HortenseRUS` e a voz.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com `fr-FR` a localidade `fr-FR-Julie-Apollo` e a voz.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com `fr-FR` a localidade `fr-FR-Paul-Apollo` e a voz.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com `it-IT` a localidade `it-IT-Cosimo-Apollo` e a voz.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com `it-IT` a localidade `it-IT-LuciaRUS` e a voz.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com `ja-JP` a localidade `ja-JP-Ayumi-Apollo` e a voz.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com `ja-JP` a localidade `ja-JP-HarukaRUS` e a voz.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com `ja-JP` a localidade `ja-JP-Ichiro-Apollo` e a voz.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com `ko-KR` a localidade `ko-KR-HeamiRUS` e a voz.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com `pt-BR` a localidade `pt-BR-Daniel-Apollo` e a voz.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com `pt-BR` a localidade `pt-BR-HeloisaRUS` e a voz.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com `zh-CN` a localidade `zh-CN-HuihuiRUS` e a voz.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Kangkang-Apollo` e a voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Yaoyao-Apollo` e a voz.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-BenjaminRUS` e a voz.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com `en-US` a localidade `en-US-Guy24kRUS` e a voz.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com `en-US` a localidade `en-US-Jessa24kRUS` e a voz.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com `en-US` a localidade `en-US-JessaRUS` e a voz.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com `en-US` a localidade `en-US-ZiraRUS` e a voz.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com `zh-CN` a localidade `zh-CN-HuihuiRUS` e a voz.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Kangkang-Apollo` e a voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com `zh-CN` a localidade `zh-CN-Yaoyao-Apollo` e a voz.   |

## <a name="key-phrase-extraction"></a>Extração de Frases-Chave

A imagem de contêiner [extração de frases-chave][ta-kp] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `keyphrase`. O nome da imagem de contêiner totalmente qualificado `mcr.microsoft.com/azure-cognitive-services/keyphrase`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Detecção de Idioma

A imagem de contêiner [detecção de idioma][ta-la] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `language`. O nome da imagem de contêiner totalmente qualificado `mcr.microsoft.com/azure-cognitive-services/language`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Análise de Sentimento

A imagem de contêiner [análise de sentimento][ta-se] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `sentiment`. O nome da imagem de contêiner totalmente qualificado `mcr.microsoft.com/azure-cognitive-services/sentiment`é,.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
