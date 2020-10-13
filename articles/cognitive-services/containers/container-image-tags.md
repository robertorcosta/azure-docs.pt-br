---
title: Marcas de imagem de contêiner dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma listagem abrangente de todas as marcas de imagem de contêiner de serviço cognitiva.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 2a24433389e738bf5d0ecb7ecac6bf369c8ba183
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369477"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Marcas de imagem de contêiner de serviços cognitivas do Azure

Os serviços cognitivas do Azure oferecem muitas imagens de contêiner. Os registros de contêiner e os repositórios correspondentes variam entre as imagens de contêiner. Cada nome de imagem de contêiner oferece várias marcas. Uma marca de imagem de contêiner é um mecanismo de controle de versão da imagem de contêiner. Este artigo destina-se a ser usado como uma referência abrangente para listar todas as imagens de contêiner de serviços cognitivas e suas marcas disponíveis.

> [!TIP]
> Ao usar [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o, preste muita atenção à capitalização do registro de contêiner, do repositório, do nome da imagem de contêiner e da marca correspondente, pois eles diferenciam **maiúsculas de minúsculas**.

## <a name="anomaly-detector"></a>Detector de Anomalias

A imagem de contêiner do [detector de anomalias][ad-containers] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `anomaly-detector` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Visual Computacional

A imagem de contêiner de OCR de [Pesquisa Visual computacional][cv-containers] leitura pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-read` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-read` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • Diminuir ainda mais o uso de memória para o contêiner. |
|                                          | • O cache externo é necessário para a configuração de vários pods. Por exemplo, configure Redis para Caching. |
|                                          | • Corrigir resultados que faltam de problemas quando o cache Redis é configurado e ResultExpirationPeriod = 0.  |
|                                          | • Remova a limitação do tamanho do corpo da solicitação de 26MB. O contêiner agora pode aceitar >arquivos 26MB.  |
|                                          | • Adicionar carimbo de data/hora e versão de compilação ao log do console.  |
| `1.1.013050001-amd64-preview`            | * Adicionou ReadEngineConfig: configuração de inicialização de contêiner ResultExpirationPeriod para especificar quando o sistema deve limpar os resultados de reconhecimento. |
|                                          | A configuração é em horas, e o valor padrão é 48hr.   |
|                                          |   A configuração pode reduzir o uso de memória para o armazenamento de resultados, especialmente quando o armazenamento na memória do contêiner é usado.  |
|                                          |    * Exemplo 1. ReadEngineConfig: ResultExpirationPeriod = 1, o sistema limpará o resultado de reconhecimento 1h após o processo.   |
|                                          |    * Exemplo 2. ReadEngineConfig: ResultExpirationPeriod = 0, o sistema limpará o resultado de reconhecimento após a recuperação do resultado.  |
|                                          | Foi corrigido um erro de servidor interno 500 quando um formato de imagem inválido é passado para o sistema. Agora, ele retornará um erro 400:   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

A imagem de contêiner de [face][fa-containers] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-face` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-face` .

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

A imagem de contêiner do [reconhecedor de formulário][fr-containers] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-form-recognizer` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Reconhecimento Vocal (LUIS)

A imagem de contêiner [Luis][lu-containers] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `luis` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/luis` .

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

A imagem de contêiner de [fala personalizada para texto][sp-cstt] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-custom-speech-to-text` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem            | Observações |
|-----------------------|:------|
| `latest`              |       |
| `2.5.0-amd64`         |       |
| `2.4.0-amd64-preview` |       |
| `2.3.1-amd64-preview` |       | 
| `2.3.0-amd64-preview` |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Conversão de texto em fala personalizada

A imagem de contêiner de [texto em fala personalizado][sp-ctts] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-custom-text-to-speech` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem            | Observações |
|-----------------------|:------|
| `latest`              |       |
| `1.7.0-amd64`         |       |
| `1.6.0-amd64-preview` |       |
| `1.6.0-amd64-preview` |       |
| `1.5.0-amd64-preview` |       |
| `1.4.0-amd64-preview` |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Conversão de fala em texto

A imagem de contêiner de [fala em texto][sp-stt] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-speech-to-text` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` .
As imagens 2.5.0 de fala em texto são compatíveis com a *Virgínia do governo dos EUA*. Use o ponto de extremidade de cobrança do *Virgínia do governo dos EUA* e as chaves de API para experimentar.

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                  | Observações                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Imagem de contêiner com a `en-US` localidade. |
| `2.5.0-amd64-ar-ae`         | Imagem de contêiner com a `ar-AE` localidade. |
| `2.5.0-amd64-ar-eg`         | Imagem de contêiner com a `ar-EG` localidade. |
| `2.5.0-amd64-ar-kw`         | Imagem de contêiner com a `ar-KW` localidade. |
| `2.5.0-amd64-ar-qa`         | Imagem de contêiner com a `ar-QA` localidade. |
| `2.5.0-amd64-ar-sa`         | Imagem de contêiner com a `ar-SA` localidade. |
| `2.5.0-amd64-ca-es`         | Imagem de contêiner com a `ca-ES` localidade. |
| `2.5.0-amd64-da-dk`         | Imagem de contêiner com a `da-DK` localidade. |
| `2.5.0-amd64-de-de`         | Imagem de contêiner com a `de-DE` localidade. |
| `2.5.0-amd64-en-au`         | Imagem de contêiner com a `en-AU` localidade. |
| `2.5.0-amd64-en-ca`         | Imagem de contêiner com a `en-CA` localidade. |
| `2.5.0-amd64-en-gb`         | Imagem de contêiner com a `en-GB` localidade. |
| `2.5.0-amd64-en-in`         | Imagem de contêiner com a `en-IN` localidade. |
| `2.5.0-amd64-en-nz`         | Imagem de contêiner com a `en-NZ` localidade. |
| `2.5.0-amd64-en-us`         | Imagem de contêiner com a `en-US` localidade. |
| `2.5.0-amd64-es-es`         | Imagem de contêiner com a `es-ES` localidade. |
| `2.5.0-amd64-es-mx`         | Imagem de contêiner com a `es-MX` localidade. |
| `2.5.0-amd64-fi-fi`         | Imagem de contêiner com a `fi-FI` localidade. |
| `2.5.0-amd64-fr-ca`         | Imagem de contêiner com a `fr-CA` localidade. |
| `2.5.0-amd64-fr-fr`         | Imagem de contêiner com a `fr-FR` localidade. |
| `2.5.0-amd64-gu-in`         | Imagem de contêiner com a `gu-IN` localidade. |
| `2.5.0-amd64-hi-in`         | Imagem de contêiner com a `hi-IN` localidade. |
| `2.5.0-amd64-it-it`         | Imagem de contêiner com a `it-IT` localidade. |
| `2.5.0-amd64-ja-jp`         | Imagem de contêiner com a `ja-JP` localidade. |
| `2.5.0-amd64-ko-kr`         | Imagem de contêiner com a `ko-KR` localidade. |
| `2.5.0-amd64-mr-in`         | Imagem de contêiner com a `mr-IN` localidade. |
| `2.5.0-amd64-nb-no`         | Imagem de contêiner com a `nb-NO` localidade. |
| `2.5.0-amd64-nl-nl`         | Imagem de contêiner com a `nl-NL` localidade. |
| `2.5.0-amd64-pl-pl`         | Imagem de contêiner com a `pl-PL` localidade. |
| `2.5.0-amd64-pt-br`         | Imagem de contêiner com a `pt-BR` localidade. |
| `2.5.0-amd64-pt-pt`         | Imagem de contêiner com a `pt-PT` localidade. |
| `2.5.0-amd64-ru-ru`         | Imagem de contêiner com a `ru-RU` localidade. |
| `2.5.0-amd64-sv-se`         | Imagem de contêiner com a `sv-SE` localidade. |
| `2.5.0-amd64-ta-in`         | Imagem de contêiner com a `ta-IN` localidade. |
| `2.5.0-amd64-te-in`         | Imagem de contêiner com a `te-IN` localidade. |
| `2.5.0-amd64-th-th`         | Imagem de contêiner com a `th-TH` localidade. |
| `2.5.0-amd64-tr-tr`         | Imagem de contêiner com a `tr-TR` localidade. |
| `2.5.0-amd64-zh-cn`         | Imagem de contêiner com a `zh-CN` localidade. |
| `2.5.0-amd64-zh-hk`         | Imagem de contêiner com a `zh-HK` localidade. |
| `2.5.0-amd64-zh-tw`         | Imagem de contêiner com a `zh-TW` localidade. |
| `2.4.0-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.4.0-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.4.0-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.4.0-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.4.0-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.4.0-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.4.0-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.4.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.4.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.4.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.4.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.4.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.4.0-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.4.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.4.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.4.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.4.0-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.4.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.4.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.4.0-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.4.0-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.4.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.4.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.4.0-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.4.0-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.4.0-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.4.0-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.4.0-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.4.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.4.0-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.4.0-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.4.0-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.4.0-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.4.0-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.4.0-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.4.0-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.4.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.4.0-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.4.0-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.3.1-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.3.1-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.3.1-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.3.1-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.3.1-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.3.1-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.3.1-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.3.1-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.3.1-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.3.1-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.3.1-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.3.1-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.3.1-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.3.1-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.3.1-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.3.1-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.3.1-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.3.1-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.3.1-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.3.1-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.3.1-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.3.1-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.3.1-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.3.1-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.3.1-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.3.1-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.3.1-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.3.1-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.3.1-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.3.1-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.3.1-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.3.1-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.3.1-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.3.1-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.3.1-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.3.1-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.3.1-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.3.1-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.3.1-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.3.0-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.3.0-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.3.0-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.3.0-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.3.0-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.3.0-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.3.0-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.3.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.3.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.3.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.3.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.3.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.3.0-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.3.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.3.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.3.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.3.0-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.3.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.3.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.3.0-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.3.0-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.3.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.3.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.3.0-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.3.0-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.3.0-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.3.0-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.3.0-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.3.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.3.0-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.3.0-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.3.0-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.3.0-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.3.0-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.3.0-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.3.0-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.3.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.3.0-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.3.0-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.2.0-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.2.0-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.2.0-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.2.0-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.2.0-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.2.0-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.2.0-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.2.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.2.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.2.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.2.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.2.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.2.0-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.2.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.2.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.2.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.2.0-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.2.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.2.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.2.0-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.2.0-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.2.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.2.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.2.0-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.2.0-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.2.0-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.2.0-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.2.0-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.2.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.2.0-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.2.0-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.2.0-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.2.0-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.2.0-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.2.0-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.2.0-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.2.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.2.0-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.2.0-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.1.1-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.1.1-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.1.1-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.1.1-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.1.1-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.1.1-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.1.1-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.1.1-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.1.1-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.1.1-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.1.1-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.1.1-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.1.1-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.1.1-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.1.1-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.1.1-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.1.1-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.1.1-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.1.1-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.1.1-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.1.1-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.1.1-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.1.1-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.1.1-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.1.1-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.1.1-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.1.1-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.1.1-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.1.1-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.1.1-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.1.1-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.1.1-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.1.1-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.1.1-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.1.1-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.1.1-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.1.1-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.1.1-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.1.1-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.1.1-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.1.0-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.1.0-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.1.0-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.1.0-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.1.0-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.1.0-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.1.0-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.1.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.1.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.1.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.1.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.1.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.1.0-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.1.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.1.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.1.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.1.0-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.1.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.1.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.1.0-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.1.0-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.1.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.1.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.1.0-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.1.0-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.1.0-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.1.0-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.1.0-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.1.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.1.0-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.1.0-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.1.0-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.1.0-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.1.0-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.1.0-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.1.0-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.1.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.1.0-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.1.0-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.0.3-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.0.2-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.0.2-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.0.2-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.0.2-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.0.2-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.0.2-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.0.2-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.0.2-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.0.2-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.0.2-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.0.2-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.0.2-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.0.2-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.0.2-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.0.2-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.0.2-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.0.2-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.0.2-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.0.2-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.0.2-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.0.2-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.0.2-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.0.2-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.0.2-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.0.2-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.0.2-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.0.2-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.0.2-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.0.2-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.0.2-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.0.2-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.0.2-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.0.2-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.0.2-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.0.2-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.0.2-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.0.2-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.0.2-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.0.2-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.0.1-amd64-ar-ae-preview` | Imagem de contêiner com a `ar-AE` localidade. |
| `2.0.1-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.0.1-amd64-ar-kw-preview` | Imagem de contêiner com a `ar-KW` localidade. |
| `2.0.1-amd64-ar-qa-preview` | Imagem de contêiner com a `ar-QA` localidade. |
| `2.0.1-amd64-ar-sa-preview` | Imagem de contêiner com a `ar-SA` localidade. |
| `2.0.1-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.0.1-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.0.1-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.0.1-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.0.1-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.0.1-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.0.1-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.0.1-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.0.1-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.0.1-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.0.1-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.0.1-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.0.1-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.0.1-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.0.1-amd64-gu-in-preview` | Imagem de contêiner com a `gu-IN` localidade. |
| `2.0.1-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.0.1-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.0.1-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.0.1-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.0.1-amd64-mr-in-preview` | Imagem de contêiner com a `mr-IN` localidade. |
| `2.0.1-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.0.1-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.0.1-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.0.1-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.0.1-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.0.1-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.0.1-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.0.1-amd64-ta-in-preview` | Imagem de contêiner com a `ta-IN` localidade. |
| `2.0.1-amd64-te-in-preview` | Imagem de contêiner com a `te-IN` localidade. |
| `2.0.1-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.0.1-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.0.1-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.0.1-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.0.1-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `2.0.0-amd64-ar-eg-preview` | Imagem de contêiner com a `ar-EG` localidade. |
| `2.0.0-amd64-ca-es-preview` | Imagem de contêiner com a `ca-ES` localidade. |
| `2.0.0-amd64-da-dk-preview` | Imagem de contêiner com a `da-DK` localidade. |
| `2.0.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `2.0.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `2.0.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `2.0.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `2.0.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `2.0.0-amd64-en-nz-preview` | Imagem de contêiner com a `en-NZ` localidade. |
| `2.0.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `2.0.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `2.0.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `2.0.0-amd64-fi-fi-preview` | Imagem de contêiner com a `fi-FI` localidade. |
| `2.0.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `2.0.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `2.0.0-amd64-hi-in-preview` | Imagem de contêiner com a `hi-IN` localidade. |
| `2.0.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `2.0.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `2.0.0-amd64-ko-kr-preview` | Imagem de contêiner com a `ko-KR` localidade. |
| `2.0.0-amd64-nb-no-preview` | Imagem de contêiner com a `nb-NO` localidade. |
| `2.0.0-amd64-nl-nl-preview` | Imagem de contêiner com a `nl-NL` localidade. |
| `2.0.0-amd64-pl-pl-preview` | Imagem de contêiner com a `pl-PL` localidade. |
| `2.0.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `2.0.0-amd64-pt-pt-preview` | Imagem de contêiner com a `pt-PT` localidade. |
| `2.0.0-amd64-ru-ru-preview` | Imagem de contêiner com a `ru-RU` localidade. |
| `2.0.0-amd64-sv-se-preview` | Imagem de contêiner com a `sv-SE` localidade. |
| `2.0.0-amd64-th-th-preview` | Imagem de contêiner com a `th-TH` localidade. |
| `2.0.0-amd64-tr-tr-preview` | Imagem de contêiner com a `tr-TR` localidade. |
| `2.0.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `2.0.0-amd64-zh-hk-preview` | Imagem de contêiner com a `zh-HK` localidade. |
| `2.0.0-amd64-zh-tw-preview` | Imagem de contêiner com a `zh-TW` localidade. |
| `1.2.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `1.2.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `1.2.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `1.2.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `1.2.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `1.2.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `1.2.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `1.2.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `1.2.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `1.2.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `1.2.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `1.2.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `1.2.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `1.2.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `1.1.3-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `1.1.3-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `1.1.3-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `1.1.3-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `1.1.3-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `1.1.3-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `1.1.3-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `1.1.3-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `1.1.3-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `1.1.3-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `1.1.3-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `1.1.3-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `1.1.3-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `1.1.3-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `1.1.2-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `1.1.2-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `1.1.2-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `1.1.2-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `1.1.2-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `1.1.2-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `1.1.2-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `1.1.2-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `1.1.2-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `1.1.2-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `1.1.2-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `1.1.2-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `1.1.2-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `1.1.2-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `1.1.1-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `1.1.1-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `1.1.1-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `1.1.1-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `1.1.1-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `1.1.1-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `1.1.1-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `1.1.1-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `1.1.1-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `1.1.1-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `1.1.1-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `1.1.1-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `1.1.1-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `1.1.1-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `1.1.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `1.1.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `1.1.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `1.1.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `1.1.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `1.1.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `1.1.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `1.1.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `1.1.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `1.1.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `1.1.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `1.1.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `1.1.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `1.1.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |
| `1.0.0-amd64-de-de-preview` | Imagem de contêiner com a `de-DE` localidade. |
| `1.0.0-amd64-en-au-preview` | Imagem de contêiner com a `en-AU` localidade. |
| `1.0.0-amd64-en-ca-preview` | Imagem de contêiner com a `en-CA` localidade. |
| `1.0.0-amd64-en-gb-preview` | Imagem de contêiner com a `en-GB` localidade. |
| `1.0.0-amd64-en-in-preview` | Imagem de contêiner com a `en-IN` localidade. |
| `1.0.0-amd64-en-us-preview` | Imagem de contêiner com a `en-US` localidade. |
| `1.0.0-amd64-es-es-preview` | Imagem de contêiner com a `es-ES` localidade. |
| `1.0.0-amd64-es-mx-preview` | Imagem de contêiner com a `es-MX` localidade. |
| `1.0.0-amd64-fr-ca-preview` | Imagem de contêiner com a `fr-CA` localidade. |
| `1.0.0-amd64-fr-fr-preview` | Imagem de contêiner com a `fr-FR` localidade. |
| `1.0.0-amd64-it-it-preview` | Imagem de contêiner com a `it-IT` localidade. |
| `1.0.0-amd64-ja-jp-preview` | Imagem de contêiner com a `ja-JP` localidade. |
| `1.0.0-amd64-pt-br-preview` | Imagem de contêiner com a `pt-BR` localidade. |
| `1.0.0-amd64-zh-cn-preview` | Imagem de contêiner com a `zh-CN` localidade. |

## <a name="text-to-speech"></a>Conversão de texto em fala

A imagem de contêiner de [conversão de texto em fala][sp-tts] pode ser encontrada no registro de `containerpreview.azurecr.io` contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-text-to-speech` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.         |
| `1.7.0-amd64-ar-eg-hoda`                    | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `1.7.0-amd64-ar-sa-naayf`                   | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `1.7.0-amd64-bg-bg-ivan`                    | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `1.7.0-amd64-ca-es-herenarus`               | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `1.7.0-amd64-cs-cz-jakub`                   | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `1.7.0-amd64-da-dk-hellerus`                | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `1.7.0-amd64-de-at-michael`                 | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `1.7.0-amd64-de-ch-karsten`                 | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `1.7.0-amd64-de-de-hedda`                   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.7.0-amd64-de-de-heddarus`                | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.7.0-amd64-de-de-stefan-apollo`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.7.0-amd64-el-gr-stefanos`                | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `1.7.0-amd64-en-au-catherine`               | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.7.0-amd64-en-au-hayleyrus`               | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.7.0-amd64-en-ca-heatherrus`              | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `1.7.0-amd64-en-ca-linda`                   | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `1.7.0-amd64-en-gb-george-apollo`           | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.7.0-amd64-en-gb-hazelrus`                | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.7.0-amd64-en-gb-susan-apollo`            | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.7.0-amd64-en-ie-sean`                    | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `1.7.0-amd64-en-in-heera-apollo`            | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.7.0-amd64-en-in-priyarus`                | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.7.0-amd64-en-in-ravi-apollo`             | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.7.0-amd64-en-us-benjaminrus`             | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.7.0-amd64-en-us-guy24krus`               | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.7.0-amd64-en-us-aria24krus`              | Imagem de contêiner com a `en-US` localidade e a `en-US-Aria24kRUS` voz.      |
| `1.7.0-amd64-en-us-ariarus`                 | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.         |
| `1.7.0-amd64-en-us-zirarus`                 | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.7.0-amd64-es-es-helenarus`               | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.7.0-amd64-es-es-laura-apollo`            | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.7.0-amd64-es-es-pablo-apollo`            | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.7.0-amd64-es-mx-hildarus`                | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.7.0-amd64-es-mx-raul-apollo`             | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.7.0-amd64-fi-fi-heidirus`                | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `1.7.0-amd64-fr-ca-caroline`                | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.7.0-amd64-fr-ca-harmonierus`             | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.7.0-amd64-fr-ch-guillaume`               | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `1.7.0-amd64-fr-fr-hortenserus`             | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.7.0-amd64-fr-fr-julie-apollo`            | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.7.0-amd64-fr-fr-paul-apollo`             | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.7.0-amd64-he-il-asaf`                    | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `1.7.0-amd64-hi-in-hemant`                  | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `1.7.0-amd64-hi-in-kalpana-apollo`          | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana-Apollo` voz.  |
| `1.7.0-amd64-hi-in-kalpana`                 | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `1.7.0-amd64-hr-hr-matej`                   | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `1.7.0-amd64-hu-hu-szabolcs`                | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `1.7.0-amd64-id-id-andika`                  | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `1.7.0-amd64-it-it-cosimo-apollo`           | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.7.0-amd64-it-it-luciarus`                | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.7.0-amd64-ja-jp-ayumi-apollo`            | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.7.0-amd64-ja-jp-harukarus`               | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.7.0-amd64-ja-jp-ichiro-apollo`           | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.7.0-amd64-ko-kr-heamirus`                | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.7.0-amd64-ms-my-rizwan`                  | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `1.7.0-amd64-nb-no-huldarus`                | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `1.7.0-amd64-nl-nl-hannarus`                | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `1.7.0-amd64-pl-pl-paulinarus`              | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `1.7.0-amd64-pt-br-daniel-apollo`           | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.7.0-amd64-pt-br-heloisarus`              | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.7.0-amd64-pt-pt-heliarus`                | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `1.7.0-amd64-ro-ro-andrei`                  | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `1.7.0-amd64-ru-ru-ekaterinarus`            | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `1.7.0-amd64-ru-ru-irina-apollo`            | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `1.7.0-amd64-ru-ru-pavel-apollo`            | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `1.7.0-amd64-sk-sk-filip`                   | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `1.7.0-amd64-sl-si-lado`                    | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `1.7.0-amd64-sv-se-hedvigrus`               | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `1.7.0-amd64-ta-in-valluvar`                | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `1.7.0-amd64-te-in-chitra`                  | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `1.7.0-amd64-th-th-pattara`                 | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `1.7.0-amd64-tr-tr-sedarus`                 | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `1.7.0-amd64-vi-vn-an`                      | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `1.7.0-amd64-zh-cn-huihuirus`               | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.7.0-amd64-zh-cn-kangkang-apollo`         | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.7.0-amd64-zh-cn-yaoyao-apollo`           | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.7.0-amd64-zh-hk-danny-apollo`            | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `1.7.0-amd64-zh-hk-tracy-apollo`            | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `1.7.0-amd64-zh-hk-tracyrus`                | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `1.7.0-amd64-zh-tw-hanhanrus`               | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `1.7.0-amd64-zh-tw-yating-apollo`           | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `1.7.0-amd64-zh-tw-zhiwei-apollo`           | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |
| `1.6.0-amd64-ar-eg-hoda-preview`            | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `1.6.0-amd64-ar-sa-naayf-preview`           | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `1.6.0-amd64-bg-bg-ivan-preview`            | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `1.6.0-amd64-ca-es-herenarus-preview`       | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `1.6.0-amd64-cs-cz-jakub-preview`           | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `1.6.0-amd64-da-dk-hellerus-preview`        | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `1.6.0-amd64-de-at-michael-preview`         | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `1.6.0-amd64-de-ch-karsten-preview`         | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `1.6.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.6.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.6.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.6.0-amd64-el-gr-stefanos-preview`        | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `1.6.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.6.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.6.0-amd64-en-ca-heatherrus-preview`      | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `1.6.0-amd64-en-ca-linda-preview`           | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `1.6.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.6.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.6.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.6.0-amd64-en-ie-sean-preview`            | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `1.6.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.6.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.6.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.6.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.6.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.6.0-amd64-en-us-aria24krus-preview`      | Imagem de contêiner com a `en-US` localidade e a `en-US-Aria24kRUS` voz.      |
| `1.6.0-amd64-en-us-ariarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.         |
| `1.6.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.6.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.6.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.6.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.6.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.6.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.6.0-amd64-fi-fi-heidirus-preview`        | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `1.6.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.6.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.6.0-amd64-fr-ch-guillaume-preview`       | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `1.6.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.6.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.6.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.6.0-amd64-he-il-asaf-preview`            | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `1.6.0-amd64-hi-in-hemant-preview`          | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `1.6.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana-Apollo` voz.  |
| `1.6.0-amd64-hi-in-kalpana-preview`         | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `1.6.0-amd64-hr-hr-matej-preview`           | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `1.6.0-amd64-hu-hu-szabolcs-preview`        | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `1.6.0-amd64-id-id-andika-preview`          | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `1.6.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.6.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.6.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.6.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.6.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.6.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.6.0-amd64-ms-my-rizwan-preview`          | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `1.6.0-amd64-nb-no-huldarus-preview`        | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `1.6.0-amd64-nl-nl-hannarus-preview`        | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `1.6.0-amd64-pl-pl-paulinarus-preview`      | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `1.6.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.6.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.6.0-amd64-pt-pt-heliarus-preview`        | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `1.6.0-amd64-ro-ro-andrei-preview`          | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `1.6.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `1.6.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `1.6.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `1.6.0-amd64-sk-sk-filip-preview`           | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `1.6.0-amd64-sl-si-lado-preview`            | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `1.6.0-amd64-sv-se-hedvigrus-preview`       | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `1.6.0-amd64-ta-in-valluvar-preview`        | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `1.6.0-amd64-te-in-chitra-preview`          | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `1.6.0-amd64-th-th-pattara-preview`         | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `1.6.0-amd64-tr-tr-sedarus-preview`         | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `1.6.0-amd64-vi-vn-an-preview`              | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `1.6.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.6.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.6.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.6.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `1.6.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `1.6.0-amd64-zh-hk-tracyrus-preview`        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `1.6.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `1.6.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `1.6.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |
| `1.5.0-amd64-ar-eg-hoda-preview`            | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `1.5.0-amd64-ar-sa-naayf-preview`           | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `1.5.0-amd64-bg-bg-ivan-preview`            | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `1.5.0-amd64-ca-es-herenarus-preview`       | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `1.5.0-amd64-cs-cz-jakub-preview`           | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `1.5.0-amd64-da-dk-hellerus-preview`        | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `1.5.0-amd64-de-at-michael-preview`         | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `1.5.0-amd64-de-ch-karsten-preview`         | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `1.5.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.5.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.5.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.5.0-amd64-el-gr-stefanos-preview`        | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `1.5.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.5.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.5.0-amd64-en-ca-heatherrus-preview`      | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `1.5.0-amd64-en-ca-linda-preview`           | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `1.5.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.5.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.5.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.5.0-amd64-en-ie-sean-preview`            | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `1.5.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.5.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.5.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.5.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.5.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.5.0-amd64-en-us-aria24krus-preview`      | Imagem de contêiner com a `en-US` localidade e a `en-US-Aria24kRUS` voz.     |
| `1.5.0-amd64-en-us-ariarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.        |
| `1.5.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.5.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.5.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.5.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.5.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.5.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.5.0-amd64-fi-fi-heidirus-preview`        | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `1.5.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.5.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.5.0-amd64-fr-ch-guillaume-preview`       | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `1.5.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.5.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.5.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.5.0-amd64-he-il-asaf-preview`            | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `1.5.0-amd64-hi-in-hemant-preview`          | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `1.5.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana-Apollo` voz.  |
| `1.5.0-amd64-hi-in-kalpana-preview`         | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `1.5.0-amd64-hr-hr-matej-preview`           | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `1.5.0-amd64-hu-hu-szabolcs-preview`        | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `1.5.0-amd64-id-id-andika-preview`          | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `1.5.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.5.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.5.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.5.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.5.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.5.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.5.0-amd64-ms-my-rizwan-preview`          | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `1.5.0-amd64-nb-no-huldarus-preview`        | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `1.5.0-amd64-nl-nl-hannarus-preview`        | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `1.5.0-amd64-pl-pl-paulinarus-preview`      | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `1.5.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.5.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.5.0-amd64-pt-pt-heliarus-preview`        | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `1.5.0-amd64-ro-ro-andrei-preview`          | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `1.5.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `1.5.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `1.5.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `1.5.0-amd64-sk-sk-filip-preview`           | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `1.5.0-amd64-sl-si-lado-preview`            | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `1.5.0-amd64-sv-se-hedvigrus-preview`       | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `1.5.0-amd64-ta-in-valluvar-preview`        | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `1.5.0-amd64-te-in-chitra-preview`          | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `1.5.0-amd64-th-th-pattara-preview`         | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `1.5.0-amd64-tr-tr-sedarus-preview`         | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `1.5.0-amd64-vi-vn-an-preview`              | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `1.5.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.5.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.5.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.5.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `1.5.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `1.5.0-amd64-zh-hk-tracyrus-preview`        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `1.5.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `1.5.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `1.5.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |
| `1.4.0-amd64-ar-eg-hoda-preview`            | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `1.4.0-amd64-ar-sa-naayf-preview`           | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `1.4.0-amd64-bg-bg-ivan-preview`            | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `1.4.0-amd64-ca-es-herenarus-preview`       | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `1.4.0-amd64-cs-cz-jakub-preview`           | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `1.4.0-amd64-da-dk-hellerus-preview`        | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `1.4.0-amd64-de-at-michael-preview`         | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `1.4.0-amd64-de-ch-karsten-preview`         | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `1.4.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.4.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.4.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.4.0-amd64-el-gr-stefanos-preview`        | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `1.4.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.4.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.4.0-amd64-en-ca-heatherrus-preview`      | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `1.4.0-amd64-en-ca-linda-preview`           | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `1.4.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.4.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.4.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.4.0-amd64-en-ie-sean-preview`            | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `1.4.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.4.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.4.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.4.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.4.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.4.0-amd64-en-us-aria24krus-preview`      | Imagem de contêiner com a `en-US` localidade e a `en-US-Aria24kRUS` voz.     |
| `1.4.0-amd64-en-us-ariarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.        |
| `1.4.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.4.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.4.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.4.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.4.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.4.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.4.0-amd64-fi-fi-heidirus-preview`        | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `1.4.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.4.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.4.0-amd64-fr-ch-guillaume-preview`       | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `1.4.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.4.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.4.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.4.0-amd64-he-il-asaf-preview`            | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `1.4.0-amd64-hi-in-hemant-preview`          | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `1.4.0-amd64-hi-in-kalpana-apollo-preview`  | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana-Apollo` voz.  |
| `1.4.0-amd64-hi-in-kalpana-preview`         | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `1.4.0-amd64-hr-hr-matej-preview`           | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `1.4.0-amd64-hu-hu-szabolcs-preview`        | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `1.4.0-amd64-id-id-andika-preview`          | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `1.4.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.4.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.4.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.4.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.4.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.4.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.4.0-amd64-ms-my-rizwan-preview`          | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `1.4.0-amd64-nb-no-huldarus-preview`        | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `1.4.0-amd64-nl-nl-hannarus-preview`        | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `1.4.0-amd64-pl-pl-paulinarus-preview`      | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `1.4.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.4.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.4.0-amd64-pt-pt-heliarus-preview`        | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `1.4.0-amd64-ro-ro-andrei-preview`          | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `1.4.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `1.4.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `1.4.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `1.4.0-amd64-sk-sk-filip-preview`           | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `1.4.0-amd64-sl-si-lado-preview`            | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `1.4.0-amd64-sv-se-hedvigrus-preview`       | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `1.4.0-amd64-ta-in-valluvar-preview`        | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `1.4.0-amd64-te-in-chitra-preview`          | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `1.4.0-amd64-th-th-pattara-preview`         | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `1.4.0-amd64-tr-tr-sedarus-preview`         | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `1.4.0-amd64-vi-vn-an-preview`              | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `1.4.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.4.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.4.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.4.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `1.4.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `1.4.0-amd64-zh-hk-tracyrus-preview`        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `1.4.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `1.4.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `1.4.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `1.3.0-amd64-de-at-michael-preview`         | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com a `de-DE` localidade e a `de-DE-HeddaRUS` voz.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-Jessa24kRUS` voz.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com a `en-US` localidade e a `en-US-JessaRUS` voz.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `1.3.0-amd64-id-id-andika-preview`          | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |
| `1.2.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com a `de-DE` localidade e a `de-DE-HeddaRUS` voz.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-Jessa24kRUS` voz.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com a `en-US` localidade e a `en-US-JessaRUS` voz.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Imagem de contêiner com a `de-DE` localidade e a `de-DE-HeddaRUS` voz.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-Jessa24kRUS` voz.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com a `en-US` localidade e a `en-US-JessaRUS` voz.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Imagem de contêiner com a `en-US` localidade e a `en-US-Jessa24kRUS` voz.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Imagem de contêiner com a `en-US` localidade e a `en-US-JessaRUS` voz.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |

## <a name="neural-text-to-speech"></a>Texto em fala neural

A imagem de contêiner [texto em fala neural] [SP-ntts] pode ser encontrada no `containerpreview.azurecr.io` registro de contêiner. Ele reside no `microsoft` repositório e é nomeado `cognitive-services-neural-text-to-speech` . O nome da imagem de contêiner totalmente qualificado é, `containerpreview.azurecr.io/microsoft/cognitive-services-neural-text-to-speech` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `1.2.0-amd64-de-de-katjaneural-preview`     | Imagem de contêiner com a `de-DE` localidade e a `de-DE-KatjaNeural` voz.     |
| `1.2.0-amd64-en-au-natashaneural-preview`   | Imagem de contêiner com a `en-AU` localidade e a `en-AU-NatashaNeural` voz.   |
| `1.2.0-amd64-en-ca-claraneural-preview`     | Imagem de contêiner com a `en-CA` localidade e a `en-CA-ClaraNeural` voz.     |
| `1.2.0-amd64-en-gb-libbyneural-preview`     | Imagem de contêiner com a `en-GB` localidade e a `en-GB-LibbyNeural` voz.     |
| `1.2.0-amd64-en-gb-mianeural-preview`       | Imagem de contêiner com a `en-GB` localidade e a `en-GB-MiaNeural` voz.       |
| `1.2.0-amd64-en-us-arianeural-preview`      | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `1.2.0-amd64-en-us-guyneural-preview`       | Imagem de contêiner com a `en-US` localidade e a `en-US-GuyNeural` voz.       |
| `1.2.0-amd64-es-es-elviraneural-preview`    | Imagem de contêiner com a `es-ES` localidade e a `es-ES-ElviraNeural` voz.    |
| `1.2.0-amd64-es-mx-dalianeural-preview`     | Imagem de contêiner com a `es-MX` localidade e a `es-MX-DaliaNeural` voz.     |
| `1.2.0-amd64-fr-ca-sylvieneural-preview`    | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-SylvieNeural` voz.    |
| `1.2.0-amd64-fr-fr-deniseneural-preview`    | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-DeniseNeural` voz.    |
| `1.2.0-amd64-it-it-elsaneural-preview`      | Imagem de contêiner com a `it-IT` localidade e a `it-IT-ElsaNeural` voz.      |
| `1.2.0-amd64-ja-jp-nanamineural-preview`    | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-NanamiNeural` voz.    |
| `1.2.0-amd64-ko-kr-sunhineural-preview`     | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-SunHiNeural` voz.     |
| `1.2.0-amd64-pt-br-franciscaneural-preview` | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-FranciscaNeural` voz. |
| `1.2.0-amd64-zh-cn-xiaoxiaoneural-preview`  | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-XiaoxiaoNeural` voz.  |

## <a name="key-phrase-extraction"></a>Extração de Frases-Chave

A imagem de contêiner [extração de frases-chave][ta-kp] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `keyphrase` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/keyphrase` .

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

A imagem de contêiner [detecção de idioma][ta-la] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `language` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language` .

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

A imagem de contêiner [análise de sentimento][ta-se] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `sentiment` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/sentiment` .

Essa imagem de contêiner tem as seguintes marcas disponíveis:

| Marcas de imagem | Observações                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Análise de Sentimento v3 (inglês)               |
| `3.0-es`   | Análise de Sentimento v3 (espanhol)               |
| `3.0-fr`   | Análise de Sentimento v3 (francês)                |
| `3.0-it`   | Análise de Sentimento v3 (italiano)               |
| `3.0-de`   | Análise de Sentimento v3 (alemão)                |
| `3.0-zh`   | Análise de Sentimento v3 (chinês simplificado)  |
| `3.0-zht`  | Análise de Sentimento v3 (chinês tradicional) |
| `3.0-ja`   | Análise de Sentimento v3 (japonês)              |
| `3.0-pt`   | Análise de Sentimento v3 (Português)            |
| `3.0-nl`   | Análise de Sentimento v3 (Holandês)                 |
| `1.1.009301-amd64-preview`    | Análise de Sentimento v2      |
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
