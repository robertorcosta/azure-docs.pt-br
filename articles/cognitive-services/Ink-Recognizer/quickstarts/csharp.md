---
title: 'Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o C#'
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
ms.openlocfilehash: 0c7d3ed7e2cbaee7d30f368efa004bbb3daaafdd
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996877"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o C#

Use este início rápido para começar a enviar traços de tinta digital para a API de Reconhecimento de Tinta Digital. Este aplicativo C# envia uma solicitação de API que contém dados de traço de tinta formatados em JSON e obtém a resposta.

Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamará a API em um aplicativo de escrita à tinta digital. Este início rápido envia dados de traço de tinta para a amostra manuscrita a seguir de um arquivo JSON.

![uma imagem de um texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar o Newtonsoft.Json como um pacote NuGet no Visual Studio:
        1. Clique com o botão direito do mouse no **Gerenciador de Soluções**
        2. Clique em **Gerenciar Pacotes NuGet...**
        3. Pesquise `Newtonsoft.Json` e instale o pacote
- Se você estiver usando o Linux/macOS, esse aplicativo poderá ser executado com o [Mono](https://www.mono-project.com/).

- Os dados de traço de tinta de exemplo deste início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Criar um recurso do Reconhecimento de Tinta Digital

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. No Visual Studio, crie uma solução de console e adicione os pacotes a seguir. 
    
    [!code-csharp[imports](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Crie variáveis para a chave de assinatura, o ponto de extremidade e o arquivo JSON de exemplo. O ponto de extremidade posteriormente será combinado com `inkRecognitionUrl` para acessar a API. 

    [!code-csharp[endpoint file path and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função assíncrona chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um objeto `HttpClient`. Adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, crie um objeto `StringContent` para a solicitação.
 
3. Envie a solicitação com `PutAsync()`. Se a solicitação for bem-sucedida, retorne a resposta.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

1. Crie uma função chamada `recognizeInk()`. Construa a solicitação e envie-a chamando a função `Request()` com o ponto de extremidade, a chave de assinatura, a URL para a API e os dados de traço de tinta digital.

2. Desserialize o objeto JSON e grave-o no console. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Carregar os dados de tinta digital

Crie uma função chamada `LoadJson()` para carregar o arquivo JSON de dados de tinta. Use um `StreamReader` e um `JsonTextReader` para criar um `JObject` e retorne-o.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Enviar a solicitação de API

1. No método principal do aplicativo, carregue os dados JSON com a função criada acima. 

2. Chame a função `recognizeInk()` criada acima. Use `System.Console.ReadKey()` para manter a janela do console aberta depois de executar o aplicativo.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Execute o aplicativo. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API do Reconhecimento de Tinta Digital funciona em um aplicativo de escrita à tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C# e UWP (Plataforma Universal do Windows)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da Web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
