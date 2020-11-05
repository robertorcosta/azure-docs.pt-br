---
title: Ingestão de dados com o Azure Data Factory
titleSuffix: Azure Machine Learning
description: Conheça as opções disponíveis para criar um pipeline de ingestão de dados com Azure Data Factory e os benefícios de cada um.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 73850d8022618dd6544d19564e425288aff09771
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360625"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestão de dados com o Azure Data Factory

Neste artigo, você aprenderá sobre as opções disponíveis para criar um pipeline de ingestão de dados com Azure Data Factory (ADF). Esse pipeline é usado para ingerir dados para uso com Azure Machine Learning. Azure Data Factory permite que você extraia, transforme e carregue dados (ETL) facilmente. Depois que os dados tiverem sido transformados e carregados no armazenamento, eles poderão ser usados para treinar seus modelos de aprendizado de máquina.

A transformação de dados simples pode ser tratada com atividades e instrumentos do ADF nativo, como o [fluxo de dados](../data-factory/control-flow-execute-data-flow-activity.md). Quando se trata de cenários mais complicados, os dados podem ser processados com algum código personalizado. Por exemplo, Python ou código R.

Há várias técnicas comuns de usar Azure Data Factory para transformar dados durante a ingestão. Cada técnica tem prós e contras que determinam se é uma boa opção para um caso de uso específico:

| Técnica | Vantagens | Desvantagens |
| ----- | ----- | ----- |
| ADF + Azure Functions | Computação sem servidor de baixa latência</br>Funções com estado</br>Funções reutilizáveis | Só bom para processamento de execução curta |
| ADF + componente personalizado | Computação paralela em larga escala</br>Adequado para algoritmos pesados | Encapsulando código em um executável</br>Complexidade de lidar com dependências e e/s |
| ADF + Azure Databricks Notebook | Apache Spark</br>Ambiente de Python nativo | Pode ser caro</br>A criação de clusters inicialmente leva tempo e adiciona latência

## <a name="adf-with-azure-functions"></a>ADF com o Azure Functions

![O diagrama mostra um pipeline Azure Data Factory, com o Azure Function e executa o pipeline L M e um pipeline de Azure Machine Learning, com o modelo de treinamento e como eles interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions permite que você execute pequenas partes de código (funções) sem se preocupar com a infraestrutura do aplicativo. Nessa opção, os dados são processados com o código Python personalizado encapsulado em uma função do Azure. 

A função é invocada com a [atividade de função do Azure do ADF](../data-factory/control-flow-azure-function-activity.md). Essa abordagem é uma boa opção para transformações de dados leves. 

* Prós:
    * Os dados são processados em uma computação sem servidor com uma latência relativamente baixa
    * O pipeline do ADF pode invocar uma [função durável do Azure](../azure-functions/durable/durable-functions-overview.md) que pode implementar um fluxo de transformação de dados sofisticado 
    * Os detalhes da transformação de dados são dissociados para fora da função do Azure que podem ser reutilizados e invocados de outros locais
* Contras:
    * O Azure Functions deve ser criado antes do uso com o ADF
    * Azure Functions é bom somente para processamento de dados de execução curta

## <a name="adf-with-custom-component-activity"></a>ADF com atividade de componente Personalizada

![O diagrama mostra um pipeline de Azure Data Factory, com um componente personalizado e executa o pipeline L d e um pipeline de Azure Machine Learning, com o modelo de treinamento e como eles interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-customcomponent.png)

Nessa opção, os dados são processados com o código Python personalizado encapsulado em um executável. Ele é invocado com uma [atividade de componente Personalizada do ADF](../data-factory/transform-data-using-dotnet-custom-activity.md). Essa abordagem é uma melhor opção para dados grandes do que a técnica anterior.

* Prós:
    * Os dados são processados no pool [do lote do Azure](../batch/batch-technical-overview.md) , que fornece computação em larga escala paralela e de alto desempenho
    * Pode ser usado para executar algoritmos pesados e processar quantidades significativas de dados
* Contras:
    * O pool do lote do Azure deve ser criado antes do uso com o ADF
    * Sobre a engenharia relacionada à disposição do código Python em um executável. Complexidade de tratamento de dependências e parâmetros de entrada/saída

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF com Azure Databricks Notebook Python

![O diagrama mostra um pipeline Azure Data Factory, com Azure Databricks Python e executa o pipeline L M e um pipeline Azure Machine Learning, com o modelo de treinamento e como eles interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada em Apache Spark no Microsoft Cloud.

Nessa técnica, a transformação de dados é executada por um [Notebook Python](../data-factory/transform-data-using-databricks-notebook.md), em execução em um cluster Azure Databricks. Isso é provavelmente, a abordagem mais comum que aproveita todo o poder de um serviço de Azure Databricks. Ele foi projetado para processamento de dados distribuídos em escala.

* Prós:
    * Os dados são transformados no serviço de processamento de dados mais potente do Azure, cujo backup é feito por Apache Spark ambiente
    * Suporte nativo do Python juntamente com estruturas e bibliotecas de ciência de dados, incluindo TensorFlow, PyTorch e scikit-Learn
    * Não é necessário encapsular o código Python em funções ou módulos executáveis. O código funciona como está.
* Contras:
    * A infraestrutura de Azure Databricks deve ser criada antes do uso com o ADF
    * Pode ser caro dependendo da configuração do Azure Databricks
    * A rotação de clusters de computação do modo "frio" leva algum tempo que leva alta latência para a solução 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Consumindo dados em pipelines de Azure Machine Learning

![O diagrama mostra um pipeline de Azure Data Factory e um pipeline de Azure Machine Learning e como eles interagem com dados brutos e dados preparados. O pipeline Data Factory feeds de dados para o banco de dados preparado, que alimenta um armazenamento de dados, que alimenta conjuntos de dados no espaço de trabalho Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

Os dados transformados do pipeline do ADF são salvos no armazenamento de dados (como o blob do Azure). Azure Machine Learning pode acessar esses dados usando os [armazenamentos](./how-to-access-data.md#create-and-register-datastores) e os [DataSets](./how-to-create-register-datasets.md).

Cada vez que o pipeline do ADF é executado, os dados são salvos em um local diferente no armazenamento. Para passar o local para Azure Machine Learning, o pipeline do ADF chama um pipeline de Azure Machine Learning. Ao chamar o pipeline de ML, o local de dados e a ID de execução são enviados como parâmetros. O pipeline ML pode, então, criar um datastore/DataSet usando o local de dados. 

> [!TIP]
> Os conjuntos de dados [dão suporte ao controle de versão](./how-to-version-track-datasets.md), de modo que o pipeline ml pode registrar uma nova versão do conjunto de dados que aponta para o dado mais recente do pipeline do ADF.

Depois que os dados estiverem acessíveis por meio de um datastore ou DataSet, você poderá usá-los para treinar um modelo de ML. O processo de treinamento pode fazer parte do mesmo pipeline ML chamado do ADF. Ou pode ser um processo separado, como experimentação em um notebook Jupyter.

Como os conjuntos de dados dão suporte ao controle de versão e cada execução do pipeline cria uma nova versão, é fácil entender qual versão dos dados foi usada para treinar um modelo.

## <a name="next-steps"></a>Próximas etapas

* [Executar um bloco de anotações do databricks no Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Acessar dados nos serviços de armazenamento do Azure](./how-to-access-data.md#create-and-register-datastores)
* [Treinar modelos com conjuntos de os Azure Machine Learning](./how-to-train-with-datasets.md)
* [DevOps para um pipeline de ingestão de dados](./how-to-cicd-data-ingestion.md)