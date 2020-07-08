---
title: Como criar um conjunto de dados de treinamento para um reconhecedor de formulário de modelo personalizado
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que o conjunto de dados de treinamento seja otimizado para treinar um modelo de reconhecimento de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: c6780d952b9ce6ea58fc6c8a2509a4526add7149
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028254"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Criar um conjunto de dados de treinamento para um modelo personalizado

Ao usar o modelo personalizado do reconhecedor de formulário, você fornece seus próprios dados de treinamento para que o modelo possa treinar seus formulários específicos do setor. 

Se você estiver treinando sem rótulos manuais, poderá usar cinco formulários preenchidos ou um formulário vazio (você deve incluir a palavra "Empty" no nome do arquivo) mais dois formulários preenchidos. Mesmo que você tenha formulários preenchidos suficientes, adicionar um formulário vazio ao seu conjunto de dados de treinamento pode melhorar a precisão do modelo.

Se você quiser usar dados de treinamento rotulados manualmente, deverá começar com pelo menos cinco formulários preenchidos do mesmo tipo. Você ainda pode usar formulários sem rótulo e um formulário vazio além do conjunto de dados necessário.

## <a name="training-data-tips"></a>Dicas de dados de treinamento

É importante usar um conjunto de dados otimizado para treinamento. Use as dicas a seguir para garantir que você obtenha os melhores resultados da operação [treinar modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) :

* Se possível, use documentos PDF baseados em texto em vez de documentos baseados em imagem. Os PDFs digitalizados são tratados como imagens.
* Para formulários preenchidos, use exemplos que tenham todos os seus campos preenchidos.
* Use os formulários com diferentes valores em cada campo.
* Se suas imagens de formulário forem de qualidade inferior, use um conjunto de dados maior (10-15 imagens, por exemplo).
* O tamanho total do conjunto de dados de treinamento pode ser de até 500 páginas.

## <a name="general-input-requirements"></a>Requisitos de entrada gerais

Verifique se o conjunto de dados de treinamento também segue os requisitos de entrada para todo o conteúdo do reconhecedor de formulário. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Carregue seus dados de treinamento

Depois de reunir o conjunto de documentos de formulário que você usará para treinamento, você precisa carregá-lo em um contêiner de armazenamento de BLOBs do Azure. Se você não souber como criar uma conta de armazenamento do Azure com um contêiner, seguindo o guia de [início rápido do armazenamento do Azure para Portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Se você quiser usar dados rotulados manualmente, também precisará carregar o *.labels.js* e *.ocr.jsem* arquivos que correspondam aos seus documentos de treinamento. Você pode usar a [ferramenta de rotulagem de exemplo](./quickstarts/label-tool.md) (ou sua própria interface do usuário) para gerar esses arquivos.

### <a name="organize-your-data-in-subfolders-optional"></a>Organizar seus dados em subpastas (opcional)

Por padrão, a API de [modelo personalizado Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) só usará documentos de formulário localizados na raiz do seu contêiner de armazenamento. No entanto, você pode treinar com dados em subpastas se especificá-los na chamada à API. Normalmente, o corpo da chamada de [modelo personalizado Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) tem o seguinte formato, em que `<SAS URL>` é a URL de assinatura de acesso compartilhado do seu contêiner:

```json
{
  "source":"<SAS URL>"
}
```

Se você adicionar o conteúdo a seguir ao corpo da solicitação, a API será treinada com documentos localizados em subpastas. O `"prefix"` campo é opcional e limitará o conjunto de dados de treinamento a arquivos cujos caminhos começam com a cadeia de caracteres especificada. Portanto, um valor de `"Test"` , por exemplo, fará com que a API examine apenas os arquivos ou pastas que começam com a palavra "Test".

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

Agora que você aprendeu como criar um conjunto de dados de treinamento, siga um guia de início rápido para treinar um modelo de reconhecedor de formulário personalizado e comece a usá-lo em seus formulários.

* [Treinar um modelo e extrair dados de formulário usando a ondulação](./quickstarts/curl-train-extract.md)
* [Treinar um modelo e extrair dados de formulário usando a API REST e o Python](./quickstarts/python-train-extract.md)
* [Treine com rótulos usando a ferramenta de rotulagem de exemplo](./quickstarts/label-tool.md)
* [Treinar com rótulos usando a API REST e o Python](./quickstarts/python-labeled-data.md)
