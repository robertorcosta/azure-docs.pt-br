---
title: Marcas de imagem de contêiner dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma listagem abrangente de todas as marcas de imagem de contêiner de serviço cognitiva.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: ba51776942ad28fc8d4b0db7dd2d0e162e5322b5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743330"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Marcas de imagem e notas de versão do contêiner de serviços cognitivas do Azure

Os serviços cognitivas do Azure oferecem muitas imagens de contêiner. Os registros de contêiner e os repositórios correspondentes variam entre as imagens de contêiner. Cada nome de imagem de contêiner oferece várias marcas. Uma marca de imagem de contêiner é um mecanismo de controle de versão da imagem de contêiner. Este artigo destina-se a ser usado como uma referência abrangente para listar todas as imagens de contêiner de serviços cognitivas e suas marcas disponíveis.

> [!TIP]
> Ao usar [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o, preste muita atenção à capitalização do registro de contêiner, do repositório, do nome da imagem de contêiner e da marca correspondente, pois eles diferenciam **maiúsculas de minúsculas**.

## <a name="anomaly-detector"></a>Detector de Anomalias

A imagem de contêiner do [detector de anomalias][ad-containers] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/decision` repositório e é nomeado `anomaly-detector` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Última versão](#tab/current)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Ler OCR (reconhecimento óptico de caracteres)

A imagem de contêiner de OCR de [Pesquisa Visual computacional][cv-containers] leitura pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services` repositório e é nomeado `read` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Última versão](#tab/current)

Notas de versão para `v2.0.013250001-amd64-preview` :

* Diminuir ainda mais o uso de memória para o contêiner.
* O cache externo é necessário para a configuração de vários pods. Por exemplo, configure Redis para Caching.
* Correção de resultados ausentes quando o cache Redis está configurado e `ResultExpirationPeriod` definido como 0.
* Remova a limitação do tamanho do corpo da solicitação de 26MB. O contêiner agora pode aceitar >arquivos 26MB.
* Adicione carimbo de data/hora e versão de compilação ao log do console.

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

Notas de versão do `1.1.013050001-amd64-preview`

* Adicionada `ReadEngineConfig:ResultExpirationPeriod` configuração de inicialização de contêiner para especificar quando o sistema deve limpar os resultados de reconhecimento. 
    * A configuração é em horas, e o valor padrão é de 48 horas.
    * A configuração pode reduzir o uso de memória para o armazenamento de resultados, especialmente quando o armazenamento na memória do contêiner é usado.
    * Exemplo 1. ReadEngineConfig: ResultExpirationPeriod = 1, o sistema limpará o resultado de reconhecimento 1h após o processo.
    * Se essa configuração for definida como 0, o sistema limpará o resultado do reconhecimento depois que o resultado for recuperado.

* Foi corrigido um erro de servidor interno 500 quando um formato de imagem inválido é passado para o sistema. Agora, ele retornará um erro 400:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Reconhecimento de Formulários

A imagem de contêiner do [reconhecedor de formulário][fr-containers] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/custom-form` repositório e é nomeado `labeltool` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Última versão](#tab/current)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Reconhecimento Vocal (LUIS)

A imagem de contêiner [Luis][lu-containers] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/language` repositório e é nomeado `luis` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Última versão](#tab/current)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Fala Personalizada para texto

A imagem de contêiner de [fala personalizada para texto][sp-cstt] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/speechservices/` repositório e é nomeado `custom-speech-to-text` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Última versão](#tab/current)

Nota de versão para `2.6.0-amd64` :

**Recursos**
* Suporte para a expressão v2 
* As listas de frases têm suporte nas seguintes localidades:
    * EN-au
    * EN-AC
    * en-GB
    * EN-in
    * en-NZ
    * pt-br
    * zh-cn
* Suporte para download de modelo de base personalizado. 
    * Use `BaseModelLocale=<locale>` com o `docker run` comando
* Migrado completamente para o .NET 3,1

**Correções**
* Corrigido um problema em que a pontuação de confiança era sempre 1 no modo Diarization
* Migrado para a API do textanalytics 3,0

Observe que, devido às listas de frases incluídas, o tamanho dessa imagem de contêiner aumentou.

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de versão para `2.5.0-amd64` :

**Recursos**
* Suporte a pronúncia personalizada em modelos personalizados
* Suporte para Azure e nuvem do Azure no governo dos EUA

**Correções**
* Corrigir execução como um problema de usuário não raiz no modo Diarization

| Marcas de imagem                    | Observações               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   primeira versão GA    |

---

## <a name="custom-text-to-speech"></a>Conversão de texto em fala personalizada

A imagem de contêiner de [texto em fala personalizado][sp-ctts] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/speechservices/` repositório e é nomeado `custom-text-to-speech` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Última versão](#tab/current)

Nota de versão para `1.8.0-amd64` :

**Recursos**
* Migrado completamente para o .NET 3,1

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de versão para `1.7.0-amd64` :

**Recurso**
* Migrado parcialmente para o .NET 3,1

| Marcas de imagem                    | Observações               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   primeira versão GA    |

---

## <a name="speech-to-text"></a>Conversão de fala em texto

A imagem de contêiner de [fala em texto][sp-stt] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/speechservices/` repositório e é nomeado `speech-to-text` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Você pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Desde a 2.5.0 de fala em texto, as imagens têm suporte na região da *Virgínia do governo dos EUA* . Use o ponto de extremidade de cobrança do *Virgínia do governo dos EUA* e as chaves de API ao usar essa região.

# <a name="latest-version"></a>[Última versão](#tab/current)

Nota de versão para `2.6.0-amd64-<locale>` :

**Recursos**
* Atualizado para os modelos mais recentes e migrado totalmente para o .NET 3,1
* Suporte para a expressão v2
* As listas de frases têm suporte nas seguintes localidades:
    * EN-au
    * EN-AC
    * en-GB
    * EN-in
    * en-NZ
    * pt-br
    * zh-cn
* Suporte para nova localidade `cs-CZ` 
    * Não há suporte para capitalização e pontuação no momento.

**Correções**
* Corrige um problema em que as pontuações de confiança eram sempre 1 no modo Diarization
* Migrado use a API do textanalytics 3,0

Observe que, devido às listas de frases incluídas, o tamanho dessa imagem de contêiner aumentou. 

| Marcas de imagem                    | Observações                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Imagem de contêiner com a `en-US` localidade.                                                             |
| `2.6.0-amd64-<locale>`        | Substitua `<locale>` por uma das localidades disponíveis, listadas abaixo. Por exemplo, `2.6.0-amd64-en-us`. |

Esse contêiner tem as seguintes localidades disponíveis.

| Localidade para v 2.6.0           | Observações                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Imagem de contêiner com a `ar-AE` localidade. |
| `ar-eg`                     | Imagem de contêiner com a `ar-EG` localidade. |
| `ar-kw`                     | Imagem de contêiner com a `ar-KW` localidade. |
| `ar-qa`                     | Imagem de contêiner com a `ar-QA` localidade. |
| `ar-sa`                     | Imagem de contêiner com a `ar-SA` localidade. |
| `ca-es`                     | Imagem de contêiner com a `ca-ES` localidade. |
| `cs-cz`                     | Imagem de contêiner com a `cs-CZ` localidade. |
| `da-dk`                     | Imagem de contêiner com a `da-DK` localidade. |
| `de-de`                     | Imagem de contêiner com a `de-DE` localidade. |
| `en-au`                     | Imagem de contêiner com a `en-AU` localidade. |
| `en-ca`                     | Imagem de contêiner com a `en-CA` localidade. |
| `en-gb`                     | Imagem de contêiner com a `en-GB` localidade. |
| `en-in`                     | Imagem de contêiner com a `en-IN` localidade. |
| `en-nz`                     | Imagem de contêiner com a `en-NZ` localidade. |
| `en-us`                     | Imagem de contêiner com a `en-US` localidade. |
| `es-es`                     | Imagem de contêiner com a `es-ES` localidade. |
| `es-mx`                     | Imagem de contêiner com a `es-MX` localidade. |
| `fi-fi`                     | Imagem de contêiner com a `fi-FI` localidade. |
| `fr-ca`                     | Imagem de contêiner com a `fr-CA` localidade. |
| `fr-fr`                     | Imagem de contêiner com a `fr-FR` localidade. |
| `gu-in`                     | Imagem de contêiner com a `gu-IN` localidade. |
| `hi-in`                     | Imagem de contêiner com a `hi-IN` localidade. |
| `it-it`                     | Imagem de contêiner com a `it-IT` localidade. |
| `ja-jp`                     | Imagem de contêiner com a `ja-JP` localidade. |
| `ko-kr`                     | Imagem de contêiner com a `ko-KR` localidade. |
| `mr-in`                     | Imagem de contêiner com a `mr-IN` localidade. |
| `nb-no`                     | Imagem de contêiner com a `nb-NO` localidade. |
| `nl-nl`                     | Imagem de contêiner com a `nl-NL` localidade. |
| `pl-pl`                     | Imagem de contêiner com a `pl-PL` localidade. |
| `pt-br`                     | Imagem de contêiner com a `pt-BR` localidade. |
| `pt-pt`                     | Imagem de contêiner com a `pt-PT` localidade. |
| `ru-ru`                     | Imagem de contêiner com a `ru-RU` localidade. |
| `sv-se`                     | Imagem de contêiner com a `sv-SE` localidade. |
| `ta-in`                     | Imagem de contêiner com a `ta-IN` localidade. |
| `te-in`                     | Imagem de contêiner com a `te-IN` localidade. |
| `th-th`                     | Imagem de contêiner com a `th-TH` localidade. |
| `tr-tr`                     | Imagem de contêiner com a `tr-TR` localidade. |
| `zh-cn`                     | Imagem de contêiner com a `zh-CN` localidade. |
| `zh-hk`                     | Imagem de contêiner com a `zh-HK` localidade. |
| `zh-tw`                     | Imagem de contêiner com a `zh-TW` localidade. |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de versão para `2.5.0-amd64-<locale>` :

**Recursos**
* Suporte para a nuvem do governo dos EUA do Azure

**Correções**
* Corrige um problema com a execução como um usuário não raiz no modo Diarization

| Marcas de imagem                  | Observações                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Substitua `<locale>` por uma das localidades disponíveis, listadas abaixo. Por exemplo, `2.5.0-amd64-en-us`.  |

Esse contêiner tem as seguintes localidades disponíveis.

| Localidade para v 2.5.0           | Observações                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Imagem de contêiner com a `ar-AE` localidade. |
| `ar-eg`                     | Imagem de contêiner com a `ar-EG` localidade. |
| `ar-kw`                     | Imagem de contêiner com a `ar-KW` localidade. |
| `ar-qa`                     | Imagem de contêiner com a `ar-QA` localidade. |
| `ar-sa`                     | Imagem de contêiner com a `ar-SA` localidade. |
| `ca-es`                     | Imagem de contêiner com a `ca-ES` localidade. |
| `da-dk`                     | Imagem de contêiner com a `da-DK` localidade. |
| `de-de`                     | Imagem de contêiner com a `de-DE` localidade. |
| `en-au`                     | Imagem de contêiner com a `en-AU` localidade. |
| `en-ca`                     | Imagem de contêiner com a `en-CA` localidade. |
| `en-gb`                     | Imagem de contêiner com a `en-GB` localidade. |
| `en-in`                     | Imagem de contêiner com a `en-IN` localidade. |
| `en-nz`                     | Imagem de contêiner com a `en-NZ` localidade. |
| `en-us`                     | Imagem de contêiner com a `en-US` localidade. |
| `es-es`                     | Imagem de contêiner com a `es-ES` localidade. |
| `es-mx`                     | Imagem de contêiner com a `es-MX` localidade. |
| `fi-fi`                     | Imagem de contêiner com a `fi-FI` localidade. |
| `fr-ca`                     | Imagem de contêiner com a `fr-CA` localidade. |
| `fr-fr`                     | Imagem de contêiner com a `fr-FR` localidade. |
| `gu-in`                     | Imagem de contêiner com a `gu-IN` localidade. |
| `hi-in`                     | Imagem de contêiner com a `hi-IN` localidade. |
| `it-it`                     | Imagem de contêiner com a `it-IT` localidade. |
| `ja-jp`                     | Imagem de contêiner com a `ja-JP` localidade. |
| `ko-kr`                     | Imagem de contêiner com a `ko-KR` localidade. |
| `mr-in`                     | Imagem de contêiner com a `mr-IN` localidade. |
| `nb-no`                     | Imagem de contêiner com a `nb-NO` localidade. |
| `nl-nl`                     | Imagem de contêiner com a `nl-NL` localidade. |
| `pl-pl`                     | Imagem de contêiner com a `pl-PL` localidade. |
| `pt-br`                     | Imagem de contêiner com a `pt-BR` localidade. |
| `pt-pt`                     | Imagem de contêiner com a `pt-PT` localidade. |
| `ru-ru`                     | Imagem de contêiner com a `ru-RU` localidade. |
| `sv-se`                     | Imagem de contêiner com a `sv-SE` localidade. |
| `ta-in`                     | Imagem de contêiner com a `ta-IN` localidade. |
| `te-in`                     | Imagem de contêiner com a `te-IN` localidade. |
| `th-th`                     | Imagem de contêiner com a `th-TH` localidade. |
| `tr-tr`                     | Imagem de contêiner com a `tr-TR` localidade. |
| `zh-cn`                     | Imagem de contêiner com a `zh-CN` localidade. |
| `zh-hk`                     | Imagem de contêiner com a `zh-HK` localidade. |
| `zh-tw`                     | Imagem de contêiner com a `zh-TW` localidade. |

---

## <a name="text-to-speech"></a>Conversão de texto em fala

A imagem de contêiner de [conversão de texto em fala][sp-tts] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/speechservices/` repositório e é nomeado `text-to-speech` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Última versão](#tab/current)

Nota de versão para `1.8.0-amd64-<locale-and-voice>` :

**Recurso**
* Migrado completamente para o .NET 3,1

| Marcas de imagem                                  | Observações                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Substitua `<locale>` por uma das localidades disponíveis, listadas abaixo. Por exemplo, `1.8.0-amd64-en-us-ariarus`.  |


| Localidades para v 1.8.0                          | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `ar-sa-naayf`                               | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `bg-bg-ivan`                                | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `ca-es-herenarus`                           | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `cs-cz-jakub`                               | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `da-dk-hellerus`                            | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `de-at-michael`                             | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `de-ch-karsten`                             | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `de-de-hedda`                               | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `de-de-heddarus`                            | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `de-de-stefan-apollo`                       | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `el-gr-stefanos`                            | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `en-au-catherine`                           | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `en-au-hayleyrus`                           | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `en-ca-heatherrus`                          | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `en-ca-linda`                               | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `en-gb-george-apollo`                       | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `en-gb-hazelrus`                            | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `en-gb-susan-apollo`                        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `en-ie-sean`                                | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `en-in-heera-apollo`                        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `en-in-priyarus`                            | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `en-in-ravi-apollo`                         | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `en-us-benjaminrus`                         | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `en-us-guy24krus`                           | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `en-us-aria24krus`                          | Imagem de contêiner com a `en-US` localidade e a `en-US-Aria24kRUS` voz.      |
| `en-us-ariarus`                             | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.         |
| `en-us-zirarus`                             | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `es-es-helenarus`                           | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `es-es-laura-apollo`                        | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `es-es-pablo-apollo`                        | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `es-mx-hildarus`                            | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `es-mx-raul-apollo`                         | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `fi-fi-heidirus`                            | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `fr-ca-caroline`                            | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `fr-ca-harmonierus`                         | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `fr-ch-guillaume`                           | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `fr-fr-hortenserus`                         | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `fr-fr-julie-apollo`                        | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `fr-fr-paul-apollo`                         | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `he-il-asaf`                                | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `hi-in-hemant`                              | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `hi-in-kalpana-apollo`                      | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana-Apollo` voz.  |
| `hi-in-kalpana`                             | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `hr-hr-matej`                               | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `hu-hu-szabolcs`                            | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `id-id-andika`                              | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `it-it-cosimo-apollo`                       | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `it-it-luciarus`                            | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `ja-jp-ayumi-apollo`                        | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `ja-jp-harukarus`                           | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `ja-jp-ichiro-apollo`                       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `ko-kr-heamirus`                            | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `ms-my-rizwan`                              | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `nb-no-huldarus`                            | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `nl-nl-hannarus`                            | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `pl-pl-paulinarus`                          | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `pt-br-daniel-apollo`                       | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `pt-br-heloisarus`                          | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `pt-pt-heliarus`                            | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `ro-ro-andrei`                              | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `ru-ru-ekaterinarus`                        | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `ru-ru-irina-apollo`                        | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `ru-ru-pavel-apollo`                        | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `sk-sk-filip`                               | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `sl-si-lado`                                | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `sv-se-hedvigrus`                           | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `ta-in-valluvar`                            | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `te-in-chitra`                              | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `th-th-pattara`                             | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `tr-tr-sedarus`                             | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `vi-vn-an`                                  | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `zh-cn-huihuirus`                           | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `zh-cn-kangkang-apollo`                     | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `zh-cn-yaoyao-apollo`                       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `zh-hk-danny-apollo`                        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `zh-hk-tracy-apollo`                        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `zh-hk-tracyrus`                            | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `zh-tw-hanhanrus`                           | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `zh-tw-yating-apollo`                       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `zh-tw-zhiwei-apollo`                       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de versão para `1.7.0-amd64-<locale-and-voice>` :

**Recurso**
* Componentes atualizados para o .NET 3,1

| Marcas de imagem                                  | Observações                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | primeira versão GA. Substitua `<locale>` por uma das localidades disponíveis, listadas abaixo. Por exemplo, `1.7.0-amd64-en-us-ariarus`.  |


| Localidades para v 1.7.0                          | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Imagem de contêiner com a `ar-EG` localidade e a `ar-EG-Hoda` voz.            |
| `ar-sa-naayf`                               | Imagem de contêiner com a `ar-SA` localidade e a `ar-SA-Naayf` voz.           |
| `bg-bg-ivan`                                | Imagem de contêiner com a `bg-BG` localidade e a `bg-BG-Ivan` voz.            |
| `ca-es-herenarus`                           | Imagem de contêiner com a `ca-ES` localidade e a `ca-ES-HerenaRUS` voz.       |
| `cs-cz-jakub`                               | Imagem de contêiner com a `cs-CZ` localidade e a `cs-CZ-Jakub` voz.           |
| `da-dk-hellerus`                            | Imagem de contêiner com a `da-DK` localidade e a `da-DK-HelleRUS` voz.        |
| `de-at-michael`                             | Imagem de contêiner com a `de-AT` localidade e a `de-AT-Michael` voz.         |
| `de-ch-karsten`                             | Imagem de contêiner com a `de-CH` localidade e a `de-CH-Karsten` voz.         |
| `de-de-hedda`                               | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `de-de-heddarus`                            | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Hedda` voz.           |
| `de-de-stefan-apollo`                       | Imagem de contêiner com a `de-DE` localidade e a `de-DE-Stefan-Apollo` voz.   |
| `el-gr-stefanos`                            | Imagem de contêiner com a `el-GR` localidade e a `el-GR-Stefanos` voz.        |
| `en-au-catherine`                           | Imagem de contêiner com a `en-AU` localidade e a `en-AU-Catherine` voz.       |
| `en-au-hayleyrus`                           | Imagem de contêiner com a `en-AU` localidade e a `en-AU-HayleyRUS` voz.       |
| `en-ca-heatherrus`                          | Imagem de contêiner com a `en-CA` localidade e a `en-CA-HeatherRUS` voz.      |
| `en-ca-linda`                               | Imagem de contêiner com a `en-CA` localidade e a `en-CA-Linda` voz.           |
| `en-gb-george-apollo`                       | Imagem de contêiner com a `en-GB` localidade e a `en-GB-George-Apollo` voz.   |
| `en-gb-hazelrus`                            | Imagem de contêiner com a `en-GB` localidade e a `en-GB-HazelRUS` voz.        |
| `en-gb-susan-apollo`                        | Imagem de contêiner com a `en-GB` localidade e a `en-GB-Susan-Apollo` voz.    |
| `en-ie-sean`                                | Imagem de contêiner com a `en-IE` localidade e a `en-IE-Sean` voz.            |
| `en-in-heera-apollo`                        | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Heera-Apollo` voz.    |
| `en-in-priyarus`                            | Imagem de contêiner com a `en-IN` localidade e a `en-IN-PriyaRUS` voz.        |
| `en-in-ravi-apollo`                         | Imagem de contêiner com a `en-IN` localidade e a `en-IN-Ravi-Apollo` voz.     |
| `en-us-benjaminrus`                         | Imagem de contêiner com a `en-US` localidade e a `en-US-BenjaminRUS` voz.     |
| `en-us-guy24krus`                           | Imagem de contêiner com a `en-US` localidade e a `en-US-Guy24kRUS` voz.       |
| `en-us-aria24krus`                          | Imagem de contêiner com a `en-US` localidade e a `en-US-Aria24kRUS` voz.      |
| `en-us-ariarus`                             | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaRUS` voz.         |
| `en-us-zirarus`                             | Imagem de contêiner com a `en-US` localidade e a `en-US-ZiraRUS` voz.         |
| `es-es-helenarus`                           | Imagem de contêiner com a `es-ES` localidade e a `es-ES-HelenaRUS` voz.       |
| `es-es-laura-apollo`                        | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Laura-Apollo` voz.    |
| `es-es-pablo-apollo`                        | Imagem de contêiner com a `es-ES` localidade e a `es-ES-Pablo-Apollo` voz.    |
| `es-mx-hildarus`                            | Imagem de contêiner com a `es-MX` localidade e a `es-MX-HildaRUS` voz.        |
| `es-mx-raul-apollo`                         | Imagem de contêiner com a `es-MX` localidade e a `es-MX-Raul-Apollo` voz.     |
| `fi-fi-heidirus`                            | Imagem de contêiner com a `fi-FI` localidade e a `fi-FI-HeidiRUS` voz.        |
| `fr-ca-caroline`                            | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-Caroline` voz.        |
| `fr-ca-harmonierus`                         | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-HarmonieRUS` voz.     |
| `fr-ch-guillaume`                           | Imagem de contêiner com a `fr-CH` localidade e a `fr-CH-Guillaume` voz.       |
| `fr-fr-hortenserus`                         | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-HortenseRUS` voz.     |
| `fr-fr-julie-apollo`                        | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Julie-Apollo` voz.    |
| `fr-fr-paul-apollo`                         | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-Paul-Apollo` voz.     |
| `he-il-asaf`                                | Imagem de contêiner com a `he-IL` localidade e a `he-IL-Asaf` voz.            |
| `hi-in-hemant`                              | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Hemant` voz.          |
| `hi-in-kalpana-apollo`                      | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana-Apollo` voz.  |
| `hi-in-kalpana`                             | Imagem de contêiner com a `hi-IN` localidade e a `hi-IN-Kalpana` voz.         |
| `hr-hr-matej`                               | Imagem de contêiner com a `hr-HR` localidade e a `hr-HR-Matej` voz.           |
| `hu-hu-szabolcs`                            | Imagem de contêiner com a `hu-HU` localidade e a `hu-HU-Szabolcs` voz.        |
| `id-id-andika`                              | Imagem de contêiner com a `id-ID` localidade e a `id-ID-Andika` voz.          |
| `it-it-cosimo-apollo`                       | Imagem de contêiner com a `it-IT` localidade e a `it-IT-Cosimo-Apollo` voz.   |
| `it-it-luciarus`                            | Imagem de contêiner com a `it-IT` localidade e a `it-IT-LuciaRUS` voz.        |
| `ja-jp-ayumi-apollo`                        | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ayumi-Apollo` voz.    |
| `ja-jp-harukarus`                           | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-HarukaRUS` voz.       |
| `ja-jp-ichiro-apollo`                       | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-Ichiro-Apollo` voz.   |
| `ko-kr-heamirus`                            | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-HeamiRUS` voz.        |
| `ms-my-rizwan`                              | Imagem de contêiner com a `ms-MY` localidade e a `ms-MY-Rizwan` voz.          |
| `nb-no-huldarus`                            | Imagem de contêiner com a `nb-NO` localidade e a `nb-NO-HuldaRUS` voz.        |
| `nl-nl-hannarus`                            | Imagem de contêiner com a `nl-NL` localidade e a `nl-NL-HannaRUS` voz.        |
| `pl-pl-paulinarus`                          | Imagem de contêiner com a `pl-PL` localidade e a `pl-PL-PaulinaRUS` voz.      |
| `pt-br-daniel-apollo`                       | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-Daniel-Apollo` voz.   |
| `pt-br-heloisarus`                          | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-HeloisaRUS` voz.      |
| `pt-pt-heliarus`                            | Imagem de contêiner com a `pt-PT` localidade e a `pt-PT-HeliaRUS` voz.        |
| `ro-ro-andrei`                              | Imagem de contêiner com a `ro-RO` localidade e a `ro-RO-Andrei` voz.          |
| `ru-ru-ekaterinarus`                        | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-EkaterinaRUS` voz.    |
| `ru-ru-irina-apollo`                        | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Irina-Apollo` voz.    |
| `ru-ru-pavel-apollo`                        | Imagem de contêiner com a `ru-RU` localidade e a `ru-RU-Pavel-Apollo` voz.    |
| `sk-sk-filip`                               | Imagem de contêiner com a `sk-SK` localidade e a `sk-SK-Filip` voz.           |
| `sl-si-lado`                                | Imagem de contêiner com a `sl-SI` localidade e a `sl-SI-Lado` voz.            |
| `sv-se-hedvigrus`                           | Imagem de contêiner com a `sv-SE` localidade e a `sv-SE-HedvigRUS` voz.       |
| `ta-in-valluvar`                            | Imagem de contêiner com a `ta-IN` localidade e a `ta-IN-Valluvar` voz.        |
| `te-in-chitra`                              | Imagem de contêiner com a `te-IN` localidade e a `te-IN-Chitra` voz.          |
| `th-th-pattara`                             | Imagem de contêiner com a `th-TH` localidade e a `th-TH-Pattara` voz.         |
| `tr-tr-sedarus`                             | Imagem de contêiner com a `tr-TR` localidade e a `tr-TR-SedaRUS` voz.         |
| `vi-vn-an`                                  | Imagem de contêiner com a `vi-VN` localidade e a `vi-VN-An` voz.              |
| `zh-cn-huihuirus`                           | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-HuihuiRUS` voz.       |
| `zh-cn-kangkang-apollo`                     | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Kangkang-Apollo` voz. |
| `zh-cn-yaoyao-apollo`                       | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-Yaoyao-Apollo` voz.   |
| `zh-hk-danny-apollo`                        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Danny-Apollo` voz.    |
| `zh-hk-tracy-apollo`                        | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-Tracy-Apollo` voz.    |
| `zh-hk-tracyrus`                            | Imagem de contêiner com a `zh-HK` localidade e a `zh-HK-TracyRUS` voz.        |
| `zh-tw-hanhanrus`                           | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-HanHanRUS` voz.       |
| `zh-tw-yating-apollo`                       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Yating-Apollo` voz.   |
| `zh-tw-zhiwei-apollo`                       | Imagem de contêiner com a `zh-TW` localidade e a `zh-TW-Zhiwei-Apollo` voz.   |

---

## <a name="neural-text-to-speech"></a>Texto em fala neural

A imagem de contêiner de [texto em fala neural][sp-ntts] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/speechservices/` repositório e é nomeado `neural-text-to-speech` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Última versão](#tab/current)

Notas de versão para `v1.3.0` :
* O contêiner de texto em fala neural agora está disponível para o público geral. 

| Marcas de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `1.3.0-amd64-<locale-and-voice>`    | Substitua `<locale>` por uma das localidades disponíveis, listadas abaixo. Por exemplo, `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0 localidades e vozes           | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Imagem de contêiner com a `de-DE` localidade e a `de-DE-KatjaNeural` voz.     |
| `en-au-natashaneural`               | Imagem de contêiner com a `en-AU` localidade e a `en-AU-NatashaNeural` voz.   |
| `en-ca-claraneural`                 | Imagem de contêiner com a `en-CA` localidade e a `en-CA-ClaraNeural` voz.     |
| `en-gb-libbyneural`                 | Imagem de contêiner com a `en-GB` localidade e a `en-GB-LibbyNeural` voz.     |
| `en-gb-mianeural`                   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-MiaNeural` voz.       |
| `en-us-arianeural`                  | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `en-us-guyneural`                   | Imagem de contêiner com a `en-US` localidade e a `en-US-GuyNeural` voz.       |
| `es-es-elviraneural`                | Imagem de contêiner com a `es-ES` localidade e a `es-ES-ElviraNeural` voz.    |
| `es-mx-dalianeural`                 | Imagem de contêiner com a `es-MX` localidade e a `es-MX-DaliaNeural` voz.     |
| `fr-ca-sylvieneural`                | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-SylvieNeural` voz.    |
| `fr-fr-deniseneural`                | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-DeniseNeural` voz.    |
| `it-it-elsaneural`                  | Imagem de contêiner com a `it-IT` localidade e a `it-IT-ElsaNeural` voz.      |
| `ja-jp-nanamineural`                | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-NanamiNeural` voz.    |
| `ko-kr-sunhineural`                 | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-SunHiNeural` voz.     |
| `pt-br-franciscaneural`             | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-FranciscaNeural` voz. |
| `zh-cn-xiaoxiaoneural`              | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-XiaoxiaoNeural` voz.  |

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

| Marcas de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Substitua `<locale>` por uma das localidades disponíveis, listadas abaixo. Por exemplo, `1.2.0-amd64-en-us-arianeural-preview`. |


| 1.2.0 e as localidades da visualização v           | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `de-de-katjaneural-preview`                 | Imagem de contêiner com a `de-DE` localidade e a `de-DE-KatjaNeural` voz.     |
| `en-au-natashaneural-preview`               | Imagem de contêiner com a `en-AU` localidade e a `en-AU-NatashaNeural` voz.   |
| `en-ca-claraneural-preview`                 | Imagem de contêiner com a `en-CA` localidade e a `en-CA-ClaraNeural` voz.     |
| `en-gb-libbyneural-preview`                 | Imagem de contêiner com a `en-GB` localidade e a `en-GB-LibbyNeural` voz.     |
| `en-gb-mianeural-preview`                   | Imagem de contêiner com a `en-GB` localidade e a `en-GB-MiaNeural` voz.       |
| `en-us-arianeural-preview`                  | Imagem de contêiner com a `en-US` localidade e a `en-US-AriaNeural` voz.      |
| `en-us-guyneural-preview`                   | Imagem de contêiner com a `en-US` localidade e a `en-US-GuyNeural` voz.       |
| `es-es-elviraneural-preview`                | Imagem de contêiner com a `es-ES` localidade e a `es-ES-ElviraNeural` voz.    |
| `es-mx-dalianeural-preview`                 | Imagem de contêiner com a `es-MX` localidade e a `es-MX-DaliaNeural` voz.     |
| `fr-ca-sylvieneural-preview`                | Imagem de contêiner com a `fr-CA` localidade e a `fr-CA-SylvieNeural` voz.    |
| `fr-fr-deniseneural-preview`                | Imagem de contêiner com a `fr-FR` localidade e a `fr-FR-DeniseNeural` voz.    |
| `it-it-elsaneural-preview`                  | Imagem de contêiner com a `it-IT` localidade e a `it-IT-ElsaNeural` voz.      |
| `ja-jp-nanamineural-preview`                | Imagem de contêiner com a `ja-JP` localidade e a `ja-JP-NanamiNeural` voz.    |
| `ko-kr-sunhineural-preview`                 | Imagem de contêiner com a `ko-KR` localidade e a `ko-KR-SunHiNeural` voz.     |
| `pt-br-franciscaneural-preview`             | Imagem de contêiner com a `pt-BR` localidade e a `pt-BR-FranciscaNeural` voz. |
| `zh-cn-xiaoxiaoneural-preview`              | Imagem de contêiner com a `zh-CN` localidade e a `zh-CN-XiaoxiaoNeural` voz.  |

---

## <a name="speech-language-detection"></a>Detecção de idioma de fala

A imagem de contêiner de [detecção de idioma de fala][sp-lid] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/speechservices/` repositório e é nomeado `language-detection` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Marcas de imagem                                  | Observações                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extração de Frases-Chave

a imagem de contêiner pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/textanalytics/` repositório e é nomeado `keyphrase` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Última versão](#tab/current)


| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Detecção de idioma do texto

A imagem de contêiner [detecção de idioma][ta-la] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/textanalytics/` repositório e é nomeado `language` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Versões mais recentes](#tab/current)

| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)


| Marcas de imagem                    | Observações |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Análise de sentimento

A imagem de contêiner [análise de sentimento][ta-se] pode ser encontrada na `mcr.microsoft.com` agregação do registro de contêiner. Ele reside no `azure-cognitive-services/textanalytics/` repositório e é nomeado `sentiment` . O nome da imagem de contêiner totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Essa imagem de contêiner tem as seguintes marcas disponíveis. Você também pode encontrar uma lista completa de [marcas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

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
| `2.1`    | Análise de Sentimento v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
