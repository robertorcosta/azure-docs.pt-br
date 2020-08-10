---
title: Criar conjuntos de itens com os conjuntos de valores abertos do Azure
titleSuffix: Azure Open Datasets
description: Saiba como criar um conjunto de Azure Machine Learning de conjuntos de valores abertos do Azure.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: db2e493f04d74308b21e8a63222f26b1e2af8546
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038159"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Criar conjuntos de Azure Machine Learning de conjunto de os conjuntos de valores abertos do Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar um conjunto de dados de Azure Machine Learning de [Azure Open](https://azure.microsoft.com/services/open-datasets/) DataSets a fim de acessar as informações para seus experimentos locais ou remotos. 

Ao criar um [conjunto](../machine-learning/how-to-create-register-datasets.md)de dados Azure Machine Learning, você cria uma referência para o local da fonte de dado, juntamente com uma cópia de seus metadados. Como os dados permanecem em seu local existente, você não incorre nenhum custo de armazenamento extra e não tem risco de alterar suas fontes de dados originais de forma não intencional. Além disso, os conjuntos de itens são avaliados lentamente, o que ajuda em velocidades de desempenho do fluxo de trabalho.
 
Para entender onde os conjuntos de dados se encaixam no fluxo de trabalho de acesso de data de Azure Machine Learning geral, confira o artigo [dados de acesso seguro](../machine-learning/concept-data.md#data-workflow) .


Os conjuntos de itens abertos estão na nuvem no Microsoft Azure e são incluídos no [SDK do Azure Machine Learning Python](#create-datasets-with-the-sdk) e no [Azure Machine Learning Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Por que usar os conjuntos de itens abertos do Azure? 

Os Conjuntos de dados abertos do Azure são conjuntos de dados públicos coletados que você pode usar para adicionar recursos específicos do cenário para soluções de aprendizado de máquina para obter modelos mais precisos. Os conjuntos de dados incluem dados de domínio público de clima, censo, feriados, segurança pública e localização que ajudam você a treinar os modelos de machine learning e aprimorar as soluções de previsão. 

Para obter mais informações, consulte [o que são conjuntos de dados abertos?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma assinatura do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

    * Crie uma [instância de computação Azure Machine Learning](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), que é um ambiente de desenvolvimento totalmente configurado e gerenciado que inclui blocos de anotações integrados e o SDK já instalado.

    **OR**

    * Trabalhe em seu próprio notebook Jupyter e instale o SDK com [estas instruções](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , que é compatível apenas com Python de 64 bits. Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux (7, 8), Ubuntu (14, 4, 16, 4, 18, 4), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Criar conjuntos de os com o SDK

Para criar conjuntos de valores por meio de classes Open DataSets no SDK do Azure Machine Learning Python, verifique se você instalou o pacote com o `pip install azureml-opendatasets` . Cada conjunto de dados discretos é representado por sua própria classe no SDK, e determinadas classes estão disponíveis como um `TabularDataset` , `FileDataset` ou ambos. Consulte a [documentação de referência](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) para obter uma lista completa de classes.

Você pode recuperar determinadas classes como um `TabularDataset` ou o `FileDataset` , que permite manipular e/ou baixar os arquivos diretamente. Outras classes **só** podem obter um conjunto de um DataSet usando uma das `get_tabular_dataset()` `get_file_dataset()` funções ou. O exemplo de código a seguir mostra alguns exemplos desses tipos de classes.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando você registra um conjunto de dados criado a partir de DataSets abertos, nenhum dado é baixado imediatamente, mas os dados serão acessados posteriormente quando solicitado (durante o treinamento, por exemplo) de um local de armazenamento central.

## <a name="create-datasets-with-the-studio"></a>Criar conjuntos de os com o estúdio

Você também pode criar conjuntos de valores de conjuntos de os abertos com o [Azure Machine Learning Studio](https://ml.azure.com). Em seu espaço de trabalho, selecione a guia **conjuntos de valores** em **ativos**. No menu suspenso **criar conjunto** de linhas, selecione **de conjuntos de valores abertos**.

![Abrir conjunto de um com a interface do usuário](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

Selecione um conjunto de um DataSet selecionando seu bloco. (Você tem a opção de filtrar usando a barra de pesquisa.) Selecione **Avançar**.

![Escolher conjunto de um](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

Escolha um nome sob o qual registrar o conjunto de dados e, opcionalmente, filtre-os usando os filtros disponíveis. Nesse caso, para o conjunto de data de feriados públicos, você filtra o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Definir parâmetros de conjunto de DataSet e criar conjunto de um](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

Agora, o conjunto de linhas está disponível em seu espaço de trabalho em **conjuntos**de os. Você pode usá-lo da mesma maneira que outros conjuntos de os que você criou.


## <a name="access-datasets-for-your-experiments"></a>Acessar conjuntos de os seus experimentos

Use seus conjuntos de informações em seus experimentos de aprendizado de máquina para modelos de ML de treinamento. [Saiba mais sobre como treinar com conjuntos de dados](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Para obter exemplos e demonstrações de funcionalidade de conjuntos de valores abertos, consulte esses [blocos de anotações](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo com conjuntos de valores](../machine-learning/how-to-train-with-datasets.md).

* [Crie um conjunto de informações do Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



