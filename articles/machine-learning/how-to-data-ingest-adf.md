---
title: Ingestão de dados com fábrica de dados do Azure
titleSuffix: Azure Machine Learning
description: Saiba como construir um pipeline de ingestão de dados com a Fábrica de Dados Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274782"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestão de dados com fábrica de dados do Azure

Neste artigo, você aprende a construir um pipeline de ingestão de dados com a Azure Data Factory (ADF). Este pipeline é usado para ingerir dados para uso com o Azure Machine Learning. A Fábrica de Dados do Azure permite extrair, transformar e carregar facilmente dados (ETL). Uma vez que os dados foram transformados e carregados em armazenamento, ele pode ser usado para treinar seus modelos de aprendizado de máquina.

A simples transformação de dados pode ser tratada com atividades e instrumentos nativos do ADF, como [o fluxo de dados.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Quando se trata de cenários mais complicados, os dados podem ser processados com algum código personalizado. Por exemplo, código Python ou R.

Existem várias técnicas comuns de usar o Azure Data Factory para transformar dados durante a ingestão. Cada técnica tem prós e contras que determinam se é um bom ajuste para um caso de uso específico:

| Técnica | Vantagens | Desvantagens |
| ----- | ----- | ----- |
| Funções ADF + Azure | Baixa latência, computação sem servidor</br>Funções imponentes</br>Funções reutilizáveis | Só é bom para processamento em curto prazo |
| ADF + componente personalizado | Computação paralela em larga escala</br>Adequado para algoritmos pesados | Código de embrulho em um executável</br>Complexidade de lidar com dependências e IO |
| Notebook ADF + Azure Databricks | Apache Spark</br>Ambiente Python nativo | Pode ser caro</br>Criar clusters inicialmente leva tempo e adiciona latência

## <a name="adf-with-azure-functions"></a>ADF com funções do Azure

![adf-função](media/how-to-data-ingest-adf/adf-function.png)

O Azure Functions permite executar pequenos pedaços de código (funções) sem se preocupar com a infra-estrutura do aplicativo. Nesta opção, os dados são processados com código Python personalizado embrulhado em uma função Azure. 

A função é invocada com a [atividade aDF Azure Function](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Essa abordagem é uma boa opção para transformações de dados leves. 

* Prós:
    * Os dados são processados em um cálculo sem servidor com uma latência relativamente baixa
    * O pipeline ADF pode invocar uma [função Azure durável](/azure/azure-functions/durable/durable-functions-overview) que pode implementar um fluxo de transformação de dados sofisticado 
    * Os detalhes da transformação de dados são abstratos pela Função Azure que pode ser reutilizada e invocada de outros lugares
* Contras:
    * As funções do Azure devem ser criadas antes de serem usados com o ADF
    * Funções do Azure é bom apenas para processamento de dados em curto prazo

## <a name="adf-with-custom-component-activity"></a>ADF com atividade de componentes personalizados

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

Nesta opção, os dados são processados com código Python personalizado embrulhado em um executável. Ele é invocado com uma [atividade de componente personalizado ADF](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Esta abordagem é mais adequada para dados grandes do que a técnica anterior.

* Prós:
    * Os dados são processados no [pool Azure Batch,](https://docs.microsoft.com/azure/batch/batch-technical-overview) que fornece computação paralela e de alto desempenho em larga escala
    * Pode ser usado para executar algoritmos pesados e processar quantidades significativas de dados
* Contras:
    * A piscina azure Batch deve ser criada antes de usar com a ADF
    * Sobre a engenharia relacionada ao embrulho do código Python em um executável. Complexidade do manuseio de dependências e parâmetros de entrada/saída

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF com notebook Python Databricks Azure

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[O Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada em Apache Spark na nuvem da Microsoft.

Nesta técnica, a transformação de dados é realizada por um [notebook Python](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), executado em um cluster Azure Databricks. Esta é provavelmente a abordagem mais comum que aproveita todo o poder de um serviço Azure Databricks. Ele foi projetado para o processamento de dados distribuídos em escala.

* Prós:
    * Os dados são transformados no serviço azure de processamento de dados mais poderoso, que é apoiado pelo ambiente Apache Spark
    * Suporte nativo do Python, juntamente com frameworks e bibliotecas de ciência de dados, incluindo TensorFlow, PyTorch e scikit-learn
    * Não há necessidade de envolver o código Python em funções ou módulos executáveis. O código funciona como está.
* Contras:
    * A infra-estrutura do Azure Databricks deve ser criada antes de ser usada com o ADF
    * Pode ser caro dependendo da configuração do Azure Databricks
    * Girar clusters de computação do modo "frio" leva algum tempo que traz alta latência para a solução 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Consumindo dados em pipelines de Machine Learning do Azure

![conjunto de dados aml](media/how-to-data-ingest-adf/aml-dataset.png)

Os dados transformados do pipeline ADF são salvos no armazenamento de dados (como o Azure Blob). O Azure Machine Learning pode acessar esses dados usando [datastores](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) e [conjuntos de dados](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

Cada vez que o pipeline ADF é executado, os dados são salvos em um local diferente no armazenamento. Para passar a localização para o Azure Machine Learning, o pipeline aDF chama um pipeline de Machine Learning do Azure. Ao chamar o pipeline ML, a localização dos dados e o ID de execução são enviados como parâmetros. O pipeline ML pode então criar um conjunto de dados/conjunto de dados usando a localização dos dados. 

> [!TIP]
> Os conjuntos de dados [suportam a versão](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), para que o pipeline ML possa registrar uma nova versão do conjunto de dados que aponta para os dados mais recentes do pipeline ADF.

Uma vez que os dados são acessíveis através de um datastore ou conjunto de dados, você pode usá-los para treinar um modelo ML. O processo de treinamento pode fazer parte do mesmo gasoduto ML que é chamado de ADF. Ou pode ser um processo separado, como experimentação em um caderno jupyter.

Uma vez que os conjuntos de dados suportam a versão, e cada execução do pipeline cria uma nova versão, é fácil entender qual versão dos dados foi usada para treinar um modelo.

## <a name="next-steps"></a>Próximas etapas

* [Execute um notebook Databricks na Fábrica de Dados do Azure](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Dados de acesso em serviços de armazenamento do Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Treinar modelos com conjuntos de dados no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps para um pipeline de ingestão de dados](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

