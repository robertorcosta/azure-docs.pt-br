---
title: Como especificar um modelo de detecção - Face
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como escolher qual modelo de detecção facial usar com o aplicativo Azure Face.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76934568"
---
# <a name="specify-a-face-detection-model"></a>Especificar um modelo de detecção facial

Este guia mostra como especificar um modelo de detecção facial para o serviço Azure Face.

O serviço Face usa modelos de aprendizado de máquina para realizar operações em rostos humanos em imagens. Continuamos a melhorar a precisão de nossos modelos com base no feedback dos clientes e avanços na pesquisa, e entregamos essas melhorias como atualizações de modelos. Os desenvolvedores têm a opção de especificar qual versão do modelo de detecção facial eles gostariam de usar; eles podem escolher o modelo que melhor se encaixa em seu caso de uso.

Continue lendo para saber como especificar o modelo de detecção facial em certas operações faciais. O serviço Face usa detecção facial sempre que converte uma imagem de um rosto em alguma outra forma de dados.

Se você não tiver certeza se deve usar o modelo mais recente, pule para a seção [Avaliar diferentes modelos](#evaluate-different-models) para avaliar o novo modelo e comparar resultados usando seu conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com o conceito de detecção facial de IA. Se você não estiver, consulte o guia conceitual de detecção facial ou como fazer:

* [Conceitos de detecção facial](../concepts/face-detection.md)
* [Como detectar rostos em uma imagem](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detectar rostos com modelo especificado

A detecção facial encontra os locais de caixa delimitador de rostos humanos e identifica seus marcos visuais. Ele extrai os recursos do rosto e armazena-os para uso posterior em operações [de reconhecimento.](../concepts/face-recognition.md)

Quando você usa o [Face - Detecte] a API, você pode atribuir a versão do modelo com o `detectionModel` parâmetro. Os valores disponíveis são:

* `detection_01`
* `detection_02`

Uma URL de solicitação para o [Face - Detectrest] REST API será assim:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca do cliente, você pode atribuir o valor para `detectionModel` passar em uma seqüência apropriada. Se você deixá-lo não atribuído, a API`detection_01`usará a versão padrão do modelo ( ). Veja o exemplo de código a seguir na biblioteca de clientes .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Adicionar face a Pessoa com modelo especificado

O serviço Face pode extrair dados faciais de uma imagem e associá-los a um objeto **PersonGroup** person [- Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API. Nesta chamada de API, você pode especificar o modelo de detecção da mesma forma que em [Face - Detect].

Veja o exemplo de código a seguir na biblioteca de clientes .NET.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Esse código cria um **PersonGroup** com ID `mypersongroupid` e adiciona uma **Pessoa** a ele. Em seguida, ele **Person** adiciona um `detection_02` Rosto a essa Pessoa usando o modelo. Se você não especificar o parâmetro *detectionModel,* a API `detection_01`usará o modelo padrão, .

> [!NOTE]
> Você não precisa usar o mesmo modelo de detecção para todos os rostos em um objeto **Person,** e você não precisa usar o mesmo modelo de detecção ao detectar novos rostos para comparar com um objeto **Person** (no [Rosto - Identifique] a API, por exemplo).

## <a name="add-face-to-facelist-with-specified-model"></a>Adicionar facelist com modelo especificado

Você também pode especificar um modelo de detecção quando adicionar um rosto a um objeto **FaceList** existente. Veja o exemplo de código a seguir na biblioteca de clientes .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Este código cria uma `My face collection` **FaceList** chamada e `detection_02` adiciona um Face a ele com o modelo. Se você não especificar o parâmetro *detectionModel,* a API `detection_01`usará o modelo padrão, .

> [!NOTE]
> Você não precisa usar o mesmo modelo de detecção para todos os rostos em um objeto **FaceList,** e não precisa usar o mesmo modelo de detecção ao detectar novos rostos para comparar com um objeto **FaceList.**

## <a name="evaluate-different-models"></a>Avaliar diferentes modelos

Os diferentes modelos de detecção facial são otimizados para diferentes tarefas. Veja a tabela a seguir para obter uma visão geral das diferenças.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Escolha padrão para todas as operações de detecção facial. | Lançado em maio de 2019 e disponível opcionalmente em todas as operações de detecção facial.
|Não otimizado para rostos pequenos, laterais ou embaçados.  | Maior precisão em rostos pequenos, laterais e embaçados. |
|Retorna atributos faciais (pose de cabeça, idade, emoção e assim por diante) se eles forem especificados na chamada de detecção. |  Não retorna atributos faciais.     |
|Retorna pontos de referência se eles forem especificados na chamada de detecção.   | Não retorna marcos faciais.  |

A melhor maneira de comparar `detection_01` os `detection_02` desempenhos dos modelos e dos modelos é usá-los em um conjunto de dados de amostra. Recomendamos chamar o [Face - Detectar] API em uma variedade de imagens, especialmente imagens de muitos rostos ou de rostos que são difíceis de ver, usando cada modelo de detecção. Preste atenção ao número de rostos que cada modelo retorna.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como especificar o modelo de detecção para usar com diferentes APIs do Face. Em seguida, siga um início rápido para começar usando a detecção facial.

* [Rosto .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Face – Detectar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
