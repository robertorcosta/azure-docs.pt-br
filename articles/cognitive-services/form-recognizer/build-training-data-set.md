---
title: Como construir um conjunto de dados de treinamento para um modelo personalizado - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que seu conjunto de dados de treinamento seja otimizado para treinar um modelo de Reconhecimento de Formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380619"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Construa um conjunto de dados de treinamento para um modelo personalizado

Quando você usa o modelo personalizado do Form Recognizer, você fornece seus próprios dados de treinamento para que o modelo possa treinar para os formulários específicos do seu setor. Você pode treinar um modelo com cinco formulários preenchidos ou um formulário vazio (você deve incluir a palavra "vazio" no nome do arquivo) mais dois formulários preenchidos. Mesmo que você tenha formulários preenchidos suficientes para treinar, adicionar um formulário vazio ao seu conjunto de dados de treinamento pode melhorar a precisão do modelo.

Se você quiser usar dados de treinamento rotulados manualmente, você deve começar com pelo menos cinco formas do mesmo tipo. Você ainda pode usar formulários não rotulados e um formulário vazio no mesmo conjunto de dados.

## <a name="training-data-tips"></a>Dicas de dados de treinamento

É importante usar um conjunto de dados otimizado para treinamento. Use as seguintes dicas para garantir que você obtenha os melhores resultados da operação [Train Custom Model:](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)

* Se possível, use documentos PDF baseados em texto em vez de documentos baseados em imagem. PDFs digitalizados são tratados como imagens.
* Para formulários preenchidos, use exemplos que tenham todos os seus campos preenchidos.
* Use os formulários com diferentes valores em cada campo.
* Se as imagens de formulário forem de menor qualidade, use um conjunto de dados maior (10-15 imagens, por exemplo).
* O tamanho total do conjunto de dados de treinamento pode ser de até 500 páginas.

## <a name="general-input-requirements"></a>Requisitos gerais de entrada

Certifique-se de que seu conjunto de dados de treinamento também segue os requisitos de entrada para todo o conteúdo do Reconhecimento de Formulário. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Carregue seus dados de treinamento

Quando você tiver reunido o conjunto de documentos de formulário que você usará para treinamento, você precisa carregá-lo para um contêiner de armazenamento azure blob. Se você não sabe como criar uma conta de armazenamento Azure com um contêiner, seguindo o [quickstart do Azure Storage para o portal Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organize seus dados em subpastas (opcional)

Por padrão, a API [do Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) de Trem usará apenas documentos de formulário localizados na raiz do recipiente de armazenamento. No entanto, você pode treinar com dados em subpastas se você especificar na chamada da API. Normalmente, o corpo da chamada [Train Custom](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) `<SAS URL>` Model tem o seguinte formulário, onde está a URL de assinatura de acesso compartilhado do seu contêiner:

```json
{
  "source":"<SAS URL>"
}
```

Se você adicionar o seguinte conteúdo ao órgão de solicitação, a API treinará com documentos localizados em subpastas. O `"prefix"` campo é opcional e limitará o conjunto de dados de treinamento a arquivos cujos caminhos começam com a seqüência dada. Assim, um `"Test"`valor de , por exemplo, fará com que a API olhe apenas para os arquivos ou pastas que começam com a palavra "Teste".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a construir um conjunto de dados de treinamento, siga um início rápido para treinar um modelo personalizado de Reconhecimento de Formulário e comece a usá-lo em seus formulários.

* [Quickstart: Treine um modelo e extraa dados de formulário usando cURL](./quickstarts/curl-train-extract.md)
* [Quickstart: Treine um modelo e extraia dados de formulário usando a API REST com Python](./quickstarts/python-train-extract.md)
* [Treinar com rótulos usando a API REST e o Python](./quickstarts/python-labeled-data.md)