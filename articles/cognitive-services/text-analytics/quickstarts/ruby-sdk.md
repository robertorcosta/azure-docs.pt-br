---
title: 'Início Rápido: Biblioteca de clientes da Análise de Texto para o Ruby | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, detecte o idioma usando a biblioteca de clientes da Análise de Texto para o Ruby nos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 039a52c9ab0bfc460116e48086c854f4d7e8efb4
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996976"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Início Rápido: Usar a biblioteca de clientes da Análise de Texto para o Ruby

Introdução à biblioteca de clientes da Análise de Texto. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.

Use a biblioteca de clientes da Análise de Texto para executar:

* Análise de sentimento
* Detecção de idioma
* Reconhecimento de entidade
* Extração de frases-chave

> [!NOTE]
> Este início rápido só se aplica à Análise de Texto versão 2.1. Atualmente, uma biblioteca de clientes v3 para o Ruby não está disponível.

[Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [Pacote (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Ruby](https://www.ruby-lang.org/)
* Após ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Crie um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter sua chave e seu ponto de extremidade. 
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API de Análise de Texto. Você fará isso posteriormente no início rápido.
    * Você pode usar o tipo de preço gratuito para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-ruby-application"></a>Criar um novo aplicativo em Ruby

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. Em seguida crie um arquivo chamado `GemFile` e um arquivo Ruby para seu código.

```console
mkdir myapp && cd myapp
```

No seu `GemFile`, adicione as seguintes linhas para adicionar a biblioteca de clientes como uma dependência.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

No arquivo Ruby, importe os pacotes a seguir.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Modelo de objeto 

O cliente Análise de Texto se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote. 

O texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` que contêm uma combinação de atributos `id`, `text` e `language`, dependendo do método usado. O atributo `text` armazena o texto a ser analisado na origem `language` e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar o seguinte com a biblioteca de clientes de Análise de Texto para Ruby:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimento](#sentiment-analysis)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade](#entity-recognition)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma classe denominada `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Nessa classe, crie uma função chamada `initialize` para autenticar o cliente usando sua chave e ponto de extremidade. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Fora da classe, use a função `new()` do cliente para instanciá-la.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Análise de sentimento

No objeto do cliente, crie uma função chamada `AnalyzeSentiment()` que usará uma lista de documentos de entrada que serão criados posteriormente. Chame a função `sentiment()` do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a pontuação de sentimento. Uma pontuação mais perto de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Fora da função do cliente, crie uma nova função chamada `SentimentAnalysisExample()` que use o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `MultiLanguageInput` contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `Language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor. Em seguida, chame a função `AnalyzeSentiment()` do cliente.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Chame a função `SentimentAnalysisExample()`.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Detecção de idioma

No objeto do cliente, crie uma função chamada `DetectLanguage()` que usará uma lista de documentos de entrada que serão criados posteriormente. Chame a função `detect_language()` do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e o idioma detectado.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Fora da função do cliente, crie uma nova função chamada `DetectLanguageExample()` que use o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `LanguageInput` contendo os documentos que você deseja analisar. Cada objeto conterá um `id` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, e `id` pode ser qualquer valor. Em seguida, chame a função `DetectLanguage()` do cliente.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Chame a função `DetectLanguageExample()`.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Reconhecimento de entidade

No objeto do cliente, crie uma função chamada `RecognizeEntities()` que usará uma lista de documentos de entrada que serão criados posteriormente. Chame a função `entities()` do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e as entidades reconhecidas.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Fora da função do cliente, crie uma nova função chamada `RecognizeEntitiesExample()` que use o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `MultiLanguageInput` contendo os documentos que você deseja analisar. Cada objeto conterá uma `id`, um `language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor. Em seguida, chame a função `RecognizeEntities()` do cliente.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Chame a função `RecognizeEntitiesExample()`.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extração de frases-chave

No objeto do cliente, crie uma função chamada `ExtractKeyPhrases()` que usará uma lista de documentos de entrada que serão criados posteriormente. Chame a função `key_phrases()` do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e quaisquer frases-chave extraídas.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Fora da função do cliente, crie uma nova função chamada `KeyPhraseExtractionExample()` que use o objeto `TextAnalyticsClient` criado anteriormente. Crie uma lista de objetos `MultiLanguageInput` contendo os documentos que você deseja analisar. Cada objeto conterá uma `id`, um `language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor. Em seguida, chame a função `ExtractKeyPhrases()` do cliente.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Chame a função `KeyPhraseExtractionExample()`.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Saída

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
