---
title: Crie e explore conjuntos de dados com rótulos
titleSuffix: Azure Machine Learning
description: Aprenda a exportar etiquetas de dados de seus projetos de rotulagem de Machine Learning do Azure e use-os para tarefas de aprendizado de máquina.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549483"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Crie e explore o conjunto de dados Azure Machine Learning com rótulos

Neste artigo, você aprenderá a exportar os rótulos de dados de um projeto de rotulagem de dados do Azure Machine Learning e carregá-los em formatos populares, como, um dataframe pandas para exploração de dados ou um conjunto de dados Torchvision para transformação de imagem. 

## <a name="what-are-datasets-with-labels"></a>O que são conjuntos de dados com rótulos 

Os conjuntos de dados do Azure Machine Learning com rótulos são [TabularDatasets](how-to-create-register-datasets.md#dataset-types) com uma propriedade de rótulo, vamos nos referir a eles como conjuntos de dados rotulados. Esses tipos específicos de TabularDatasets são criados apenas como uma saída de projetos de rotulagem de dados do Azure Machine Learning. Crie um projeto de rotulagem de dados com [essas etapas](how-to-create-labeling-projects.md). O Machine Learning suporta projetos de rotulagem de dados para classificação de imagens, multi-rótulos ou multiclasse, e identificação de objetos, juntamente com caixas limitadas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* O [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao estúdio [Azure Machine Learning](https://ml.azure.com/).
    * Instale o pacote [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* Acesso a um projeto de rotulagem de dados do Azure Machine Learning. Se você não tem um projeto de rotulagem, crie um com [essas etapas](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Rótulos de dados de exportação 

Quando você concluir um projeto de rotulagem de dados, você pode exportar os dados do rótulo de um projeto de rotulagem. Fazendo isso, permite capturar tanto a referência aos dados quanto seus rótulos e exportá-los no [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados Azure Machine Learning. Use o botão **Exportar** na página **Detalhes do projeto** do projeto de rotulagem.

### <a name="coco"></a>COCO 

 O arquivo COCO é criado no armazenamento de blobs padrão do Workspace do Azure Machine Learning em uma pasta dentro de *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Conjunto de dados do Azure Machine Learning

Você pode acessar o conjunto de dados de aprendizado de máquina exportado na seção **Conjuntos** de dados do seu estúdio Azure Machine Learning. A página **Detalhes** do conjunto de dados também fornece código de exemplo para acessar seus rótulos do Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Explorar conjuntos de dados rotulados

Carregue seus conjuntos de dados rotulados em um dataframe pandas ou conjunto de dados Torchvision para aproveitar bibliotecas de código aberto populares para exploração de dados, bem como o PyTorch forneceu bibliotecas para transformação e treinamento de imagens.

### <a name="pandas-dataframe"></a>Dataframe do Pandas

Você pode carregar conjuntos de dados rotulados [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) em `azureml-contrib-dataset` um dataframe pandas com o método da classe. Instale a classe com o seguinte comando shell: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>O espaço de nome azureml.contrib muda com freqüência, à medida que trabalhamos para melhorar o serviço. Assim, tudo nesse namespace deve ser considerado como uma versão prévia e sem o suporte total da Microsoft.

Oferecemos as seguintes opções de tratamento de arquivos para fluxos de arquivos ao converter para um dataframe pandas.
* Download: Baixe seus arquivos de dados para um caminho local.
* Montagem: Monte seus arquivos de dados em um ponto de montagem. O mount só funciona para computação baseada em Linux, incluindo o notebook Azure Machine Learning VM e o Azure Machine Learning Compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Conjuntos de dados Torchvision

Você pode carregar conjuntos de dados rotulados no conjunto de `azureml-contrib-dataset` dados Torchvision com o método [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) também da classe. Para usar este método, você precisa ter [pyTorch](https://pytorch.org/) instalado. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Próximas etapas

* Consulte o [conjunto de dados com o caderno de etiquetas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) para obter uma amostra completa de treinamento.
