---
title: 'Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o Java'
titleSuffix: Azure Cognitive Services
description: Use a API de Reconhecimento de Tinta Digital para começar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: e8cd6a4acbd1492bba1c9e88b523a7c44a44f009
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996854"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o Java

Use este início rápido para começar a usar a API de Reconhecimento de Tinta Digital em traços de tinta digital. Esse aplicativo Java envia uma solicitação de API que contém dados de traço de tinta formatados em JSON e obtém a resposta.

Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamará a API em um aplicativo de escrita à tinta digital. Este início rápido envia dados de traço de tinta para a amostra manuscrita a seguir de um arquivo JSON.

![uma imagem de um texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Pré-requisitos

- O JDK ([Java&trade; Development Kit) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Importar essas bibliotecas do repositório do Maven
    - Pacote [JSON em Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacote [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Os dados de traço de tinta de exemplo deste início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso do Reconhecimento de Tinta Digital

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Crie um novo projeto Java em seu IDE ou editor favorito e importe as bibliotecas a seguir.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Crie variáveis para a chave de assinatura, o ponto de extremidade e o arquivo JSON. O ponto de extremidade será acrescentado posteriormente ao URI do Reconhecimento de Tinta Digital.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função chamada `sendRequest()` que use as variáveis criadas anteriormente. Em seguida, siga estas etapas.

2. Crie um objeto `CloseableHttpClient` que pode enviar solicitações à API. Envie a solicitação para um objeto de solicitação `HttpPut` combinando o ponto de extremidade e a URL de Reconhecimento de Tinta Digital.

3. Use a função `setHeader()` da solicitação para definir o cabeçalho `Content-Type` como `application/json` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

4. Use a função `setEntity()` da solicitação para os dados a serem enviados.   

5. Use a função `execute()` do cliente para enviar a solicitação e salve-a em um objeto `CloseableHttpResponse`. 

6. Crie um objeto `HttpEntity` para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorne-a.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

Crie um método chamado `recognizeInk()` para reconhecer os dados de traço de tinta. Chame o método `sendRequest()` criado acima com o ponto de extremidade, a URL, a chave de assinatura e os dados JSON. Obtenha o resultado e imprima-o no console.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Carregar os dados de tinta digital e enviar a solicitação

1. No método principal do aplicativo, leia o arquivo JSON que contém os dados que serão adicionados às solicitações.

2. Chame a função de reconhecimento de tinta criada acima.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Execute o aplicativo. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API do Reconhecimento de Tinta Digital funciona em um aplicativo de escrita à tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C# e UWP (Plataforma Universal do Windows)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da Web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
