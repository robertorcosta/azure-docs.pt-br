---
title: Proteger o acesso a dados na nuvem
titleSuffix: Azure Machine Learning
description: Saiba como se conectar com segurança aos seus dados a partir de Azure Machine Learning e como usar DataSets e datastores para tarefas de ML. Os armazenamentos de dados podem ser armazenados em um blob do Azure, Azure Data Lake Gen 1 & 2, banco de dados SQL, databricks,...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.openlocfilehash: 614cc866529cd4ead8a6ea798526d59aff13d4d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144479"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Proteger o acesso a dados no Azure Machine Learning

Azure Machine Learning facilita a conexão com seus dados na nuvem.  Ele fornece uma camada de abstração sobre o serviço de armazenamento subjacente, para que você possa acessar e trabalhar com segurança com seus dados sem precisar escrever código específico ao seu tipo de armazenamento. O Azure Machine Learning também fornece os seguintes recursos de dados:

*    Controle de versão e acompanhamento de linhagem de dados
*    Rotulamento de dados 
*    Monitoramento de dessincronização de dados
*    Interoperabilidade com pandas e Spark dataframes

## <a name="data-workflow"></a>Fluxo de trabalho de dados

Quando você estiver pronto para usar os dados em sua solução de armazenamento baseada em nuvem, recomendamos o seguinte fluxo de trabalho de entrega de dados. Este fluxo de trabalho pressupõe que você tenha uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) e dados em um serviço de armazenamento baseado em nuvem no Azure. 

1. Crie um [repositório](#datastores) de dados Azure Machine Learning para armazenar informações de conexão para o armazenamento do Azure.

2. Nesse datastore, crie um [conjunto de Azure Machine Learning](#datasets) para apontar para um ou mais arquivos em seu armazenamento subjacente. 

3. Para usar esse conjunto de informações em seu experimento do Machine Learning, você pode
    1. Monte-o no destino de computação do experimento para o treinamento do modelo.

        **OR** 

    1. Consuma-o diretamente em soluções de Azure Machine Learning como, execuções de experimento automatizadas do Machine Learning (ML automatizado), pipelines de aprendizado de máquina ou [Designer de Azure Machine Learning](concept-designer.md).

4. Crie [monitores de conjunto](#data-drift) de dados para seu conjunto de dados de saída de modelo para detectar descompasso de dado 

5. Se a descompasso de dados for detectada, atualize o DataSet de entrada e retreine seu modelo de acordo.

O diagrama a seguir fornece uma demonstração visual desse fluxo de trabalho recomendado.

![Diagrama de conceito de dados](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Armazenamentos de dados

Azure Machine Learning armazenamentos de dados mantêm com segurança as informações de conexão para o armazenamento do Azure, de modo que você não precisará codificar em seus scripts. [Registre e crie um armazenamento](how-to-access-data.md) de dados para se conectar facilmente à sua conta de armazenamento e acesse os dados em seu serviço de armazenamento do Azure subjacente. 

Serviços de armazenamento baseados em nuvem com suporte no Azure que podem ser registrados como repositórios de armazenamento:

+ Contêiner de blob do Azure
+ Compartilhamento de arquivos do Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Banco de Dados SQL do Azure
+ Banco de Dados do Azure para PostgreSQL
+ Sistema de arquivos do Databricks
+ Banco de Dados do Azure para MySQL

## <a name="datasets"></a>Conjunto de dados

Azure Machine Learning DataSets são referências que apontam para os dados em seu serviço de armazenamento. Eles não são cópias dos seus dados, portanto, nenhum custo de armazenamento extra é cobrado. Para interagir com os seus dados no armazenamento, [crie um conjunto](how-to-create-register-datasets.md) de dados para empacotar seus dados em um objeto consumível para tarefas de aprendizado de máquina. Registre o conjunto de dados em seu espaço de trabalho para compartilhá-lo e reutilizá-lo em experimentos diferentes sem complexidades de ingestão de dados.

Os conjuntos de valores podem ser criados a partir de arquivos locais, de URLs públicas, de conjuntos de de [Azure abertos](https://azure.microsoft.com/services/open-datasets/)ou de serviços de armazenamento do Azure por meio de armazenamentos. Para criar um conjunto de dados a partir de um dataframe do pandas na memória, grave-os em um arquivo local, como um parquet, e crie seu conjunto de dado a partir desse arquivo.  

Damos suporte a dois tipos de conjuntos de valores: 
+ Um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo ou a lista de arquivos fornecida. Você pode carregar um TabularDataset em um dataframe do pandas ou Spark para manipulação e limpeza adicionais. Para obter uma lista completa dos formatos de dados dos quais você pode criar TabularDatasets, consulte a [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ Um [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Você pode [baixar ou montar arquivos](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) referenciados por filedatasets em seu destino de computação.

Os recursos adicionais de conjuntos de documentos podem ser encontrados na seguinte documentação:

+ Linhagem [e faixa de conjunto de](how-to-version-track-datasets.md) DataSet.
+ [Monitore seu conjunto](how-to-monitor-datasets.md) de dados para ajudá-lo com a detecção de descompasso.    

## <a name="work-with-your-data"></a>Trabalhe com seus dados

Com conjuntos de informações, você pode realizar várias tarefas de aprendizado de máquina por meio de integração direta com recursos de Azure Machine Learning. 

+ Crie um [projeto de rotulagem de dados](#label).
+ Treinar modelos de aprendizado de máquina:
     + [experimentos de ML automatizados](how-to-use-automated-ml-for-ml-models.md)
     + o [Designer](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebooks](how-to-train-with-datasets.md)
     + [Pipelines de Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Acesse conjuntos de informações para pontuação com [inferência de lote](how-to-use-parallel-run-step.md) em [pipelines de Machine Learning](how-to-create-your-first-pipeline.md).
+ Configure um monitor de conjunto de dados para a detecção de [descompasso de dados](#drift) .

<a name="label"></a>

## <a name="data-labeling"></a>Rotulamento de dados

Rotular grandes quantidades de dados costuma ser uma dor de cabeça nos projetos de aprendizado de máquina. Aqueles com um componente da pesquisa Visual computacional, como classificação de imagem ou detecção de objetos, geralmente exigem milhares de imagens e rótulos correspondentes.

O Azure Machine Learning oferece uma localização central para criar, gerenciar e monitorar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, os rótulos e os membros da equipe, permitindo que você gerencie com mais eficiência as tarefas de rotulagem. Atualmente, as tarefas compatíveis são a classificação de imagem (seja ela de vários rótulos ou de várias classes) e a identificação de objetos usando caixas delimitadas.

Crie um [projeto de rotulagem de dados](how-to-create-labeling-projects.md)e a saída de um DataSet para uso em experimentos de aprendizado de máquina.

<a name="drift"></a>

## <a name="data-drift"></a>Descompasso de dados

No contexto do Machine Learning, a descompasso de dados é a alteração nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É um dos principais motivos pelos quais a precisão do modelo diminui ao longo do tempo, o que monitora a descompasso de dados ajuda a detectar problemas de desempenho do modelo.

Consulte o artigo [criar um conjunto de](how-to-monitor-datasets.md) dados para saber mais sobre como detectar e alertar a descompasso de dados em novos dados em um DataSet.

## <a name="next-steps"></a>Próximas etapas 

+ Crie um conjunto de um DataSet no Azure Machine Learning Studio ou com o SDK do Python [usando estas etapas.](how-to-create-register-datasets.md)
+ Experimente exemplos de treinamento de conjunto de [anotações com nossos notebooks de exemplo](https://aka.ms/dataset-tutorial).
+ Para obter exemplos de descompasso de dados, consulte este [tutorial de descompasso de dados](https://aka.ms/datadrift-notebook).
