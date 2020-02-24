---
title: 'Início Rápido: Biblioteca de clientes da Análise de Texto v3 para Java | Microsoft Docs'
description: Introdução à biblioteca de clientes da Análise de Texto v3 para Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/11/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: eeaef4a9970609c43c03cd784436796a6f8af174
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211433"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Pacote (Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) | [Exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (Java Development Kit) com a versão 8 ou superior


[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-maven-project"></a>Criar um novo projeto Maven

Adicione a dependência de análise de texto a seguir ao seu projeto. Esta versão da dependência usa a versão `3.0-preview` da API de Análise de Texto. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
</dependencies>
```

Crie um arquivo Java no seguinte diretório: `\src\main\java`.

Abra o arquivo java e adicione as seguintes instruções `import`:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

No arquivo Java, adicione uma nova classe, bem como a chave e o ponto de extremidade do seu recurso do Azure conforme mostrado abaixo.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adicione o seguinte método principal à classe. Você definirá os métodos chamados aqui mais adiante.

```java
public static void main(String[] args) {

    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Modelo de objeto

O cliente de Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave e fornece funções para aceitar texto como cadeias de caracteres únicas ou como um lote. Você pode enviar texto para a API de forma síncrona ou assíncrona. O objeto de resposta conterá as informações de análise para cada documento enviado. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimento](#sentiment-analysis) 
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner) 
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método para instanciar o objeto `TextAnalyticsClient` com o `KEY` E o `ENDPOINT` criados acima.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

No método `main()` do programa, chame o método de autenticação para criar uma instância do cliente.

## <a name="sentiment-analysis"></a>Análise de sentimento

Crie uma função chamada `sentimentAnalysisExample()` que use o cliente que você criou anteriormente e chame sua função `analyzeSentiment()`. O objeto `AnalyzeSentimentResult` retornado conterá `documentSentiment` e `sentenceSentiments` se for bem-sucedido ou um `errorMessage` se não. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
        System.out.printf(
            "Recognized document sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
            documentSentiment.getSentiment(),
            documentSentiment.getSentimentScores().getPositive(),
            documentSentiment.getSentimentScores().getNeutral(),
            documentSentiment.getSentimentScores().getNegative());

        for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
            System.out.printf(
                "Recognized sentence sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
                sentenceSentiment.getSentiment(),
                sentenceSentiment.getSentimentScores().getPositive(),
                sentenceSentiment.getSentimentScores().getNeutral(),
                sentenceSentiment.getSentimentScores().getNegative());
        }
}
```

### <a name="output"></a>Saída

```console
Recognized document sentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized sentence sentiment: positive, positive score: 1.00, neutral score: 0.00, negative score: 0.00.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```
## <a name="language-detection"></a>Detecção de idioma

Crie uma função chamada `detectLanguageExample()` que use o cliente que você criou anteriormente e chame sua função `detectLanguage()`. O objeto `DetectLanguageResult` retornado conterá um idioma primário detectado, uma lista de outros idiomas detectados se for bem-sucedido ou um `errorMessage` se não.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão; para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `countryHint = ""`. Para definir um padrão diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Saída

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>NER (reconhecimento de entidade nomeada)

> [!NOTE]
> Na versão `3.0-preview`:
> * O NER inclui métodos separados para detecção de informações pessoais. 
> * A vinculação de entidade é uma solicitação separada do NER.

Crie uma função chamada `recognizeEntitiesExample()` que use o cliente que você criou anteriormente e chame sua função `recognizeEntities()`. O objeto `RecognizeEntitiesResult` retornado conterá uma lista de `NamedEntity` se for bem-sucedido ou um `errorMessage` se não.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, offset: 26, length: 7, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, offset: 34, length: 9, score: 0.80.
```

## <a name="using-ner-to-recognize-personal-information"></a>Como usar o NER para reconhecer informações pessoais

Crie uma função chamada `recognizePIIEntitiesExample()` que use o cliente que você criou anteriormente e chame sua função `recognizePiiEntities()`. O objeto `RecognizePiiEntitiesResult` retornado conterá uma lista de `NamedEntity` se for bem-sucedido ou um `errorMessage` se não. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), entity sub-category: N/A, offset: 33, length: 11, score: 0.85.
```

## <a name="entity-linking"></a>Vinculação de entidade

Crie uma função chamada `recognizeLinkedEntitiesExample()` que use o cliente que você criou anteriormente e chame sua função `recognizeLinkedEntities()`. O objeto `RecognizeLinkedEntitiesResult` retornado conterá uma lista de `LinkedEntity` se for bem-sucedido ou um `errorMessage` se não. Como as entidades vinculadas são unicamente identificadas, as ocorrências da mesma entidade são agrupadas em um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getOffset(),
                    linkedEntityMatch.getLength(),
                    linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.78.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.55.
Text: Gates, Offset: 5, Length: 161, Score: 0.55.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.53.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.47.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.47.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Offset: 7, Length: 54, Score: 0.25.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.28.
```
## <a name="key-phrase-extraction"></a>Extração de frases-chave

Crie uma função chamada `extractKeyPhrasesExample()` que use o cliente que você criou anteriormente e chame sua função `extractKeyPhrases()`. O objeto `ExtractKeyPhraseResult` retornado conterá uma lista de frases-chave se for bem-sucedido ou um `errorMessage` se não.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Saída

```console
Recognized phrases: 
cat
veterinarian
```
