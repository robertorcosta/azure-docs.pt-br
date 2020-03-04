---
title: 'Início Rápido: Biblioteca de cliente Análise de Texto para Go| Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, detecte o idioma usando a biblioteca de clientes da Análise de Texto para o Go nos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: aahi
ms.openlocfilehash: 5623be028cfe10c0ea6786b24bf75ae2bf9c5255
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560772"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Início Rápido: Usar a biblioteca de clientes da Análise de Texto para o Go

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pacote (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Este início rápido só se aplica à Análise de Texto versão 2.1. Atualmente, uma biblioteca de clientes v3 para o Go não está disponível.

## <a name="prerequisites"></a>Prerequisites

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão mais recente do [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um recurso de Análise de Texto do Azure 

[!INCLUDE [text-analytics-resource-creation](../includes/quickstarts/resource-creation.md)]

### <a name="create-a-new-go-project"></a>Criar um novo projeto do Go

Em uma janela de console (cmd, PowerShell, Terminal, Bash), crie um novo workspace para seu projeto do Go e navegue até ele. Seu workspace conterá três pastas: 

* **src** – esse diretório contém o código-fonte e os pacotes. Todos os pacotes instalados com o comando `go get` residirão aqui.
* **pkg** – esse diretório contém os objetos de pacote go compilados. Todos esses arquivos têm uma extensão `.a`.
* **bin** – esse diretório contém os arquivos executáveis binários que são criados quando você executa o `go install`.

> [!TIP]
> Saiba mais sobre a estrutura de um [workspace do Go](https://golang.org/doc/code.html#Workspaces). Este guia inclui informações para configurar `$GOPATH` e `$GOROOT`.

Crie um workspace chamado `my-app` e os subdiretórios necessários para `src`, `pkg` e `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Instalar a biblioteca de clientes da Análise de Texto para Go

Instalar a biblioteca de clientes para Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

Ou se você usar o DEP, no repositório, execute:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Criar seu aplicativo Go

Em seguida, crie um arquivo chamado `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Abra `quickstart.go` em seu IDE ou editor de texto favorito. Em seguida, adicione o nome do pacote e importe as seguintes bibliotecas:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Modelo de objeto 

O cliente Análise de Texto é um objeto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) que se autentica no Azure usando sua chave. O cliente fornece vários métodos para analisar o texto, como uma única cadeia de caracteres ou um lote. 

O texto é enviado para a API como uma lista de `documents`, que são objetos `dictionary` que contêm uma combinação de atributos `id`, `text` e `language`, dependendo do método usado. O atributo `text` armazena o texto a ser analisado na origem `language` e o `id` pode ser qualquer valor. 

O objeto de resposta é uma lista que contém as informações de análise de cada documento. 

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar o seguinte com a biblioteca de clientes de Análise de Texto para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimento](#sentiment-analysis)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade](#entity-recognition)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente


Em uma nova função, crie variáveis para a chave de assinatura e o Ponto de Extremidade do Azure do recurso.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Crie um objeto [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New). Passe sua chave para a função [NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), que será passada para a propriedade do `authorizer` cliente.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Análise de sentimento

Crie uma função chamada `SentimentAnalysis()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `Language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor. 

Chame a função [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a pontuação de sentimento. Uma pontuação mais perto de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Chame `SentimentAnalysis()` no projeto.

### <a name="output"></a>Saída

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Detecção de idioma

Crie uma função chamada `LanguageDetection()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) contendo os documentos que você deseja analisar. Cada objeto conterá um `id` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, e `id` pode ser qualquer valor. 

Chame [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e o idioma detectado.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Chame `LanguageDetection()` no projeto.

### <a name="output"></a>Saída

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidade

Crie uma função chamada `ExtractEntities()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor. 

Chame [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a pontuação das entidades extraídas.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Chame `ExtractEntities()` no projeto.

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

## <a name="key-phrase-extraction"></a>Extração de frases-chave

Crie uma função chamada `ExtractKeyPhrases()` e crie um cliente usando o método `GetTextAnalyticsClient()` criado anteriormente. Crie uma lista de objetos [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) contendo os documentos que você deseja analisar. Cada objeto conterá um `id`, `language` e um atributo `text`. O atributo `text` armazena o texto a ser analisado, `language` é o idioma do documento e `id` pode ser qualquer valor.

Chame [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) do cliente e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e quaisquer frases-chave extraídas.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Chame `ExtractKeyPhrases()` no projeto.

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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
