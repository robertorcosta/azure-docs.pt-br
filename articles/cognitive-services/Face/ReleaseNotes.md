---
title: Notas de versão-serviço de face
titleSuffix: Azure Cognitive Services
description: As notas de versão do serviço de face incluem um histórico das alterações de versão para várias versões.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76165862"
---
# <a name="face-release-notes"></a>Notas de versão facial

Este artigo pertence à versão do serviço de face 1,0.

### <a name="release-changes-in-june-2019"></a>Alterações de versão em junho de 2019

* Foi adicionado um novo modelo de detecção facial com precisão aprimorada em faces pequenas, obstruído e borradas. Use-a por meio [de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facelist-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)adicionar face, [LargeFaceList-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [pessoa-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) adicionar face e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) especificando o novo nome do modelo de detecção facial `detection_02` no `detectionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de detecção](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Alterações de versão em abril de 2019

* Maior precisão geral dos `age` atributos e `headPose` . O `headPose` atributo também é atualizado com o `pitch` valor habilitado agora. Use esses atributos especificando-os no `returnFaceAttributes` parâmetro do parâmetro de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Melhoria na velocidade de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Facelist-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [pessoa de](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) pessoa física-adicionar face e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Alterações de versão em março de 2019

* Adicionou um novo modelo de reconhecimento facial com precisão aprimorada. Use-a por meio [de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facelist-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Person-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) Create e [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) especificando o novo nome do modelo de reconhecimento facial `recognition_02` no `recognitionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de reconhecimento](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>alterações de versão em janeiro de 2019

* Adicionado recurso de instantâneo para dar suporte à migração de dados entre assinaturas: [instantâneo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mais detalhes [sobre como migrar os dados de face para uma assinatura facial diferente](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Alterações de versão em outubro de 2018

* Descrição refinada para `status`, `createdDateTime`, `lastActionDateTime` e `lastSuccessfulTrainingDateTime` em [PersonGroup – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) e [LargeFaceList – Obter Status de Treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Alterações de versão em maio de 2018

* Atributo `gender` aprimorado significativamente e também atributos `age`, `glasses`, `facialHair`, `hair`, `makeup` aprimorados. Use-os por meio do parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Limite de tamanho do arquivo de imagem de entrada maior, passando 4 MB para 6 MB em [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [Pessoa de LargePersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Alterações de versão em março de 2018

* Adicionado o Contêiner de Escala de Milhões: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mais detalhes sobre [Como usar o recurso de larga escala](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Parâmetro [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` aumentado de [1, 5] para [1, 100] e o padrão para 10.

### <a name="release-changes-in-may-2017"></a>Alterações de versão em maio de 2017

* Adicionados os atributos `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` e `noise` no parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.

* Suporte para dez mil pessoas em um PersonGroup e [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Paginação compatível em [PersonGroup pessoa – Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) com parâmetros opcionais: `start` e `top`.

* Simultaneidade compatível na adição/exclusão de faces com relação a diferentes FaceLists e diferentes pessoas em PersonGroup.

### <a name="release-changes-in-march-2017"></a>Alterações de versão em março de 2017
* Adicionado o atributo `emotion` no parâmetro [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.

* Corrigida face que não podia ser detectada com o retângulo retornado de [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como `targetFace` em [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Corrigido tamanho de face detectável garantir que fique estritamente entre 36 x 36 a 4.096 x 4.096 pixels.

### <a name="release-changes-in-november-2016"></a>Alterações de versão em novembro de 2016
* Adicionada assinatura Armazenamento Facial Padrão para armazenar faces persistentes adicionais ao usar [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) para correspondência de similaridade ou identificação. As imagens armazenadas são cobradas a US$ 0,5 por mil faces, e essa taxa é dividida proporcionalmente por dia. As assinaturas da camada gratuita continuam limitadas a mil pessoas no total.

### <a name="release-changes-in-october-2016"></a>Alterações de versão em outubro de 2016
* Alterada a mensagem de erro de mais de uma face em targetFace, passando de 'Existem mais de uma face na imagem' para 'Existe mais de uma face na imagem' em [FaceList – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [Pessoa de PersonGroup – Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Alterações de versão de julho de 2016
* Face compatível para autenticação de objeto em [Face – Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Adicionado o parâmetro `mode` opcional possibilitando a seleção de dois modos de trabalho: `matchPerson` e `matchFace`, em [Face – Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e o padrão é `matchPerson`.

* Adicionado o parâmetro `confidenceThreshold` opcional para o usuário definir o limite de se uma face pertence a um objeto de Pessoa em [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Adicionados os parâmetros `start` e `top` opcionais em [PersonGroup – Listar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) para permitir que o usuário especifique o ponto inicial e o número total de PersonGroups para a lista.

### <a name="v10-changes-from-v0"></a>Alterações à V1.0 da V0
* Atualizado o ponto de extremidade da raiz do serviço de ```https://westus.api.cognitive.microsoft.com/face/v0/``` para ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Alterações aplicadas a: [Face – Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face – Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face – Localizar Semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Face – Agrupar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Atualizado o tamanho mínimo face detectável para 36 x 36 pixels. Faces menores que 36 x 36 pixels não serão detectadas.

* Preteridos os dados de PersonGroup e Pessoa de Detecção Facial V0. Esses dados não podem ser acessados com o serviço Detecção Facial V1.0.

* Preterido o ponto de extremidade V0 da API de Detecção Facial em 30 de junho de 2016.
