---
title: Dados no Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se conecta com segurança aos seus dados e usa esses dados para tarefas de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128485"
---
# <a name="data-access-in-azure-machine-learning"></a>Acesso a dados no Azure Machine Learning

O Azure Machine Learning facilita a conexão com seus dados na nuvem.  Ele fornece uma camada de abstração sobre o serviço de armazenamento subjacente, para que você possa acessar e trabalhar com segurança com seus dados sem ter que escrever código específico para o seu tipo de armazenamento. O Azure Machine Learning também fornece os seguintes recursos de dados:

*    Versão e rastreamento da linhagem de dados
*    Rotulagem de dados 
*    Monitoramento de dessincronização de dados
*    Interoperabilidade com Pandas e Spark DataFrames

## <a name="data-workflow"></a>Fluxo de trabalho de dados

Quando você estiver pronto para usar os dados em sua solução de armazenamento baseada em nuvem, recomendamos o seguinte fluxo de trabalho de entrega de dados. Esse fluxo de trabalho pressupõe que você tenha uma [conta de armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e dados em um serviço de armazenamento baseado em nuvem no Azure. 

1. Crie um [armazenamento de dados Azure Machine Learning](#datastores) para armazenar informações de conexão ao seu armazenamento Azure.

2. A partir desse datastore, crie um [conjunto de dados Azure Machine Learning](#datasets) para apontar para um arquivo específico no seu armazenamento subjacente. 

3. Para usar esse conjunto de dados em seu experimento de aprendizado de máquina, você pode
    1. Monte-o no alvo de computação do seu experimento para treinamento de modelos.

        **Ou** 

    1. Consuma-o diretamente em soluções de Machine Learning do Azure, como, corridas automatizadas de aprendizado de máquina (ML automatizados), pipelines de aprendizado de máquina ou o designer de Machine Learning do [Azure.](concept-designer.md)

4. Crie [monitores de conjunto de dados](#data-drift) para o conjunto de dados de saída do modelo para detectar a deriva de dados. 

5. Se o deriva de dados for detectado, atualize seu conjunto de dados de entrada e retreine seu modelo de acordo.

O diagrama a seguir fornece uma demonstração visual deste fluxo de trabalho recomendado.

![Diagrama de conceito de dados](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Armazenamentos de dados

Os datastores de aprendizado de máquina do Azure mantêm as informações de conexão seguras no armazenamento do Azure, para que você não precise cocitá-los em seus scripts. [Registre-se e crie um armazenamento de dados](how-to-access-data.md) para se conectar facilmente à sua conta de armazenamento e acesse os dados do seu serviço de armazenamento Azure subjacente. 

Serviços de armazenamento baseados em nuvem suportados no Azure que podem ser registrados como datastores:

+ Recipiente Azure Blob
+ Compartilhamento de arquivos do Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Banco de Dados SQL do Azure
+ Banco de Dados do Azure para PostgreSQL
+ Sistema de arquivos do Databricks
+ Banco de Dados do Azure para MySQL

## <a name="datasets"></a>Conjunto de dados

Os conjuntos de dados azure Machine Learning são referências que apontam para os dados em seu serviço de armazenamento. Eles não são cópias de seus dados, então nenhum custo extra de armazenamento é incorrido. Para interagir com seus dados no armazenamento, [crie um conjunto de dados](how-to-create-register-datasets.md) para embalar seus dados em um objeto consumível para tarefas de aprendizado de máquina. Registre o conjunto de dados no seu espaço de trabalho para compartilhá-lo e reutilizá-lo em diferentes experimentos sem complexidades de ingestão de dados.

Os conjuntos de dados podem ser criados a partir de arquivos locais, urls públicos, [conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/)ou serviços de armazenamento Do Zure através de armazenamentos de dados. Para criar um conjunto de dados a partir de um dataframe de pandas de memória, escreva os dados para um arquivo local, como um parquet, e crie seu conjunto de dados a partir desse arquivo.  

Nós suportamos 2 tipos de conjuntos de dados: 
+ Um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa os dados em um formato tabular analisando o arquivo fornecido ou a lista de arquivos. Você pode carregar um TabularDataset em um Pandas ou Spark DataFrame para mais manipulação e limpeza. Para obter uma lista completa de formatos de dados que você pode criar TabularDatasets, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ Um [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a arquivos únicos ou múltiplos em seus datastores ou URLs públicos. Você pode [baixar ou montar arquivos](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) referenciados por FileDatasets para o seu destino de computação.

Recursos adicionais de conjuntos de dados podem ser encontrados na seguinte documentação:

+ [Linhagem de conjunto](how-to-version-track-datasets.md) de dados de versão e faixa.
+ [Monitore seu conjunto de dados](how-to-monitor-datasets.md) para ajudar na detecção de deriva de dados.    

## <a name="work-with-your-data"></a>Trabalhe com seus dados

Com conjuntos de dados, você pode realizar uma série de tarefas de aprendizado de máquina através da integração perfeita com os recursos de Machine Learning do Azure. 

+ Crie um [projeto de rotulagem de dados](#label).
+ Modelos de aprendizagem de máquina de trem:
     + [experimentos automatizados ml](how-to-use-automated-ml-for-ml-models.md)
     + o [designer](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebooks](how-to-train-with-datasets.md)
     + [Gasodutos de aprendizado de máquina do Azure](how-to-create-your-first-pipeline.md)
+ Acesse conjuntos de dados para pontuação com [inferência em lote](how-to-use-parallel-run-step.md) em [pipelines de aprendizado de máquina](how-to-create-your-first-pipeline.md).
+ Configure um monitor de conjunto de dados para detecção de deriva de [dados.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Rotulagem de dados

Rotular grandes quantidades de dados costuma ser uma dor de cabeça nos projetos de aprendizado de máquina. Aqueles com um componente de visão computacional, como classificação de imagem ou detecção de objetos, geralmente requerem milhares de imagens e etiquetas correspondentes.

O Azure Machine Learning oferece uma localização central para criar, gerenciar e monitorar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, os rótulos e os membros da equipe, permitindo que você gerencie com mais eficiência as tarefas de rotulagem. Atualmente, as tarefas compatíveis são a classificação de imagem (seja ela de vários rótulos ou de várias classes) e a identificação de objetos usando caixas delimitadas.

Crie um [projeto de rotulagem de dados](how-to-create-labeling-projects.md)e produza um conjunto de dados para uso em experimentos de aprendizado de máquina.

<a name="drift"></a>

## <a name="data-drift"></a>Deriva de dados

No contexto da machine learning, o desvio de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, assim, monitorar a deriva de dados ajuda a detectar problemas de desempenho do modelo.

Consulte o [artigo Criar um monitor de conjunto de dados,](how-to-monitor-datasets.md) para saber mais sobre como detectar e alertar para a deriva de dados em novos dados em um conjunto de dados.

## <a name="next-steps"></a>Próximas etapas 

+ Crie um conjunto de dados no estúdio Azure Machine Learning ou com o Python SDK [usando essas etapas.](how-to-create-register-datasets.md)
+ Experimente exemplos de treinamento de conjunto de dados com [nossos cadernos de amostra](https://aka.ms/dataset-tutorial).
+ Para exemplos de deriva de dados, consulte este [tutorial de deriva de dados](https://aka.ms/datadrift-notebook).
