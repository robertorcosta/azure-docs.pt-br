---
title: 'Início Rápido: Visualização de URL do Projeto, JavaScript'
titlesuffix: Azure Cognitive Services
description: Exemplo de script para começar a usar rapidamente a API de Visualização de URL do Bing com JavaScript.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: ec8a7ceef8cd938984b8eedc8397f6f67294c990
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706923"
---
# <a name="quickstart-url-preview-in-javascript"></a>Início Rápido: URL de Visualização em JavaScript 

O seguinte aplicativo de página única usa JavaScript para criar uma Visualização de URL para o site SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Prerequisites

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Cenário do código
O exemplo a seguir de JavaScript inclui um objeto de entrada de caixa de texto onde o usuário insere a URL a ser visualizada.  Quando o usuário clica no botão de **Visualização**, o método onclick encaminha para `getPreview` onde o código gera uma solicitação da Web para o ponto de extremidade **UrlPreview**.

O código cria um *XMLHttpRequest*, adiciona a chave e o cabeçalho *Ocp-Apim-Subscription-Key* e envia a solicitação.  Ele adiciona um manipulador de eventos assíncrono para processar a resposta.

Se a resposta retorna com êxito, o manipulador atribui o texto JSON da resposta ao `demo` parágrafo na página. Outros elementos de resposta são definidos para os parágrafos seguintes para exibição.

**Resposta JSON bruta**

```
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

**A demonstração da execução**

![Exemplo de Visualização de Url do JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Executando o aplicativo

Para executar o aplicativo:

1. Substitua o valor `YOUR-SUBSCRIPTION-KEY` por uma chave de acesso válida para a assinatura.
2. Salve o HTML e o script em um arquivo com a extensão .html.
3. Execute a página da Web em um navegador.
4. Use a URL existente, ou digite outra na caixa de texto.
5. Clique na guia **Visualizar**.

**Código-fonte:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](csharp.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
