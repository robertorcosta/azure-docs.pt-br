---
title: Opções de ingestão de dados
titleSuffix: Azure Machine Learning
description: Conheça as opções de ingestão de dados para treinar seus modelos de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086883"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Ingestão de dados no Azure Machine Learning

Neste artigo, você aprende os prós e contras das seguintes opções de ingestão de dados disponíveis com o Azure Machine Learning. 

1. [Gasodutos da Fábrica de Dados Azure](#use-azure-data-factory)
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

A ingestão de dados é o processo no qual dados não estruturados são extraídos de uma ou múltiplas fontes e, em seguida, preparados para o treinamento de modelos de aprendizagem de máquina. Também é um tempo intensivo, especialmente se feito manualmente, e se você tem grandes quantidades de dados de várias fontes. Automatizar esse esforço libera recursos e garante que seus modelos usem os dados mais recentes e aplicáveis.

O Azure Data Factory (ADF) é projetado especificamente para extrair, carregar e transformar dados, no entanto, o Python SDK permite que você desenvolva uma solução de código personalizada para tarefas básicas de ingestão de dados. Se nenhum dos dois é exatamente o que você precisa, você também pode usar o ADF e o Python SDK juntos para criar um fluxo de trabalho global de ingestão de dados que atenda às suas necessidades. 

## <a name="use-azure-data-factory"></a>Usar o Azure Data Factory

[A Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) oferece suporte nativo para monitoramento de fontes de dados e gatilhos para pipelines de ingestão de dados.  

A tabela a seguir resume os prós e contras para o uso da Fábrica de Dados Do Azure para seus fluxos de trabalho de ingestão de dados.

|Vantagens|Desvantagens
---|---
Especificamente construído para extrair, carregar e transformar dados.|Atualmente oferece um conjunto limitado de tarefas de pipeline da Fábrica de Dados Do Azure 
Permite criar fluxos de trabalho orientados a dados para orquestrar movimentação de dados e transformações em escala.|Caro para construir e manter. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) da Azure Data Factory para obter mais informações.
Integrado com várias ferramentas do Azure, como [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) e [Funções Azure](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Não executa scripts nativamente, em vez disso, depende de computação separada para execução de scripts 
O suporte nativo à fonte de dados desencadeou a ingestão de dados| 
Os processos de preparação de dados e treinamento de modelos são separados.|
Recurso de linhagem de dados incorporado satisfaz fluxos de dados do Azure Data Factory|
Fornece uma [interface de usuário](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) com experiência de baixo código para abordagens sem script |

Essas etapas e o diagrama a seguir ilustram o fluxo de trabalho de ingestão de dados da Azure Data Factory.

1. Puxe os dados de suas fontes
1. Transforme e salve os dados em um recipiente blob de saída, que serve como armazenamento de dados para o Azure Machine Learning
1. Com dados preparados armazenados, o pipeline da Fábrica de Dados do Azure invoca um pipeline de aprendizado de máquina de treinamento que recebe os dados preparados para treinamento de modelos


    ![Ingestão de dados ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Saiba como construir um pipeline de ingestão de dados para Machine Learning com [a Fábrica de Dados Do Azure](how-to-data-ingest-adf.md).

## <a name="use-the-python-sdk"></a>Usar o SDK do Python 

Com o [Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml)você pode incorporar tarefas de ingestão de dados em um passo [de pipeline de aprendizado de máquina do Azure.](how-to-create-your-first-pipeline.md)

A tabela a seguir resume os prós e contra para o uso do SDK e uma etapa de pipelines ML para tarefas de ingestão de dados.

Vantagens| Desvantagens
---|---
Configure seus próprios scripts Python | Não suporta nativamente o acionamento da alteração da fonte de dados. Requer implementações de Funções logicapp ou azure function
Preparação de dados como parte de cada execução de treinamento modelo|Requer habilidades de desenvolvimento para criar um script de ingestão de dados
Suporta scripts de preparação de dados em vários alvos de computação, incluindo [a computação azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) |Não fornece uma interface de usuário para criar o mecanismo de ingestão

No diagrama a seguir, o pipeline azure Machine Learning consiste em duas etapas: ingestão de dados e treinamento de modelos. A etapa de ingestão de dados abrange tarefas que podem ser realizadas usando bibliotecas Python e o Python SDK, como extrair dados de fontes locais/web e transformações básicas de dados, como a falta de imputação de valor. A etapa de treinamento então usa os dados preparados como entrada para o seu script de treinamento para treinar seu modelo de aprendizado de máquina. 

![Pipeline Azure + Ingestão de dados SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como construir um pipeline de ingestão de dados para Machine Learning com [a Fábrica de Dados Do Azure](how-to-data-ingest-adf.md)
* Saiba como automatizar e gerenciar os ciclos de vida de desenvolvimento de seus pipelines de ingestão de dados com [a Azure Pipelines](how-to-cicd-data-ingestion.md).
