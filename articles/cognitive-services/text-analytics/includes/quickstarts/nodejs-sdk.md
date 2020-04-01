---
title: 'Início Rápido: Biblioteca de clientes da Análise de Texto v3 para Node.js | Microsoft Docs'
description: Introdução à biblioteca de clientes da Análise de Texto v3 para Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: a0616a8a2cc401b6b4c42c9882c14da2f123c6df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481867"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

[Documentação de referência da v3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Código-fonte da biblioteca v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Pacote da v3 (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Amostras da v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Documentação de referência da v2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Código-fonte da biblioteca v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Pacote da v2 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Amostras da v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org/).
* Após ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Crie um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter sua chave e seu ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API de Análise de Texto. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp 

cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Instale os pacotes NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics
```

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), que contém os exemplos de código neste início rápido. 

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Instale os pacotes NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), que contém os exemplos de código neste início rápido. 

---

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.
Crie um arquivo chamado `index.js` e adicione o seguinte:

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Modelo de objeto

O cliente da Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote.

O texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` que contêm uma combinação de atributos `id`, `text` e `language`, dependendo do método usado. O atributo `text` armazena o texto a ser analisado na origem `language` e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticação de cliente](#client-authentication)
* [Análise de Sentimento](#sentiment-analysis) 
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="client-authentication"></a>Autenticação de cliente

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Crie um objeto `TextAnalyticsClient` com sua chave e seu ponto de extremidade como parâmetros.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie um novo objeto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) com `credentials` e `endpoint` como um parâmetro.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimento

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método `analyzeSentiment()` do cliente e obtenha o objeto `SentimentBatchResult` retornado. Itere na lista de resultados e imprima a ID de cada documento e o sentimento no nível do documento com pontuações de confiança. Para cada documento, o resultado contém o sentimento no nível da frase juntamente com deslocamentos, o comprimento e as pontuações de confiança.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos de dicionário, contendo os documentos que deseja analisar. Chame o método [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) do cliente e obtenha o [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) retornado. Itere na lista de resultados e imprima a ID de cada documento e a pontuação de sentimento. Uma pontuação mais perto de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Detecção de idioma

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método `detectLanguage()` do cliente e obtenha o `DetectLanguageResultCollection` retornado. Em seguida, itere pelos resultados e imprima a ID de cada documento com o respectivo idioma primário.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos de dicionários contendo os documentos. Chame o método [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) do cliente e obtenha o [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) retornado. Em seguida, itere nos resultados e imprima a ID de cada documento e o idioma.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

> [!NOTE]
> Na versão `3.0-preview`:
> * O NER inclui métodos separados para detecção de informações pessoais. 
> * A vinculação de entidade é uma solicitação separada do NER.

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método `recognizeEntities()` do cliente e obtenha o objeto `RecognizeEntitiesResult`. Itere na lista de resultados e imprima o nome, o tipo, o subtipo, o deslocamento, o comprimento e a pontuação da entidade.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="using-ner-to-detect-personal-information"></a>Como usar o NER para detectar informações pessoais

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método `recognizePiiEntities()` do cliente e obtenha o objeto `EntitiesBatchResult`. Itere na lista de resultados e imprima o nome, o tipo, o subtipo, o deslocamento, o comprimento e a pontuação da entidade.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>Vinculação de Identidade

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método `recognizeLinkedEntities()` do cliente e obtenha o objeto `RecognizeLinkedEntitiesResult`. Itere na lista de resultados e imprima o nome, a ID, a fonte de dados, a URL e as correspondências da entidade. Todo objeto na matriz `matches` conterá o deslocamento, o comprimento e a pontuação dessa correspondência.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a vinculação de entidade está incluída na resposta do NER.

Crie uma lista de objetos contendo seus documentos. Chame o método [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) do cliente e obtenha o objeto [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Itere na lista de resultados e imprima a ID de cada documento. Para cada entidade detectada, imprimi o nome da Wikipédia dela, o tipo e os subtipos (se existir), bem como os locais no texto original.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
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

---

## <a name="key-phrase-extraction"></a>Extração de frases-chave

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

Crie uma matriz de cadeias de caracteres que contenha o documento que você deseja analisar. Chame o método `extractKeyPhrases()` do cliente e obtenha o objeto `ExtractKeyPhrasesResult` retornado. Itere nos resultados e imprima a ID de cada documento e as frases-chave detectadas.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Crie uma lista de objetos contendo seus documentos. Chame o método [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) do cliente e obtenha o objeto [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) retornado. Itere nos resultados e imprima a ID de cada documento e as frases-chave detectadas.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Execute seu código com `node index.js` na janela do console.

### <a name="output"></a>Saída

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```console
node index.js
```
