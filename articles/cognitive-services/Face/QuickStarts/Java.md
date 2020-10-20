---
title: 'Início Rápido: Detectar rostos em uma imagem com a API REST do Azure e Java'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você usará a API de Detecção Facial do Azure com Java para detectar faces em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: fbe62cf00422710e18a6b112adc08f19ea03177b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858346"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Início Rápido: Detectar rostos em uma imagem usando a API REST e Java

Neste início rápido, você usará a API REST de Detecção Facial do Azure com Java para detectar faces em uma imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Qualquer Java IDE de sua preferência.

## <a name="create-the-java-project"></a>Criar o projeto Java

1. Crie um aplicativo Java de linha de comando no IDE e adicione uma classe **Main** com um método **main**.
1. Importe as bibliotecas a seguir para seu projeto Java. Se você usar o Maven, as coordenadas do Maven serão fornecidas para cada biblioteca.
   - [Cliente do Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Núcleo do Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Registro em log do Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>Adicionar código de detecção facial

Abra a classe main de seu projeto. Aqui, você adicionará o código necessário para carregar imagens e detectar faces.

### <a name="import-packages"></a>Importar pacotes

Adicione as seguintes instruções `import` à parte superior do arquivo.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Substitua a classe **Main** pelo código a seguir. Esses dados especificam como se conectar ao serviço de Detecção Facial e onde obter os dados de entrada. Você precisará atualizar o campo `subscriptionKey` com o valor de sua chave de assinatura e alterar a cadeia de caracteres `uriBase` para que ela contenha a cadeia de caracteres do ponto de extremidade correto. Talvez você também queria definir o valor `imageWithFaces` para um caminho que aponte para um arquivo de imagem diferente.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

O campo `faceAttributes` é simplesmente uma lista de certos tipos de atributos. Ele especificará quais informações devem ser recuperadas sobre as faces detectadas.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Chamar a API REST de detecção facial

Adicione o método **main** pelo código a seguir. Ele constrói uma chamada REST para a API de Detecção Facial para detectar informações sobre a face na imagem remota (a cadeia de caracteres `faceAttributes` especifica quais atributos de face recuperar). Em seguida, ele grava os dados de saída em uma cadeia de caracteres JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Analisar a resposta JSON

Diretamente abaixo do código anterior, adicione o seguinte bloco, que converte os dados JSON retornados em um formato mais legível antes de imprimi-lo no console. Por fim, feche o bloco try-catch, o método **main** e a classe **Main**.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Executar o aplicativo

Compile o código e execute-o. Uma resposta bem-sucedida exibirá dados de Detecção Facial em um formato JSON legível na janela do console. Por exemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Extrair Atributos Faciais
 
Para extrair os atributos de face, use o modelo de detecção 1 e adicione o parâmetro de consulta `returnFaceAttributes`.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

A resposta agora inclui atributos de face. Por exemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um aplicativo de console Java simples que usa chamadas REST à API de Detecção Facial do Azure para detectar rostos em uma imagem e retornar os atributos deles. Em seguida, explore a documentação de referência de API de Detecção Facial para saber mais sobre os cenários com suporte.

> [!div class="nextstepaction"]
> [API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
