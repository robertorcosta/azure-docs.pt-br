---
title: Como especificar um modelo de reconhecimento - Face
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como escolher qual modelo de reconhecimento usar com o aplicativo Azure Face.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938835"
---
# <a name="specify-a-face-recognition-model"></a>Especificar um modelo de reconhecimento facial

Este guia mostra como especificar um modelo de reconhecimento facial para detecção facial, identificação e pesquisa de similaridade usando o serviço Azure Face.

O serviço Face usa modelos de aprendizado de máquina para realizar operações em rostos humanos em imagens. Continuamos a melhorar a precisão de nossos modelos com base no feedback dos clientes e avanços na pesquisa, e entregamos essas melhorias como atualizações de modelos. Os desenvolvedores têm a opção de especificar qual versão do modelo de reconhecimento facial eles gostariam de usar; eles podem escolher o modelo que melhor se encaixa em seu caso de uso.

Se você é um novo usuário, recomendamos que você use o modelo mais recente. Continue lendo para saber como especificá-lo em diferentes operações do Face, evitando conflitos de modelos. Se você é um usuário avançado e não tem certeza se deve mudar para o modelo mais recente, pule para a seção [Avaliar diferentes modelos](#evaluate-different-models) para avaliar o novo modelo e comparar resultados usando seu conjunto de dados atual.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com os conceitos de detecção e identificação facial de IA. Se você não estiver, veja primeiro estes guias de como fazer:

* [Como detectar rostos em uma imagem](HowtoDetectFacesinImage.md)
* [Como identificar rostos em uma imagem](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Detectar rostos com modelo especificado

A detecção facial identifica os marcos visuais dos rostos humanos e encontra seus locais de caixa delimitador. Ele também extrai as características do rosto e armazena-as para uso na identificação. Todas essas informações formam a representação de um rosto.

O modelo de reconhecimento é usado quando os recursos faciais são extraídos, para que você possa especificar uma versão do modelo ao executar a operação Detectar.

Ao usar o [Face - Detecte] a API, atribua a versão do modelo com o `recognitionModel` parâmetro. Os valores disponíveis são:

* `recognition_01`
* `recognition_02`

Opcionalmente, você pode especificar o parâmetro _returnRecognitionModel_ **(falso**padrão) para indicar se _o recognitionModel_ deve ser retornado em resposta. Assim, uma URL de solicitação para o [Face - Detecte] a API REST será assim:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se você estiver usando a biblioteca do cliente, você pode atribuir o valor para `recognitionModel` passar uma seqüência representando a versão.
Se você deixá-lo não atribuído, a versão padrão do modelo _(recognition_01)_ será usada. Veja o exemplo de código a seguir na biblioteca de clientes .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifique rostos com modelo especificado

O serviço Face pode extrair dados faciais de uma imagem e associá-los a um objeto **Person** (através da chamada API [adicionar face,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) por exemplo), e vários objetos **person** podem ser armazenados juntos em um **PersonGroup**. Em seguida, um novo rosto pode ser comparado com um **PersonGroup** (com a [chamada Face - Identificar),] e a pessoa correspondente dentro desse grupo pode ser identificada.

Um **PersonGroup** deve ter um modelo de reconhecimento único para todas `recognitionModel` as **Pessoas,** e você pode especificar isso usando o parâmetro ao criar o grupo ([PersonGroup - Create] ou [LargePersonGroup - Create]). Se você não especificar este `recognition_01` parâmetro, o modelo original será usado. Um grupo sempre usará o modelo de reconhecimento com o que foi criado, e novas faces se associarão a esse modelo quando forem adicionadas a ele; isso não pode ser alterado após a criação de um grupo. Para ver com qual modelo um **PersonGroup** está configurado, use o [PersonGroup - Obtenha] a API com o parâmetro _returnRecognitionModel_ definido como **verdadeiro**.

Veja o exemplo de código a seguir na biblioteca de clientes .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Neste código, um **PersonGroup** `mypersongroupid` com ID é criado, e ele é configurado para usar o modelo _recognition_02_ para extrair recursos faciais.

Correspondentemente, você precisa especificar qual modelo usar ao detectar rostos para comparar com este **PersonGroup** (através do [Face - Detecte] API). O modelo que você usa deve ser sempre consistente com a configuração do **PersonGroup;** caso contrário, a operação falhará devido a modelos incompatíveis.

Não há alteração no [Face - Identifique] a API; você só precisa especificar a versão do modelo na detecção.

## <a name="find-similar-faces-with-specified-model"></a>Encontre rostos semelhantes com modelo especificado

Você também pode especificar um modelo de reconhecimento para pesquisa de similaridade. Você pode atribuir a `recognitionModel` versão do modelo com ao criar a lista de rostos com [o FaceList - Criar] API ou [LargeFaceList - Criar]. Se você não especificar este `recognition_01` parâmetro, o modelo original será usado. Uma lista de rostos sempre usará o modelo de reconhecimento com o que foi criado, e novas faces se associarão a este modelo quando forem adicionadas a ele; isso não pode ser alterado após a criação. Para ver com qual modelo uma lista de rostos está configurado, use o [FaceList - Obtenha] a API com o parâmetro _returnRecognitionModel_ definido como **verdadeiro**.

Veja o exemplo de código a seguir na biblioteca de clientes .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Este código cria uma `My face collection`lista de rostos chamada , usando o modelo _recognition_02_ para extração de recursos. Quando você pesquisa esta lista facial para rostos semelhantes a um novo rosto detectado, esse rosto deve ter sido detectado ([Face - Detect]) usando o modelo _recognition_02._ Como na seção anterior, o modelo precisa ser consistente.

Não há nenhuma mudança no Face - Encontre API [semelhante;] você só especifica a versão do modelo na detecção.

## <a name="verify-faces-with-specified-model"></a>Verificar rostos com modelo especificado

O [Face - Verifique se] duas faces pertencem à mesma pessoa. Não há alteração na API Verificar em relação aos modelos de reconhecimento, mas você só pode comparar rostos que foram detectados com o mesmo modelo. Assim, as duas faces precisarão ter sido `recognition_01` `recognition_02`detectadas usando ou .

## <a name="evaluate-different-models"></a>Avaliar diferentes modelos

Se você quiser comparar as performances dos modelos _de recognition_01_ e _recognition_02_ em seus dados, você precisará:

1. Crie dois **PersonGroup**s com _recognition_01_ e _recognition_02_ respectivamente.
1. Use os dados de sua imagem para detectar rostos e registrá-los no **PersonGroup**s para esses dois **PersonGroup**s, e desencadear o processo de treinamento com [persongroup - Train] API.
1. Teste com [face - Identifique-se] em ambos **os Grupos**de Pessoas e compare os resultados.

Se você normalmente especificar um limiar de confiança (um valor entre zero e um que determina o quão confiante o modelo deve ser para identificar um rosto), você pode precisar usar diferentes limiares para diferentes modelos. Um limiar para um modelo não deve ser compartilhado com outro e não necessariamente produzirá os mesmos resultados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a especificar o modelo de reconhecimento a ser usado com diferentes APIs de serviço face. Em seguida, siga um início rápido para começar usando a detecção facial.

* [Rosto .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Face – Detectar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Rosto - Encontrar Semelhante]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Rosto - Verificar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[GrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[GrupoDePessoas - Treinar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[GrandeGrupoDePessoas - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[Lista de Rostos - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[Lista de rostos - Obter]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Criar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
