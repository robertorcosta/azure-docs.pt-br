---
title: 'Início Rápido: Gerar uma miniatura – REST, Go'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você gerará uma miniatura de uma imagem usando a API da Pesquisa Visual Computacional com Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7e9980bfe3065b4ce0df5a2f083f5f4bef366f35
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681097"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>Início Rápido: Gerar uma miniatura usando a API REST da Pesquisa Visual Computacional com o Go

Neste início rápido, você vai gerar uma miniatura de uma imagem usando a API REST da Pesquisa Visual Computacional. Você especifica a altura e largura, que podem diferir da imagem de entrada quanto à taxa de proporção. A Pesquisa Visual Computacional usa o corte inteligente para identificar a área de interesse de modo inteligente e gerar as coordenadas de corte com base nessa região.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter o [Go](https://golang.org/dl/) instalado.
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a cadeia de caracteres do ponto de extremidade de serviço, denominadas `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Copie o código a seguir em um editor de texto.
1. Outra opção é substituir o valor de `imageUrl` pela URL de uma imagem diferente com base na qual você deseja gerar uma miniatura.
1. Salve o código como um arquivo com uma extensão `.go`. Por exemplo, `get-thumbnail.go`.
1. Abra una janela de prompt de comando.
1. No prompt, execute o comando `go build` para compilar o pacote do arquivo. Por exemplo, `go build get-thumbnail.go`.
1. No prompt, execute o pacote compilado. Por exemplo, `get-thumbnail`.

```go
package main

import (
    "bytes"
    "fmt"
    "io/ioutil"
    "io"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")

    uriBase := endpoint + "vision/v3.0/generateThumbnail"
    const imageUrl = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    uri := uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    
    // Convert byte[] to io.Reader type
    readerThumb := bytes.NewReader(data)

    // Write the image binary to file
    file, err := os.Create("thumb_local.png")
    if err != nil { log.Fatal(err) }
    defer file.Close()
    _, err = io.Copy(file, readerThumb)
    if err != nil { log.Fatal(err) }

    fmt.Println("The thunbnail from local has been saved to file.")
    fmt.Println()
}
```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida contém os dados binários da imagem em miniatura. Se a solicitação falhar, a resposta conterá um código de erro e uma mensagem para ajudar a determinar o que deu errado.

## <a name="next-steps"></a>Próximas etapas

Explore a API da Pesquisa Visual Computacional para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
