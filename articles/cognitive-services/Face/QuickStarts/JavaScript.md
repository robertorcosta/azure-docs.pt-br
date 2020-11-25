---
title: 'Início Rápido: Detectar rostos em uma imagem usando a API REST e JavaScript'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você detectará faces de uma imagem usando a API de Detecção Facial com JavaScript nos Serviços Cognitivos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.custom: devx-track-js
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f302000529e0dbf7ecce69ac9bebe77af59561ee
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020833"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>Início Rápido: Detectar rostos em uma imagem usando a API REST e JavaScript

Neste início rápido, você usará a API REST de Detecção Facial do Azure com o JavaScript para detectar rostos humanos em uma imagem.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Um editor de códigos como o [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>Inicializar o arquivo HTML

Crie um arquivo HTML, *detectFaces.html*, e adicione o código a seguir.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

Em seguida, adicione o código a seguir dentro do elemento `body` do documento. Esse código configura uma interface do usuário básica com um campo de URL, um botão **Analisar rosto**, um painel de resposta e um painel de exibição de imagens.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="html_include":::

## <a name="write-the-javascript-script"></a>Escrever o script JavaScript

Adicione o código a seguir imediatamente acima do elemento `h1` no documento. Esse código configura o código JavaScript que chama a API de Detecção Facial.

:::code language="html" source="~/cognitive-services-quickstart-code/javascript/web/face/rest/detect.html" id="script_include":::

Você precisará atualizar o campo `subscriptionKey` com o valor de sua chave de assinatura e alterar a cadeia de caracteres `uriBase` para que ela contenha a cadeia de caracteres do ponto de extremidade correto. O campo `returnFaceAttributes` especifica quais atributos faciais devem ser recuperados; talvez você queira alterar essa cadeia de caracteres, dependendo do uso pretendido.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Executar o script

Abra *detectFaces.html* no navegador. Quando você clicar no botão **Analisar rosto**, o aplicativo deverá exibir a imagem da URL fornecida e imprimir uma cadeia de caracteres JSON dos dados faciais.

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

O texto a seguir é um exemplo de uma resposta JSON bem-sucedida.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]
```

## <a name="extract-face-attributes"></a>Extrair Atributos Faciais
 
Para extrair os atributos de face, use o modelo de detecção 1 e adicione o parâmetro de consulta `returnFaceAttributes`.

```javascript
// Request parameters.
var params = {
    "detectionModel": "detection_01",
    "returnFaceAttributes": "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise",
    "returnFaceId": "true"
};
```

A resposta agora inclui atributos de face. Por exemplo:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você escreveu um script JavaScript que chama o serviço de Detecção Facial do Azure para detectar rostos em uma imagem e retornar os atributos deles. Em seguida, explore a documentação de referência de API de Detecção Facial para saber mais.

> [!div class="nextstepaction"]
> [API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
