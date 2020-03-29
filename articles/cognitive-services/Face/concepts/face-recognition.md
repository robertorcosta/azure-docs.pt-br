---
title: Conceitos de reconhecimento facial
titleSuffix: Azure Cognitive Services
description: Este artigo explica os conceitos das operações de Verificação, Encontrar Similares, Grupos e Identificar o reconhecimento facial e as estruturas de dados subjacentes.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743066"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento facial

Este artigo explica os conceitos das operações de Verificação, Encontrar Similares, Grupos e Identificar o reconhecimento facial e as estruturas de dados subjacentes. Em linhas gerais, o reconhecimento descreve o trabalho de comparar duas faces diferentes para determinar se são semelhantes ou pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionadas ao reconhecimento

As operações de reconhecimento utilizam principalmente as seguintes estruturas de dados. Esses objetos são armazenados na nuvem e podem ser referenciados por suas strings iD. As seqüências de identificação são sempre únicas dentro de uma assinatura. Os campos de nome podem ser duplicados.

|Nome|Descrição|
|:--|:--|
|DetectedFace| Esta representação de rosto único é recuperada pela operação [de detecção facial.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) Sua id expira 24 horas após sua criação.|
|PersistedFace| Quando objetos DoTectedFace são adicionados a um grupo, como FaceList ou Person, eles se tornam objetos PersistedFace. Eles podem ser [recuperados a](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) qualquer momento e não expirar.|
|[Lista de rostos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [lista de rostos grandes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Essa estrutura de dados é uma lista variada de objetos PersistedFace. Um FaceList tem um ID exclusivo, uma seqüência de nomes e, opcionalmente, uma seqüência de dados do usuário.|
|[Pessoa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Essa estrutura de dados é uma lista de objetos PersistedFace que pertencem à mesma pessoa. Ele tem um ID exclusivo, uma seqüência de nomes e, opcionalmente, uma seqüência de dados do usuário.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Essa estrutura de dados é uma lista variada de objetos person. Ele tem um ID exclusivo, uma seqüência de nomes e, opcionalmente, uma seqüência de dados do usuário. Um PersonGroup deve ser [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes de ser usado em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta seção detalha como as quatro operações de reconhecimento usam as estruturas de dados descritas anteriormente. Para obter uma descrição ampla de cada operação de reconhecimento, consulte [Visão Geral](../Overview.md).

### <a name="verify"></a>Verificar

A operação [Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) pega um ID facial do DetectedFace ou PersistedFace e outro Face ID ou um objeto Person e determina se eles pertencem à mesma pessoa. Se você passar em um objeto Person, você pode opcionalmente passar em um PersonGroup ao qual essa Pessoa pertence para melhorar o desempenho.

### <a name="find-similar"></a>Localizar semelhante

A operação [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) leva um ID facial do DetectedFace ou PersistedFace e uma FaceList ou uma matriz de outros IDs faciais. Com uma FaceList, ele retorna uma FaceList menor de rostos que são semelhantes ao rosto dado. Com uma matriz de IDs faciais, ele também retorna uma matriz menor.

### <a name="group"></a>Agrupar

A operação [Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) pega uma matriz de IDs faciais variados do DetectedFace ou PersistedFace e retorna os mesmos IDs agrupados em várias matrizes menores. Cada matriz de "grupos" contém IDs faciais que parecem semelhantes. Uma única matriz "messyGroup" contém IDs de rosto para os quais nenhuma semelhança foi encontrada.

### <a name="identify"></a>Identificar

A operação [Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) leva um ou vários IDs faciais do DetectedFace ou PersistedFace e um PersonGroup e retorna uma lista de objetos de Pessoa a que cada rosto pode pertencer. Objetos de Pessoa Devolvida são embrulhados como objetos candidatos, que têm um valor de confiança de previsão.

## <a name="input-data"></a>Dados de entrada

Use as seguintes dicas para garantir que suas imagens de entrada dêem os resultados de reconhecimento mais precisos:

* Os formatos de imagem de entrada suportados são JPEG, PNG, GIF (o primeiro quadro), BMP.
* O tamanho do arquivo de imagem não deve ser maior que 4 MB.
* Quando você cria objetos Person, use fotos que apresentam diferentes tipos de ângulos e iluminação.
* Alguns rostos podem não ser reconhecidos por causa de desafios técnicos, tais como:
  * Imagens com iluminação extrema, por exemplo, iluminação traseira severa.
  * Obstruções que bloqueiam um ou ambos os olhos.
  * Diferenças no tipo de cabelo ou pelos faciais.
  * Mudanças na aparência facial por causa da idade.
  * Expressões faciais extremas.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com conceitos de reconhecimento facial, aprenda a escrever um script que identifique rostos contra um PersonGroup treinado.

* [Identificar faces em imagens](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)