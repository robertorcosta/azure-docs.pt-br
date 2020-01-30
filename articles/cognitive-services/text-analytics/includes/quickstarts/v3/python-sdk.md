---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/23/2019
ms.author: aahi
ms.openlocfilehash: b71c66c9025ea76d9f99f27537c0d4239ce93fdc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717214"
---
<a name="HOLTop"></a>

[Documentação de referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Pacote (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * Este início rápido usa a versão `3.0-preview` da biblioteca de clientes de Análise de Texto, que inclui uma versão prévia pública para uma [Análise de Sentimento](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e um [NER (Reconhecimento de Entidade Nomeada)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) aprimorados.
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Para cenários de produção, recomendamos usar os métodos assíncronos em lote para desempenho e escalabilidade. Por exemplo, importar o cliente do namespace `azure.ai.textanalytics.aio` e chamar `analyze_sentiment()`, em vez de `analyze_sentiment()` do namespace `azure.ai.textanalytics`.

## <a name="prerequisites"></a>Prerequisites

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um recurso de Análise de Texto do Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um arquivo Python e variáveis para a chave de assinatura e o Ponto de Extremidade do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modelo de objeto

O cliente da Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto como um lote. Este início rápido usa uma coleção de funções para enviar rapidamente documentos individuais.

Durante o processamento em lotes, o texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` que contêm uma combinação de atributos `id`, `text` e `language`, dependendo do método usado. O atributo `text` armazena o texto a ser analisado na origem `language` e o `id` pode ser qualquer valor. Ao processar documentos individuais, apenas uma entrada `text` é necessária, como pode ser visto nos exemplos abaixo.  

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de Análise de Texto para Python:

* [Análise de Sentimento](#sentiment-analysis) (versão prévia pública)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-public-preview) (versão prévia pública)
* [Reconhecimento de entidade nomeada – informações pessoais](#named-entity-recognition---personal-information-public-preview) (versão prévia pública)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Análise de sentimento

> [!NOTE]
> O código abaixo é para análise de sentimento v3, que está em versão prévia pública.

Crie uma função chamada `sentiment_analysis_example()` que usa o ponto de extremidade e a chave como argumentos e chama a função `single_analyze_sentiment()`. O objeto de resposta retornado conterá o rótulo e a pontuação de sentimento de todo o documento de entrada, assim como uma análise de sentimento para cada frase.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, credential=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Saída

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Detecção de idioma

Crie uma função chamada `language_detection_example()` que usa o ponto de extremidade e a chave como argumentos e chama a função `single_detect_languages()`. O objeto de resposta retornado conterá o idioma detectado em `detected_languages` se for bem-sucedido e um `error` se não.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `country_hint` para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão; para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `country_hint : ""`. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, credential=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Saída

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Reconhecimento de entidade nomeada (versão prévia pública)

> [!NOTE]
> O código abaixo é para o Reconhecimento de Entidade Nomeada v3, que está na versão prévia pública.

Crie uma função chamada `entity_recognition_example` que usa o ponto de extremidade e a chave como argumentos e chama a função `single_recognize_entities()` e itera por meio dos resultados. O objeto de resposta retornado conterá a lista de entidades detectadas em `entity` se for bem-sucedido e um `error` se não. Para cada entidade detectada, imprima seu Tipo e seu Subtipo, se houver.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, credential=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Saída

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Reconhecimento de entidade nomeada – informações pessoais (versão prévia pública)

> [!NOTE]
> O código abaixo é para detectar informações pessoais usando o Reconhecimento de Entidade Nomeada v3, que está em versão prévia pública.

Crie uma função chamada `entity_pii_example()` que usa o ponto de extremidade e a chave como argumentos e chama a função `single_recognize_pii_entities()` e obtém o resultado. Em seguida, itere os resultados e imprima as entidades.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, credential=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Vinculação de Identidade

Crie uma função chamada `entity_linking_example()` que usa o ponto de extremidade e a chave como argumentos e chama a função `single_recognize_linked_entities()` e itera por meio dos resultados. O objeto de resposta retornado conterá a lista de entidades detectadas em `entities` se for bem-sucedido e um `error` se não. Como as entidades vinculadas são unicamente identificadas, as ocorrências da mesma entidade são agrupadas em um objeto `entity` como uma lista de objetos `match`.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, credential=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Saída

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>Extração de frases-chave

Crie uma função chamada `key_phrase_extraction_example()` que usa o ponto de extremidade e a chave como argumentos e chama a função `single_extract_key_phrases()`. O resultado conterá a lista de frases-chave detectadas em `key_phrases` se for bem sucedido e um `error` se não. Imprima todas as frases-chave detectadas.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, credential=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Saída

```console
    Key Phrases:
         cat
         veterinarian
```
