---
title: 'Início Rápido: executar uma pesquisa na Web com Go – API REST de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações para a API REST de Pesquisa na Web do Bing usando Go e receba uma resposta JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.reviewer: nhoyadx@gmail.com, v-gedod, erhopf
ms.custom: seodec2018
ms.openlocfilehash: 589f7884f390ae57df4e946bcd34ca3bda629ed8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978791"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-go"></a>Início Rápido: pesquisar na Web usando a API REST de Pesquisa na Web do Bing e Go

Use este Início Rápido para fazer sua primeira chamada à API de Pesquisa na Web do Bing e receber a resposta JSON. Este aplicativo Go envia uma solicitação de pesquisa à API e exibe a resposta. Embora esse aplicativo seja escrito em Go, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos
Aqui estão alguns itens de que você poderá precisar antes de executar este início rápido:

* [Binários do Go](https://golang.org/dl/)
* Uma chave de assinatura

Este início rápido requer apenas bibliotecas **principais**, não há dependências externas.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]  

## <a name="create-a-project-and-import-core-libraries"></a>Criar um projeto e importar as bibliotecas principais

Crie um novo projeto Go em seu IDE ou editor favorito. Em seguida, importe `net/http` para solicitações, `ioutil` para ler a resposta `time` e `encoding/json` para lidar com JSON e `fmt` para imprimir a saída.

```go
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)
```

## <a name="create-a-struct-to-format-the-search-results"></a>Criar um struct para formatar os resultados da pesquisa

O struct `BingAnswer` formata os dados fornecidos na resposta.

```go
// This struct formats the answers provided by the Bing Web Search API.
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}
```

## <a name="declare-the-main-function-and-define-variables"></a>Declarar a função principal e definir variáveis  

Esse código declara a função principal e define as variáveis necessárias. `endpoint` pode ser o ponto de extremidade global abaixo ou o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso. Confirme que o ponto de extremidade está correto e substitua o valor `token` por uma chave de assinatura válida da sua conta do Azure. Fique à vontade para personalizar a consulta de pesquisa substituindo o valor para `searchTerm`.

```go
// Declare the main function. This is required for all Go programs.
func main() {
// Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

// The remaining code in this quickstart goes in the main function.

}
```

## <a name="construct-a-request"></a>Construir uma solicitação

Esse código declara a solicitação HTTP, insere o cabeçalho e a payload e cria uma instância do cliente.

```go
// Declare a new GET request.
req, err := http.NewRequest("GET", endpoint, nil)
if err != nil {
    panic(err)
}

// Add the payload to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the request header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

// Instantiate a client.  
client := new(http.Client)
```

## <a name="make-a-request"></a>Fazer uma solicitação

Use este código para chamar a API de Pesquisa na Web do Bing e fechar a conexão depois que uma resposta é retornada.

```go
// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

// Close the connection.
defer resp.Body.Close()
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}
```

## <a name="handle-the-response"></a>Tratar da resposta

Lembrar a estrutura criada anteriormente? Vamos usá-la para formatar a resposta e imprimir os resultados da pesquisa.

```go
// Create a new answer.  
ans := new(BingAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
// Iterate over search results and print the result name and URL.
for _, result := range ans.WebPages.Value {
    fmt.Println(result.Name, "||", result.URL)
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é validar o seu código e executá-lo. Se você quiser comparar seu código com o nosso, aqui está o programa concluído:

```go
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)

// The is the struct for the data returned by Bing.
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}

// Verify the endpoint URI and replace the token string with a valid subscription key.  
func main() {
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // Add the payload to the request.  
    param := req.URL.Query()
    param.Add("q", searchTerm)
    req.URL.RawQuery = param.Encode()

    // Insert the request header.  
    req.Header.Add("Ocp-Apim-Subscription-Key", token)

    // Create a new client.  
    client := new(http.Client)

    // Send the request to Bing.  
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    // Close the response.
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Create a new answer.  
    ans := new(BingAnswer)
    err = json.Unmarshal(body, &ans)
    if err != nil {
         fmt.Println(err)
    }

    // Iterate over search results and print the result name and URL.
    for _, result := range ans.WebPages.Value {
         fmt.Println(result.Name, "||", result.URL)
    }

}
```

## <a name="sample-response"></a>Resposta de exemplo  

As respostas da API de Pesquisa na Web do Bing são retornadas como JSON. Essa resposta de exemplo foi formatada usando o struct `BingAnswer` e exibe o `result.Name` e a `result.URL`.

```go
Microsoft Cognitive Services || https://www.microsoft.com/cognitive-services
Cognitive Services | Microsoft Azure || https://azure.microsoft.com/services/cognitive-services/
Cognitive Service Try experience | Microsoft Azure || https://azure.microsoft.com/try/cognitive-services/
What is Microsoft Cognitive Services? | Microsoft Docs || https://docs.microsoft.com/azure/cognitive-services/Welcome
Microsoft Cognitive Toolkit || https://www.microsoft.com/en-us/cognitive-toolkit/
Microsoft Customers || https://customers.microsoft.com/en-us/search?sq=%22Microsoft%20Cognitive%20Services%22&ff=&p=0&so=story_publish_date%20desc
Microsoft Enterprise Services - Microsoft Enterprise || https://enterprise.microsoft.com/en-us/services/
Microsoft Cognitive Services || https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236
Cognitive Services - msdn.microsoft.com || https://msdn.microsoft.com/magazine/mt742868.aspx  
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única de pesquisa na Web do Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
