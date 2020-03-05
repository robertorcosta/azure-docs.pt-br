---
title: Opções de ingestão de dados
titleSuffix: Azure Machine Learning
description: Saiba mais sobre as opções de ingestão de dados para treinar seus modelos de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 35c133a4d9d95dc6e942d5b8c3e8d21debb9493b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268556"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Ingestão de dados no Azure Machine Learning

Neste artigo, você aprende os prós e contras das seguintes opções de ingestão de dados disponíveis com Azure Machine Learning. 

1. Pipelines de [Azure data Factory](#use-azure-data-factory)
2. [Azure Machine Learning SDK do Python](#use-the-python-sdk)

A ingestão de dados é o processo no qual os dados não estruturados são extraídos de uma ou várias fontes e, em seguida, preparados para o treinamento de modelos de aprendizado de máquina. Ele também é demorado, especialmente se feito manualmente, e se você tiver grandes quantidades de dados de várias fontes. Automatizar esse esforço libera recursos e garante que seus modelos usem os dados mais recentes e aplicáveis.

É recomendável que você avalie usando Azure Data Factory (ADF) inicialmente, pois ele é criado especificamente para extrair, carregar e transformar dados. Se você não puder atender aos seus requisitos usando o ADF, poderá usar o SDK do Python para desenvolver uma solução de código personalizada ou usar o ADF e o SDK do Python juntos para criar um fluxo de trabalho de ingestão de dados geral que atenda às suas necessidades.

## <a name="use-azure-data-factory"></a>Usar o Azure Data Factory

O [Azure data Factory](https://docs.microsoft.com/azure/data-factory/introduction) oferece suporte nativo para monitoramento de fonte de dados e gatilhos para pipelines de ingestão de dados.  

A tabela a seguir resume os prós e contras para usar Azure Data Factory para seus fluxos de trabalho de ingestão de dados.

|Vantagens|Desvantagens
---|---
Especificamente criado para extrair, carregar e transformar dados.|Atualmente oferece um conjunto limitado de tarefas de pipeline de Azure Data Factory 
Permite que você crie fluxos de trabalho controlados por dados para orquestrar a movimentação de dados e transformações em escala.|Caro de construir e manter. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) do Azure data Factory para obter mais informações.
Integrado com várias ferramentas do Azure, como [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) e [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Não executa scripts nativamente, em vez de se basear em computação separada para execuções de script 
Dá suporte nativo à ingestão de dados disparados por fonte de dados| 
Os processos de preparação de dados e treinamento de modelo são separados.|
Recurso de linhagem de dados inserida para fluxos de Azure Data Factory|
Fornece uma [interface do usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) de experiência de código baixa para abordagens sem scripts |

Essas etapas e o diagrama a seguir ilustram o fluxo de trabalho de ingestão de dados Azure Data Factory.

1. Efetuar pull dos dados de suas fontes
1. Transformar e salvar os dados em um contêiner de blob de saída, que serve como armazenamento de dados para Azure Machine Learning
1. Com os dados preparados armazenados, o pipeline de Azure Data Factory invoca um pipeline de Machine Learning de treinamento que recebe os dados preparados para treinamento de modelo


    ![Ingestão de dados do ADF](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>Usar o SDK do Python 

Com o [SDK do Python](https://docs.microsoft.com/python/api/overview/azure/ml), você pode incorporar tarefas de ingestão de dados em uma etapa [Azure Machine Learning pipeline](how-to-create-your-first-pipeline.md) .

A tabela a seguir resume os prós e con para usar o SDK e uma etapa de pipelines do ML para tarefas de ingestão de dados.

Vantagens| Desvantagens
---|---
Configurar seus próprios scripts Python | O não dá suporte nativo ao disparo de alterações da fonte de dados. Requer o aplicativo lógico ou implementações de função do Azure
Preparação de dados como parte de cada execução de treinamento de modelo|Requer habilidades de desenvolvimento para criar um script de ingestão de dados
Dá suporte a scripts de preparação de dados em vários destinos de computação, incluindo [Azure Machine Learning computação](concept-compute-target.md#azure-machine-learning-compute-managed) |Não fornece uma interface do usuário para criar o mecanismo de ingestão

No diagrama a seguir, o pipeline de Azure Machine Learning consiste em duas etapas: ingestão de dados e treinamento de modelo. A etapa de ingestão de dados abrange tarefas que podem ser realizadas usando bibliotecas Python e o SDK do Python, como a extração de dados de fontes locais/da Web e transformações básicas de dados, como valor ausente imputação. Em seguida, a etapa de treinamento usa os dados preparados como entrada para seu script de treinamento para treinar o modelo de aprendizado de máquina. 

![Pipeline do Azure + ingestão de dados do SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Saiba como automatizar e gerenciar os ciclos de vida de desenvolvimento de seus pipelines de ingestão de dados com o [Azure pipelines](how-to-cicd-data-ingestion.md).