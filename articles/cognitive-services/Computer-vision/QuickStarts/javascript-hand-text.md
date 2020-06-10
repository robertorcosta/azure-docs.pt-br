---
title: 'Início Rápido: Pesquisa Visual Computacional 2.0 e 2.1 – Extrair texto impresso e manuscrito – REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você extrairá um texto impresso e manuscrito de uma imagem usando a API da Pesquisa Visual Computacional com o JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7cf0d2cb27425010e1f6831bb7f75e35a670be24
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171711"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-javascript"></a>Início Rápido: Extrair um texto impresso e manuscrito usando a API REST da Pesquisa Visual Computacional e o JavaScript

Neste início rápido, você extrairá o texto impresso e manuscrito de uma imagem usando a API REST da Pesquisa Visual Computacional. Com os métodos [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) e [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750), você pode detectar um texto em uma imagem e extrair os caracteres reconhecidos em um fluxo de caracteres legível por computador. 



> [!IMPORTANT]
> O método [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) é executado de maneira assíncrona. Esse método não retorna todas as informações no corpo de uma resposta bem-sucedida. Em vez disso, o método Batch Read retorna um URI no valor do campo de cabeçalho de resposta `Operation-Location`. Em seguida, você poderá chamar esse URI, que representa a API [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) para verificar o status e retornar os resultados da chamada do método Read.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave. 


## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo


Para criar e executar o exemplo, siga estas etapas:

1. Copie o código a seguir em um editor de texto.
1. Outra opção é substituir o valor do atributo `value` para o controle `inputImage` pela URL de uma imagem diferente da qual você deseja extrair o texto.
1. Salve o código como um arquivo com uma extensão `.html`. Por exemplo, `get-text.html`.
1. Abra uma janela do navegador.
1. Quando a página da Web for exibida no navegador, preencha os parâmetros obrigatórios e escolha o botão **Ler imagem**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Text Recognition Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        let subscriptionKey = document.getElementById("key").value;
        let endpoint = document.getElementById("endpoint").value;
        if (!subscriptionKey) { throw new Error('Please enter your subscription key and endpoint.'); }
        
        var uriBase = endpoint + "/vision/v3.0/read/analyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;


        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: JSON.stringify({url: sourceImageUrl}),
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read text from image:</h1>
Enter the URL to an image of text, then click
the <strong>Read image</strong> button.
<br><br>
Endpoint: 
<input type="text" name="endpoint" id="endpoint" value="" style="width: 300px;"/>
<div style="margin: 20px;">Example: https://westus2.api.cognitive.microsoft.com</div>
Subscription Key:    
<input type="text" name="key" id="key" value="" style="width: 300px;"/>
<br><br>

Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
```



## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. A página da Web de exemplo analisa e exibe uma resposta bem-sucedida na janela do navegador, semelhante ao exemplo a seguir:


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              },
              {
                "boundingBox": [
                  540,
                  665,
                  926,
                  679,
                  926,
                  825,
                  541,
                  823
                ],
                "text": "quick",
                "confidence": 0.57
              },
              {
                "boundingBox": [
                  1125,
                  686,
                  1569,
                  700,
                  1569,
                  838,
                  1125,
                  828
                ],
                "text": "brown",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  1674,
                  703,
                  1966,
                  711,
                  1966,
                  851,
                  1674,
                  841
                ],
                "text": "fox",
                "confidence": 0.442
              },
              {
                "boundingBox": [
                  2083,
                  714,
                  2580,
                  725,
                  2579,
                  876,
                  2083,
                  855
                ],
                "text": "jumps",
                "confidence": 0.878
              }
            ]
          },
          {
            "boundingBox": [
              187,
              1062,
              485,
              1056,
              486,
              1120,
              189,
              1126
            ],
            "text": "over",
            "words": [
              {
                "boundingBox": [
                  190,
                  1064,
                  439,
                  1059,
                  441,
                  1122,
                  192,
                  1126
                ],
                "text": "over",
                "confidence": 0.37
              }
            ]
          },
          {
            "boundingBox": [
              664,
              1008,
              1973,
              1023,
              1969,
              1178,
              664,
              1154
            ],
            "text": "the lazy dog!",
            "words": [
              {
                "boundingBox": [
                  668,
                  1008,
                  923,
                  1015,
                  923,
                  1146,
                  669,
                  1117
                ],
                "text": "the",
                "confidence": 0.909
              },
              {
                "boundingBox": [
                  1107,
                  1018,
                  1447,
                  1023,
                  1445,
                  1178,
                  1107,
                  1162
                ],
                "text": "lazy",
                "confidence": 0.853
              },
              {
                "boundingBox": [
                  1639,
                  1024,
                  1974,
                  1023,
                  1971,
                  1170,
                  1636,
                  1178
                ],
                "text": "dog!",
                "confidence": 0.41
              }
            ]
          }
        ]
      }
    ]
  }
}
```


## <a name="next-steps"></a>Próximas etapas

Explore um aplicativo JavaScript que usa a Pesquisa Visual Computacional para executar OCR (reconhecimento óptico de caracteres), crie miniaturas com recorte inteligente e detecte, categorize, marque e descreva recursos visuais em imagens. 

> [!div class="nextstepaction"]
> [Tutorial de JavaScript da API de Pesquisa Visual Computacional](../Tutorials/javascript-tutorial.md)

* Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
