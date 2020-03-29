---
title: Detectar rostos em uma imagem - Face
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como usar a detecção facial para extrair atributos como sexo, idade ou pose de uma determinada imagem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169884"
---
# <a name="get-face-detection-data"></a>Obtenha dados de detecção facial

Este guia demonstra como usar a detecção facial para extrair atributos como sexo, idade ou pose de uma determinada imagem. Os trechos de código deste guia são escritos em C# usando a biblioteca cliente Azure Cognitive Services Face. A mesma funcionalidade está disponível através da [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia mostra como:

- Obtenha os locais e dimensões dos rostos em uma imagem.
- Obtenha a localização de vários pontos turísticos, como pupilas, nariz e boca, em uma imagem.
- Adivinha o sexo, idade, emoção e outros atributos de um rosto detectado.

## <a name="setup"></a>Instalação

Este guia pressupõe que você já construiu `faceClient`um objeto [FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) chamado , com uma chave de assinatura Face e URL de ponto final. A partir daqui, você pode usar o recurso de detecção facial chamando [detectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), que é usado neste guia, ou [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Para obter instruções sobre como configurar este recurso, siga uma das partidas rápidas.

Este guia se concentra nas especificidades da chamada Detect, como quais argumentos você pode passar e o que você pode fazer com os dados retornados. Recomendamos que você consulte apenas os recursos necessários. Cada operação leva mais tempo para ser concluída.

## <a name="get-basic-face-data"></a>Obtenha dados básicos do rosto

Para encontrar rostos e obter suas localizações em uma imagem, chame o método com o parâmetro _returnFaceId_ definido como **true**. Esta é a configuração padrão.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Você pode consultar os objetos [DetectedFace retornados](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) para seus IDs exclusivos e um retângulo que dá as coordenadas de pixel do rosto.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Para obter informações sobre como analisar a localização e as dimensões do rosto, consulte [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Normalmente, este retângulo contém os olhos, sobrancelhas, nariz e boca. O topo da cabeça, orelhas e queixo não estão necessariamente incluídos. Para usar o retângulo facial para cortar uma cabeça completa ou obter um retrato de tiro médio, talvez para uma imagem tipo identificação de foto, você pode expandir o retângulo em cada direção.

## <a name="get-face-landmarks"></a>Obter marcos faciais

[Os marcos faciais](../concepts/face-detection.md#face-landmarks) são um conjunto de pontos fáceis de encontrar em um rosto, como as pupilas ou a ponta do nariz. Para obter dados de referência facial, defina o parâmetro _returnFaceLandmarks_ como **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

O código a seguir demonstra como você pode recuperar os locais do nariz e dos alunos:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Você também pode usar dados de marcos faciais para calcular com precisão a direção do rosto. Por exemplo, você pode definir a rotação do rosto como um vetor do centro da boca para o centro dos olhos. O código a seguir calcula este vetor:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Quando você sabe a direção do rosto, você pode girar o quadro retangular do rosto para alinhá-lo mais corretamente. Para cortar rostos em uma imagem, você pode girar programáticamente a imagem para que os rostos sempre apareçam eretos.

## <a name="get-face-attributes"></a>Obter atributos faciais

Além de retângulos e pontos turísticos, a API de detecção facial pode analisar vários atributos conceituais de um rosto. Para obter uma lista completa, consulte a seção conceitual [face.](../concepts/face-detection.md#attributes)

Para analisar os atributos faciais, defina o parâmetro _returnFaceAttributes_ como uma lista de valores do [FaceAttributeType Enum.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Em seguida, obtenha referências aos dados devolvidos e faça mais operações de acordo com suas necessidades.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Para saber mais sobre cada um dos atributos, consulte a [detecção facial e atributos](../concepts/face-detection.md) guia conceitual.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a usar as várias funcionalidades da detecção facial. Em seguida, integre esses recursos em seu aplicativo seguindo um tutorial em profundidade.

- [Tutorial: Criar um aplicativo do WPF para exibir dados faciais em uma imagem](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Tutorial: Criar um aplicativo Android para detectar e enquadrar rostos em uma imagem](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)