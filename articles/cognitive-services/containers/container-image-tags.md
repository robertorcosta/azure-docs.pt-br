---
title: Marcas de imagem de contêiner dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma lista abrangente de todas as tags de imagem de contêiner do Serviço Cognitivo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219440"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Tags de imagem de contêiner azure Cognitive Services

O Azure Cognitive Services oferece muitas imagens de contêineres. Os registros de contêineres e os repositórios correspondentes variam entre as imagens do contêiner. Cada nome de imagem do contêiner oferece várias tags. Uma tag de imagem de contêiner é um mecanismo de versão da imagem do contêiner. Este artigo destina-se a ser usado como uma referência abrangente para listar todas as imagens de contêineres dos Serviços Cognitivos e suas etiquetas disponíveis.

> [!TIP]
> Ao [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)utilizar, preste muita atenção ao invólucro do registro do contêiner, repositório, nome da imagem do contêiner e tag correspondente - pois são **sensíveis ao caso**.

## <a name="anomaly-detector"></a>Detector de Anomalias

A imagem [do recipiente do Detector de Anomalias][ad-containers] pode ser encontrada no registro do `containerpreview.azurecr.io` contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-anomaly-detector`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Visual Computacional

A imagem do contêiner [Computer][cv-containers] `containerpreview.azurecr.io` Vision pode ser encontrada no registro do contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-read`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-read`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

A imagem [do][fa-containers] contêiner Face `containerpreview.azurecr.io` pode ser encontrada no registro do contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-face`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-face`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
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

A imagem do contêiner [Form Recognizer][fr-containers] pode ser encontrada no registro do `containerpreview.azurecr.io` contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-form-recognizer`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Reconhecimento Vocal (LUIS)

A [LUIS][lu-containers] imagem do contêiner LUIS `mcr.microsoft.com` pode ser encontrada no sindicato de registro de contêineres. Ele reside dentro `azure-cognitive-services` do repositório `luis`e é nomeado . O nome da imagem `mcr.microsoft.com/azure-cognitive-services/luis`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
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

## <a name="custom-speech-to-text"></a>Discurso personalizado para texto

A imagem personalizada do contêiner de fala `containerpreview.azurecr.io` para [texto][sp-cstt] pode ser encontrada no registro do contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-custom-speech-to-text`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem            | Observações |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Texto-para-fala personalizado

A imagem personalizada do contêiner [texto-para-fala][sp-ctts] pode ser encontrada no registro do `containerpreview.azurecr.io` contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-custom-text-to-speech`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem            | Observações |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Conversão de fala em texto

A imagem do contêiner [Fala-a-texto][sp-stt] pode ser encontrada no registro do `containerpreview.azurecr.io` contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-speech-to-text`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                  | Observações                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Imagem do `en-US` contêiner com o local. |
| `2.1.1-amd64-ar-ae-preview` | Imagem do `ar-AE` contêiner com o local. |
| `2.1.1-amd64-ar-eg-preview` | Imagem do `ar-EG` contêiner com o local. |
| `2.1.1-amd64-ar-kw-preview` | Imagem do `ar-KW` contêiner com o local. |
| `2.1.1-amd64-ar-qa-preview` | Imagem do `ar-QA` contêiner com o local. |
| `2.1.1-amd64-ar-sa-preview` | Imagem do `ar-SA` contêiner com o local. |
| `2.1.1-amd64-ca-es-preview` | Imagem do `ca-ES` contêiner com o local. |
| `2.1.1-amd64-da-dk-preview` | Imagem do `da-DK` contêiner com o local. |
| `2.1.1-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `2.1.1-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `2.1.1-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `2.1.1-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `2.1.1-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `2.1.1-amd64-en-nz-preview` | Imagem do `en-NZ` contêiner com o local. |
| `2.1.1-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `2.1.1-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `2.1.1-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `2.1.1-amd64-fi-fi-preview` | Imagem do `fi-FI` contêiner com o local. |
| `2.1.1-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `2.1.1-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `2.1.1-amd64-gu-in-preview` | Imagem do `gu-IN` contêiner com o local. |
| `2.1.1-amd64-hi-in-preview` | Imagem do `hi-IN` contêiner com o local. |
| `2.1.1-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `2.1.1-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `2.1.1-amd64-ko-kr-preview` | Imagem do `ko-KR` contêiner com o local. |
| `2.1.1-amd64-mr-in-preview` | Imagem do `mr-IN` contêiner com o local. |
| `2.1.1-amd64-nb-no-preview` | Imagem do `nb-NO` contêiner com o local. |
| `2.1.1-amd64-nl-nl-preview` | Imagem do `nl-NL` contêiner com o local. |
| `2.1.1-amd64-pl-pl-preview` | Imagem do `pl-PL` contêiner com o local. |
| `2.1.1-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `2.1.1-amd64-pt-pt-preview` | Imagem do `pt-PT` contêiner com o local. |
| `2.1.1-amd64-ru-ru-preview` | Imagem do `ru-RU` contêiner com o local. |
| `2.1.1-amd64-sv-se-preview` | Imagem do `sv-SE` contêiner com o local. |
| `2.1.1-amd64-ta-in-preview` | Imagem do `ta-IN` contêiner com o local. |
| `2.1.1-amd64-te-in-preview` | Imagem do `te-IN` contêiner com o local. |
| `2.1.1-amd64-th-th-preview` | Imagem do `th-TH` contêiner com o local. |
| `2.1.1-amd64-tr-tr-preview` | Imagem do `tr-TR` contêiner com o local. |
| `2.1.1-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `2.1.1-amd64-zh-hk-preview` | Imagem do `zh-HK` contêiner com o local. |
| `2.1.1-amd64-zh-tw-preview` | Imagem do `zh-TW` contêiner com o local. |
| `2.1.0-amd64-ar-ae-preview` | Imagem do `ar-AE` contêiner com o local. |
| `2.1.0-amd64-ar-eg-preview` | Imagem do `ar-EG` contêiner com o local. |
| `2.1.0-amd64-ar-kw-preview` | Imagem do `ar-KW` contêiner com o local. |
| `2.1.0-amd64-ar-qa-preview` | Imagem do `ar-QA` contêiner com o local. |
| `2.1.0-amd64-ar-sa-preview` | Imagem do `ar-SA` contêiner com o local. |
| `2.1.0-amd64-ca-es-preview` | Imagem do `ca-ES` contêiner com o local. |
| `2.1.0-amd64-da-dk-preview` | Imagem do `da-DK` contêiner com o local. |
| `2.1.0-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `2.1.0-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `2.1.0-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `2.1.0-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `2.1.0-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `2.1.0-amd64-en-nz-preview` | Imagem do `en-NZ` contêiner com o local. |
| `2.1.0-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `2.1.0-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `2.1.0-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `2.1.0-amd64-fi-fi-preview` | Imagem do `fi-FI` contêiner com o local. |
| `2.1.0-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `2.1.0-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `2.1.0-amd64-gu-in-preview` | Imagem do `gu-IN` contêiner com o local. |
| `2.1.0-amd64-hi-in-preview` | Imagem do `hi-IN` contêiner com o local. |
| `2.1.0-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `2.1.0-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `2.1.0-amd64-ko-kr-preview` | Imagem do `ko-KR` contêiner com o local. |
| `2.1.0-amd64-mr-in-preview` | Imagem do `mr-IN` contêiner com o local. |
| `2.1.0-amd64-nb-no-preview` | Imagem do `nb-NO` contêiner com o local. |
| `2.1.0-amd64-nl-nl-preview` | Imagem do `nl-NL` contêiner com o local. |
| `2.1.0-amd64-pl-pl-preview` | Imagem do `pl-PL` contêiner com o local. |
| `2.1.0-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `2.1.0-amd64-pt-pt-preview` | Imagem do `pt-PT` contêiner com o local. |
| `2.1.0-amd64-ru-ru-preview` | Imagem do `ru-RU` contêiner com o local. |
| `2.1.0-amd64-sv-se-preview` | Imagem do `sv-SE` contêiner com o local. |
| `2.1.0-amd64-ta-in-preview` | Imagem do `ta-IN` contêiner com o local. |
| `2.1.0-amd64-te-in-preview` | Imagem do `te-IN` contêiner com o local. |
| `2.1.0-amd64-th-th-preview` | Imagem do `th-TH` contêiner com o local. |
| `2.1.0-amd64-tr-tr-preview` | Imagem do `tr-TR` contêiner com o local. |
| `2.1.0-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `2.1.0-amd64-zh-hk-preview` | Imagem do `zh-HK` contêiner com o local. |
| `2.1.0-amd64-zh-tw-preview` | Imagem do `zh-TW` contêiner com o local. |
| `2.0.3-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `2.0.2-amd64-ar-ae-preview` | Imagem do `ar-AE` contêiner com o local. |
| `2.0.2-amd64-ar-eg-preview` | Imagem do `ar-EG` contêiner com o local. |
| `2.0.2-amd64-ar-kw-preview` | Imagem do `ar-KW` contêiner com o local. |
| `2.0.2-amd64-ar-qa-preview` | Imagem do `ar-QA` contêiner com o local. |
| `2.0.2-amd64-ar-sa-preview` | Imagem do `ar-SA` contêiner com o local. |
| `2.0.2-amd64-ca-es-preview` | Imagem do `ca-ES` contêiner com o local. |
| `2.0.2-amd64-da-dk-preview` | Imagem do `da-DK` contêiner com o local. |
| `2.0.2-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `2.0.2-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `2.0.2-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `2.0.2-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `2.0.2-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `2.0.2-amd64-en-nz-preview` | Imagem do `en-NZ` contêiner com o local. |
| `2.0.2-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `2.0.2-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `2.0.2-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `2.0.2-amd64-fi-fi-preview` | Imagem do `fi-FI` contêiner com o local. |
| `2.0.2-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `2.0.2-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `2.0.2-amd64-gu-in-preview` | Imagem do `gu-IN` contêiner com o local. |
| `2.0.2-amd64-hi-in-preview` | Imagem do `hi-IN` contêiner com o local. |
| `2.0.2-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `2.0.2-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `2.0.2-amd64-ko-kr-preview` | Imagem do `ko-KR` contêiner com o local. |
| `2.0.2-amd64-mr-in-preview` | Imagem do `mr-IN` contêiner com o local. |
| `2.0.2-amd64-nb-no-preview` | Imagem do `nb-NO` contêiner com o local. |
| `2.0.2-amd64-nl-nl-preview` | Imagem do `nl-NL` contêiner com o local. |
| `2.0.2-amd64-pl-pl-preview` | Imagem do `pl-PL` contêiner com o local. |
| `2.0.2-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `2.0.2-amd64-pt-pt-preview` | Imagem do `pt-PT` contêiner com o local. |
| `2.0.2-amd64-ru-ru-preview` | Imagem do `ru-RU` contêiner com o local. |
| `2.0.2-amd64-sv-se-preview` | Imagem do `sv-SE` contêiner com o local. |
| `2.0.2-amd64-ta-in-preview` | Imagem do `ta-IN` contêiner com o local. |
| `2.0.2-amd64-te-in-preview` | Imagem do `te-IN` contêiner com o local. |
| `2.0.2-amd64-th-th-preview` | Imagem do `th-TH` contêiner com o local. |
| `2.0.2-amd64-tr-tr-preview` | Imagem do `tr-TR` contêiner com o local. |
| `2.0.2-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `2.0.2-amd64-zh-hk-preview` | Imagem do `zh-HK` contêiner com o local. |
| `2.0.2-amd64-zh-tw-preview` | Imagem do `zh-TW` contêiner com o local. |
| `2.0.1-amd64-ar-ae-preview` | Imagem do `ar-AE` contêiner com o local. |
| `2.0.1-amd64-ar-eg-preview` | Imagem do `ar-EG` contêiner com o local. |
| `2.0.1-amd64-ar-kw-preview` | Imagem do `ar-KW` contêiner com o local. |
| `2.0.1-amd64-ar-qa-preview` | Imagem do `ar-QA` contêiner com o local. |
| `2.0.1-amd64-ar-sa-preview` | Imagem do `ar-SA` contêiner com o local. |
| `2.0.1-amd64-ca-es-preview` | Imagem do `ca-ES` contêiner com o local. |
| `2.0.1-amd64-da-dk-preview` | Imagem do `da-DK` contêiner com o local. |
| `2.0.1-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `2.0.1-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `2.0.1-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `2.0.1-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `2.0.1-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `2.0.1-amd64-en-nz-preview` | Imagem do `en-NZ` contêiner com o local. |
| `2.0.1-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `2.0.1-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `2.0.1-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `2.0.1-amd64-fi-fi-preview` | Imagem do `fi-FI` contêiner com o local. |
| `2.0.1-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `2.0.1-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `2.0.1-amd64-gu-in-preview` | Imagem do `gu-IN` contêiner com o local. |
| `2.0.1-amd64-hi-in-preview` | Imagem do `hi-IN` contêiner com o local. |
| `2.0.1-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `2.0.1-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `2.0.1-amd64-ko-kr-preview` | Imagem do `ko-KR` contêiner com o local. |
| `2.0.1-amd64-mr-in-preview` | Imagem do `mr-IN` contêiner com o local. |
| `2.0.1-amd64-nb-no-preview` | Imagem do `nb-NO` contêiner com o local. |
| `2.0.1-amd64-nl-nl-preview` | Imagem do `nl-NL` contêiner com o local. |
| `2.0.1-amd64-pl-pl-preview` | Imagem do `pl-PL` contêiner com o local. |
| `2.0.1-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `2.0.1-amd64-pt-pt-preview` | Imagem do `pt-PT` contêiner com o local. |
| `2.0.1-amd64-ru-ru-preview` | Imagem do `ru-RU` contêiner com o local. |
| `2.0.1-amd64-sv-se-preview` | Imagem do `sv-SE` contêiner com o local. |
| `2.0.1-amd64-ta-in-preview` | Imagem do `ta-IN` contêiner com o local. |
| `2.0.1-amd64-te-in-preview` | Imagem do `te-IN` contêiner com o local. |
| `2.0.1-amd64-th-th-preview` | Imagem do `th-TH` contêiner com o local. |
| `2.0.1-amd64-tr-tr-preview` | Imagem do `tr-TR` contêiner com o local. |
| `2.0.1-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `2.0.1-amd64-zh-hk-preview` | Imagem do `zh-HK` contêiner com o local. |
| `2.0.1-amd64-zh-tw-preview` | Imagem do `zh-TW` contêiner com o local. |
| `2.0.0-amd64-ar-eg-preview` | Imagem do `ar-EG` contêiner com o local. |
| `2.0.0-amd64-ca-es-preview` | Imagem do `ca-ES` contêiner com o local. |
| `2.0.0-amd64-da-dk-preview` | Imagem do `da-DK` contêiner com o local. |
| `2.0.0-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `2.0.0-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `2.0.0-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `2.0.0-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `2.0.0-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `2.0.0-amd64-en-nz-preview` | Imagem do `en-NZ` contêiner com o local. |
| `2.0.0-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `2.0.0-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `2.0.0-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `2.0.0-amd64-fi-fi-preview` | Imagem do `fi-FI` contêiner com o local. |
| `2.0.0-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `2.0.0-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `2.0.0-amd64-hi-in-preview` | Imagem do `hi-IN` contêiner com o local. |
| `2.0.0-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `2.0.0-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `2.0.0-amd64-ko-kr-preview` | Imagem do `ko-KR` contêiner com o local. |
| `2.0.0-amd64-nb-no-preview` | Imagem do `nb-NO` contêiner com o local. |
| `2.0.0-amd64-nl-nl-preview` | Imagem do `nl-NL` contêiner com o local. |
| `2.0.0-amd64-pl-pl-preview` | Imagem do `pl-PL` contêiner com o local. |
| `2.0.0-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `2.0.0-amd64-pt-pt-preview` | Imagem do `pt-PT` contêiner com o local. |
| `2.0.0-amd64-ru-ru-preview` | Imagem do `ru-RU` contêiner com o local. |
| `2.0.0-amd64-sv-se-preview` | Imagem do `sv-SE` contêiner com o local. |
| `2.0.0-amd64-th-th-preview` | Imagem do `th-TH` contêiner com o local. |
| `2.0.0-amd64-tr-tr-preview` | Imagem do `tr-TR` contêiner com o local. |
| `2.0.0-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `2.0.0-amd64-zh-hk-preview` | Imagem do `zh-HK` contêiner com o local. |
| `2.0.0-amd64-zh-tw-preview` | Imagem do `zh-TW` contêiner com o local. |
| `1.2.0-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `1.2.0-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `1.2.0-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `1.2.0-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `1.2.0-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `1.2.0-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `1.2.0-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `1.2.0-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `1.2.0-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `1.2.0-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `1.2.0-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `1.2.0-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `1.2.0-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `1.2.0-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `1.1.3-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `1.1.3-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `1.1.3-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `1.1.3-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `1.1.3-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `1.1.3-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `1.1.3-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `1.1.3-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `1.1.3-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `1.1.3-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `1.1.3-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `1.1.3-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `1.1.3-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `1.1.3-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `1.1.2-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `1.1.2-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `1.1.2-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `1.1.2-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `1.1.2-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `1.1.2-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `1.1.2-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `1.1.2-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `1.1.2-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `1.1.2-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `1.1.2-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `1.1.2-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `1.1.2-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `1.1.2-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `1.1.1-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `1.1.1-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `1.1.1-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `1.1.1-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `1.1.1-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `1.1.1-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `1.1.1-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `1.1.1-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `1.1.1-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `1.1.1-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `1.1.1-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `1.1.1-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `1.1.1-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `1.1.1-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `1.1.0-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `1.1.0-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `1.1.0-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `1.1.0-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `1.1.0-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `1.1.0-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `1.1.0-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `1.1.0-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `1.1.0-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `1.1.0-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `1.1.0-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `1.1.0-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `1.1.0-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `1.1.0-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |
| `1.0.0-amd64-de-de-preview` | Imagem do `de-DE` contêiner com o local. |
| `1.0.0-amd64-en-au-preview` | Imagem do `en-AU` contêiner com o local. |
| `1.0.0-amd64-en-ca-preview` | Imagem do `en-CA` contêiner com o local. |
| `1.0.0-amd64-en-gb-preview` | Imagem do `en-GB` contêiner com o local. |
| `1.0.0-amd64-en-in-preview` | Imagem do `en-IN` contêiner com o local. |
| `1.0.0-amd64-en-us-preview` | Imagem do `en-US` contêiner com o local. |
| `1.0.0-amd64-es-es-preview` | Imagem do `es-ES` contêiner com o local. |
| `1.0.0-amd64-es-mx-preview` | Imagem do `es-MX` contêiner com o local. |
| `1.0.0-amd64-fr-ca-preview` | Imagem do `fr-CA` contêiner com o local. |
| `1.0.0-amd64-fr-fr-preview` | Imagem do `fr-FR` contêiner com o local. |
| `1.0.0-amd64-it-it-preview` | Imagem do `it-IT` contêiner com o local. |
| `1.0.0-amd64-ja-jp-preview` | Imagem do `ja-JP` contêiner com o local. |
| `1.0.0-amd64-pt-br-preview` | Imagem do `pt-BR` contêiner com o local. |
| `1.0.0-amd64-zh-cn-preview` | Imagem do `zh-CN` contêiner com o local. |

## <a name="text-to-speech"></a>Conversão de texto em fala

A imagem do contêiner [texto-para-fala][sp-tts] pode ser encontrada no registro do `containerpreview.azurecr.io` contêiner. Ele reside dentro `microsoft` do repositório `cognitive-services-text-to-speech`e é nomeado . O nome da imagem `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem do `en-US` contêiner com `en-US-JessaRUS` a localização e a voz.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Imagem do `ar-EG` contêiner com `ar-EG-Hoda` a localização e a voz.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Imagem do `ar-SA` contêiner com `ar-SA-Naayf` a localização e a voz.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Imagem do `bg-BG` contêiner com `bg-BG-Ivan` a localização e a voz.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Imagem do `ca-ES` contêiner com `ca-ES-HerenaRUS` a localização e a voz.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Imagem do `cs-CZ` contêiner com `cs-CZ-Jakub` a localização e a voz.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Imagem do `da-DK` contêiner com `da-DK-HelleRUS` a localização e a voz.        |
| `1.3.0-amd64-de-at-michael-preview`         | Imagem do `de-AT` contêiner com `de-AT-Michael` a localização e a voz.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Imagem do `de-CH` contêiner com `de-CH-Karsten` a localização e a voz.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Imagem do `de-DE` contêiner com `de-DE-Hedda` a localização e a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem do `de-DE` contêiner com `de-DE-Hedda` a localização e a voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem do `de-DE` contêiner com `de-DE-HeddaRUS` a localização e a voz.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Imagem do `de-DE` contêiner com `de-DE-Stefan-Apollo` a localização e a voz.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Imagem do `el-GR` contêiner com `el-GR-Stefanos` a localização e a voz.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Imagem do `en-AU` contêiner com `en-AU-Catherine` a localização e a voz.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Imagem do `en-AU` contêiner com `en-AU-HayleyRUS` a localização e a voz.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Imagem do `en-CA` contêiner com `en-CA-HeatherRUS` a localização e a voz.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Imagem do `en-CA` contêiner com `en-CA-Linda` a localização e a voz.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Imagem do `en-GB` contêiner com `en-GB-George-Apollo` a localização e a voz.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Imagem do `en-GB` contêiner com `en-GB-HazelRUS` a localização e a voz.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Imagem do `en-GB` contêiner com `en-GB-Susan-Apollo` a localização e a voz.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Imagem do `en-IE` contêiner com `en-IE-Sean` a localização e a voz.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Imagem do `en-IN` contêiner com `en-IN-Heera-Apollo` a localização e a voz.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Imagem do `en-IN` contêiner com `en-IN-PriyaRUS` a localização e a voz.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Imagem do `en-IN` contêiner com `en-IN-Ravi-Apollo` a localização e a voz.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Imagem do `en-US` contêiner com `en-US-BenjaminRUS` a localização e a voz.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Imagem do `en-US` contêiner com `en-US-Guy24kRUS` a localização e a voz.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Imagem do `en-US` contêiner com `en-US-Jessa24kRUS` a localização e a voz.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Imagem do `en-US` contêiner com `en-US-JessaRUS` a localização e a voz.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Imagem do `en-US` contêiner com `en-US-ZiraRUS` a localização e a voz.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Imagem do `es-ES` contêiner com `es-ES-HelenaRUS` a localização e a voz.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Imagem do `es-ES` contêiner com `es-ES-Laura-Apollo` a localização e a voz.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Imagem do `es-ES` contêiner com `es-ES-Pablo-Apollo` a localização e a voz.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Imagem do `es-MX` contêiner com `es-MX-HildaRUS` a localização e a voz.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Imagem do `es-MX` contêiner com `es-MX-Raul-Apollo` a localização e a voz.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Imagem do `fi-FI` contêiner com `fi-FI-HeidiRUS` a localização e a voz.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Imagem do `fr-CA` contêiner com `fr-CA-Caroline` a localização e a voz.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Imagem do `fr-CA` contêiner com `fr-CA-HarmonieRUS` a localização e a voz.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Imagem do `fr-CH` contêiner com `fr-CH-Guillaume` a localização e a voz.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Imagem do `fr-FR` contêiner com `fr-FR-HortenseRUS` a localização e a voz.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Imagem do `fr-FR` contêiner com `fr-FR-Julie-Apollo` a localização e a voz.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Imagem do `fr-FR` contêiner com `fr-FR-Paul-Apollo` a localização e a voz.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Imagem do `he-IL` contêiner com `he-IL-Asaf` a localização e a voz.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Imagem do `hi-IN` contêiner com `hi-IN-Hemant` a localização e a voz.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem do `hi-IN` contêiner com `hi-IN-Kalpana-Apollo` a localização e a voz.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem do `hi-IN` contêiner com `hi-IN-Kalpana` a localização e a voz.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem do `hi-IN` contêiner com `hi-IN-Kalpana` a localização e a voz.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Imagem do `hr-HR` contêiner com `hr-HR-Matej` a localização e a voz.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Imagem do `hu-HU` contêiner com `hu-HU-Szabolcs` a localização e a voz.        |
| `1.3.0-amd64-id-id-andika-preview`          | Imagem do `id-ID` contêiner com `id-ID-Andika` a localização e a voz.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Imagem do `it-IT` contêiner com `it-IT-Cosimo-Apollo` a localização e a voz.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Imagem do `it-IT` contêiner com `it-IT-LuciaRUS` a localização e a voz.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem do `ja-JP` contêiner com `ja-JP-Ayumi-Apollo` a localização e a voz.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Imagem do `ja-JP` contêiner com `ja-JP-HarukaRUS` a localização e a voz.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem do `ja-JP` contêiner com `ja-JP-Ichiro-Apollo` a localização e a voz.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Imagem do `ko-KR` contêiner com `ko-KR-HeamiRUS` a localização e a voz.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Imagem do `ms-MY` contêiner com `ms-MY-Rizwan` a localização e a voz.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Imagem do `nb-NO` contêiner com `nb-NO-HuldaRUS` a localização e a voz.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Imagem do `nl-NL` contêiner com `nl-NL-HannaRUS` a localização e a voz.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Imagem do `pl-PL` contêiner com `pl-PL-PaulinaRUS` a localização e a voz.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Imagem do `pt-BR` contêiner com `pt-BR-Daniel-Apollo` a localização e a voz.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Imagem do `pt-BR` contêiner com `pt-BR-HeloisaRUS` a localização e a voz.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Imagem do `pt-PT` contêiner com `pt-PT-HeliaRUS` a localização e a voz.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Imagem do `ro-RO` contêiner com `ro-RO-Andrei` a localização e a voz.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem do `ru-RU` contêiner com `ru-RU-EkaterinaRUS` a localização e a voz.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Imagem do `ru-RU` contêiner com `ru-RU-Irina-Apollo` a localização e a voz.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem do `ru-RU` contêiner com `ru-RU-Pavel-Apollo` a localização e a voz.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Imagem do `sk-SK` contêiner com `sk-SK-Filip` a localização e a voz.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Imagem do `sl-SI` contêiner com `sl-SI-Lado` a localização e a voz.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Imagem do `sv-SE` contêiner com `sv-SE-HedvigRUS` a localização e a voz.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Imagem do `ta-IN` contêiner com `ta-IN-Valluvar` a localização e a voz.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Imagem do `te-IN` contêiner com `te-IN-Chitra` a localização e a voz.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Imagem do `th-TH` contêiner com `th-TH-Pattara` a localização e a voz.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Imagem do `tr-TR` contêiner com `tr-TR-SedaRUS` a localização e a voz.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Imagem do `vi-VN` contêiner com `vi-VN-An` a localização e a voz.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Imagem do `zh-CN` contêiner com `zh-CN-HuihuiRUS` a localização e a voz.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem do `zh-CN` contêiner com `zh-CN-Kangkang-Apollo` a localização e a voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem do `zh-CN` contêiner com `zh-CN-Yaoyao-Apollo` a localização e a voz.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Imagem do `zh-HK` contêiner com `zh-HK-Danny-Apollo` a localização e a voz.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem do `zh-HK` contêiner com `zh-HK-Tracy-Apollo` a localização e a voz.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Imagem do `zh-HK` contêiner com `zh-HK-TracyRUS` a localização e a voz.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Imagem do `zh-TW` contêiner com `zh-TW-HanHanRUS` a localização e a voz.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Imagem do `zh-TW` contêiner com `zh-TW-Yating-Apollo` a localização e a voz.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem do `zh-TW` contêiner com `zh-TW-Zhiwei-Apollo` a localização e a voz.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem do `de-DE` contêiner com `de-DE-Hedda` a localização e a voz.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem do `de-DE` contêiner com `de-DE-HeddaRUS` a localização e a voz.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Imagem do `de-DE` contêiner com `de-DE-Stefan-Apollo` a localização e a voz.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Imagem do `en-AU` contêiner com `en-AU-Catherine` a localização e a voz.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Imagem do `en-AU` contêiner com `en-AU-HayleyRUS` a localização e a voz.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Imagem do `en-GB` contêiner com `en-GB-George-Apollo` a localização e a voz.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Imagem do `en-GB` contêiner com `en-GB-HazelRUS` a localização e a voz.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Imagem do `en-GB` contêiner com `en-GB-Susan-Apollo` a localização e a voz.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Imagem do `en-IN` contêiner com `en-IN-Heera-Apollo` a localização e a voz.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Imagem do `en-IN` contêiner com `en-IN-PriyaRUS` a localização e a voz.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Imagem do `en-IN` contêiner com `en-IN-Ravi-Apollo` a localização e a voz.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Imagem do `en-US` contêiner com `en-US-BenjaminRUS` a localização e a voz.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Imagem do `en-US` contêiner com `en-US-Guy24kRUS` a localização e a voz.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Imagem do `en-US` contêiner com `en-US-Jessa24kRUS` a localização e a voz.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Imagem do `en-US` contêiner com `en-US-JessaRUS` a localização e a voz.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Imagem do `en-US` contêiner com `en-US-ZiraRUS` a localização e a voz.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Imagem do `es-ES` contêiner com `es-ES-HelenaRUS` a localização e a voz.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Imagem do `es-ES` contêiner com `es-ES-Laura-Apollo` a localização e a voz.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Imagem do `es-ES` contêiner com `es-ES-Pablo-Apollo` a localização e a voz.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Imagem do `es-MX` contêiner com `es-MX-HildaRUS` a localização e a voz.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Imagem do `es-MX` contêiner com `es-MX-Raul-Apollo` a localização e a voz.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Imagem do `fr-CA` contêiner com `fr-CA-Caroline` a localização e a voz.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Imagem do `fr-CA` contêiner com `fr-CA-HarmonieRUS` a localização e a voz.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Imagem do `fr-FR` contêiner com `fr-FR-HortenseRUS` a localização e a voz.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Imagem do `fr-FR` contêiner com `fr-FR-Julie-Apollo` a localização e a voz.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Imagem do `fr-FR` contêiner com `fr-FR-Paul-Apollo` a localização e a voz.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Imagem do `it-IT` contêiner com `it-IT-Cosimo-Apollo` a localização e a voz.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Imagem do `it-IT` contêiner com `it-IT-LuciaRUS` a localização e a voz.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem do `ja-JP` contêiner com `ja-JP-Ayumi-Apollo` a localização e a voz.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Imagem do `ja-JP` contêiner com `ja-JP-HarukaRUS` a localização e a voz.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem do `ja-JP` contêiner com `ja-JP-Ichiro-Apollo` a localização e a voz.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Imagem do `ko-KR` contêiner com `ko-KR-HeamiRUS` a localização e a voz.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Imagem do `pt-BR` contêiner com `pt-BR-Daniel-Apollo` a localização e a voz.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Imagem do `pt-BR` contêiner com `pt-BR-HeloisaRUS` a localização e a voz.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Imagem do `zh-CN` contêiner com `zh-CN-HuihuiRUS` a localização e a voz.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem do `zh-CN` contêiner com `zh-CN-Kangkang-Apollo` a localização e a voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem do `zh-CN` contêiner com `zh-CN-Yaoyao-Apollo` a localização e a voz.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Imagem do `de-DE` contêiner com `de-DE-Hedda` a localização e a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem do `de-DE` contêiner com `de-DE-Hedda` a localização e a voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem do `de-DE` contêiner com `de-DE-HeddaRUS` a localização e a voz.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Imagem do `de-DE` contêiner com `de-DE-Stefan-Apollo` a localização e a voz.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Imagem do `en-AU` contêiner com `en-AU-Catherine` a localização e a voz.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Imagem do `en-AU` contêiner com `en-AU-HayleyRUS` a localização e a voz.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Imagem do `en-GB` contêiner com `en-GB-George-Apollo` a localização e a voz.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Imagem do `en-GB` contêiner com `en-GB-HazelRUS` a localização e a voz.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Imagem do `en-GB` contêiner com `en-GB-Susan-Apollo` a localização e a voz.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Imagem do `en-IN` contêiner com `en-IN-Heera-Apollo` a localização e a voz.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Imagem do `en-IN` contêiner com `en-IN-PriyaRUS` a localização e a voz.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Imagem do `en-IN` contêiner com `en-IN-Ravi-Apollo` a localização e a voz.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Imagem do `en-US` contêiner com `en-US-BenjaminRUS` a localização e a voz.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Imagem do `en-US` contêiner com `en-US-Guy24kRUS` a localização e a voz.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Imagem do `en-US` contêiner com `en-US-Jessa24kRUS` a localização e a voz.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Imagem do `en-US` contêiner com `en-US-JessaRUS` a localização e a voz.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Imagem do `en-US` contêiner com `en-US-ZiraRUS` a localização e a voz.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Imagem do `es-ES` contêiner com `es-ES-HelenaRUS` a localização e a voz.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Imagem do `es-ES` contêiner com `es-ES-Laura-Apollo` a localização e a voz.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Imagem do `es-ES` contêiner com `es-ES-Pablo-Apollo` a localização e a voz.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Imagem do `es-MX` contêiner com `es-MX-HildaRUS` a localização e a voz.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Imagem do `es-MX` contêiner com `es-MX-Raul-Apollo` a localização e a voz.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Imagem do `fr-CA` contêiner com `fr-CA-Caroline` a localização e a voz.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Imagem do `fr-CA` contêiner com `fr-CA-HarmonieRUS` a localização e a voz.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Imagem do `fr-FR` contêiner com `fr-FR-HortenseRUS` a localização e a voz.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Imagem do `fr-FR` contêiner com `fr-FR-Julie-Apollo` a localização e a voz.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Imagem do `fr-FR` contêiner com `fr-FR-Paul-Apollo` a localização e a voz.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Imagem do `it-IT` contêiner com `it-IT-Cosimo-Apollo` a localização e a voz.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Imagem do `it-IT` contêiner com `it-IT-LuciaRUS` a localização e a voz.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem do `ja-JP` contêiner com `ja-JP-Ayumi-Apollo` a localização e a voz.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Imagem do `ja-JP` contêiner com `ja-JP-HarukaRUS` a localização e a voz.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem do `ja-JP` contêiner com `ja-JP-Ichiro-Apollo` a localização e a voz.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Imagem do `ko-KR` contêiner com `ko-KR-HeamiRUS` a localização e a voz.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Imagem do `pt-BR` contêiner com `pt-BR-Daniel-Apollo` a localização e a voz.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Imagem do `pt-BR` contêiner com `pt-BR-HeloisaRUS` a localização e a voz.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Imagem do `zh-CN` contêiner com `zh-CN-HuihuiRUS` a localização e a voz.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem do `zh-CN` contêiner com `zh-CN-Kangkang-Apollo` a localização e a voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem do `zh-CN` contêiner com `zh-CN-Yaoyao-Apollo` a localização e a voz.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Imagem do `en-US` contêiner com `en-US-BenjaminRUS` a localização e a voz.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Imagem do `en-US` contêiner com `en-US-Guy24kRUS` a localização e a voz.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Imagem do `en-US` contêiner com `en-US-Jessa24kRUS` a localização e a voz.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Imagem do `en-US` contêiner com `en-US-JessaRUS` a localização e a voz.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Imagem do `en-US` contêiner com `en-US-ZiraRUS` a localização e a voz.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Imagem do `zh-CN` contêiner com `zh-CN-HuihuiRUS` a localização e a voz.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem do `zh-CN` contêiner com `zh-CN-Kangkang-Apollo` a localização e a voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem do `zh-CN` contêiner com `zh-CN-Yaoyao-Apollo` a localização e a voz.   |

## <a name="key-phrase-extraction"></a>Extração de Frases-Chave

A imagem do contêiner [De extração de frases-chave][ta-kp] pode ser encontrada no sindicato do registro de `mcr.microsoft.com` contêineres. Ele reside dentro `azure-cognitive-services` do repositório `keyphrase`e é nomeado . O nome da imagem `mcr.microsoft.com/azure-cognitive-services/keyphrase`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Detecção de Idioma

A imagem do contêiner [Detecção de Idiomas][ta-la] pode ser encontrada no sindicato do registro de `mcr.microsoft.com` contêineres. Ele reside dentro `azure-cognitive-services` do repositório `language`e é nomeado . O nome da imagem `mcr.microsoft.com/azure-cognitive-services/language`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Análise de Sentimento

A imagem do contêiner De `mcr.microsoft.com` análise de [sentimento][ta-se] pode ser encontrada no sindicato do registro de contêineres. Ele reside dentro `azure-cognitive-services` do repositório `sentiment`e é nomeado . O nome da imagem `mcr.microsoft.com/azure-cognitive-services/sentiment`do contêiner totalmente qualificado é, .

Esta imagem do contêiner tem as seguintes tags disponíveis:

| Tags de imagem                    | Observações |
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
