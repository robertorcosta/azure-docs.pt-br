---
title: 'Início Rápido: Detectar rostos em uma imagem com a API REST do Azure e C#'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você usará a API REST de Detecção Facial do Azure com C# para detectar faces em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 1b8bcaf283e612b3ebe6d6b7bb5660e8b3179ad3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858363"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Início Rápido: Detectar rostos em uma imagem com a API REST de Detecção Facial e C#

Neste Início Rápido, você usará a API REST de Detecção Facial do Azure com o C# para detectar rostos humanos em uma imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
- Qualquer edição do [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um novo projeto de **Aplicativo de Console (.NET Framework)** e dê o nome de **FaceDetection**.
1. Se houver outros projetos em sua solução, selecione este como o único projeto de inicialização.

## <a name="add-face-detection-code"></a>Adicionar código de detecção facial

Abra o arquivo *Program.cs* do novo projeto. Aqui, você adicionará o código necessário para carregar imagens e detectar faces.

### <a name="include-namespaces"></a>Incluir namespaces

Adicione as instruções `using` a seguir à parte superior do seu arquivo *Program.cs*.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Adicione a classe **Programa** que contém os campos a seguir. Esses dados especificam como se conectar ao serviço de Detecção Facial e onde obter os dados de entrada. Você precisará atualizar o campo `subscriptionKey` com o valor da sua chave de assinatura e, talvez, precise alterar a cadeia de caracteres `uriBase` para que contenha a cadeia de caracteres do ponto de extremidade de seu recurso.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>Receber entrada de imagem

Adicione o código a seguir ao método **Main** na classe **Program**. Esse código grava um prompt para o console solicitando que o usuário insira seu caminho de arquivo de imagem local. Em seguida, ele chama outro método, **MakeAnalysisRequest**, para processar a imagem nesse local.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>Chamar a API REST de detecção facial

Adicione o seguinte método à classe **Programa**. Ele constrói uma chamada REST para a API de Detecção Facial para detectar informações sobre a face na imagem remota (a cadeia de caracteres `requestParameters` especifica quais atributos de face recuperar). Em seguida, ele grava os dados de saída em uma cadeia de caracteres JSON.

Você definirá os métodos auxiliares nas etapas a seguir.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>Processar os dados de imagem de entrada

Adicione o seguinte método à classe **Programa**. Esse método converte a imagem no caminho do arquivo especificado em uma matriz de bytes.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>Analisar a resposta JSON

Adicione o seguinte método à classe **Programa**. Esse método formata a entrada JSON para facilitar a leitura. Seu aplicativo gravará esses dados de cadeia de caracteres no console. Em seguida, você pode fechar a classe e o namespace.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>Executar o aplicativo

Uma resposta bem-sucedida exibirá dados de Detecção Facial em um formato JSON legível. Por exemplo:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
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

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

A resposta agora inclui atributos de face. Por exemplo:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
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
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
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

Neste início rápido, você criou um aplicativo de console .NET simples que usa chamadas REST com o serviço de Detecção Facial do Azure para detectar rostos em uma imagem e retornar os atributos deles. Em seguida, explore a documentação de referência de API de Detecção Facial para saber mais sobre os cenários com suporte.

> [!div class="nextstepaction"]
> [API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
