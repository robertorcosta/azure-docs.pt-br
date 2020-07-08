---
title: Transformar dados usando a atividade de procedimento armazenado
description: Explica como usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um Banco de Dados SQL do Azure/Data Warehouse do Azure de um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 8543276a338b523a290fb131a8f1b7a55affbd98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85248965"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformar dados usando a atividade de procedimento armazenado do SQL Server no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-stored-proc-activity.md)
> * [Versão atual](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use atividades de transformação de dados em um [pipeline](concepts-pipelines-activities.md) do Data Factory para transformar e processar dados brutos em previsões e informações. A Atividade de Procedimento Armazenado é uma das atividades de transformação que o Data Factory dá suporte. Este artigo baseia-se no artigo sobre [dados de transformação](transform-data.md), que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte no Data Factory.

> [!NOTE]
> Se você é novo no Azure Data Factory, leia a [Introdução ao Azure Data Factory](introduction.md) e siga o tutorial: [Transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Use a Atividade de Procedimento Armazenado para invocar um procedimento armazenado em um dos seguintes armazenamentos de dados em sua empresa ou em uma VM (máquina virtual) do Azure: 

- Banco de Dados SQL do Azure
- Azure Synapse Analytics (antigo SQL Data Warehouse do Azure)
- Banco de Dados do SQL Server.  Se estiver usando o SQL Server, instale o Integration Runtime (auto-hospedado) no mesmo computador que hospeda o banco de dados ou em um computador separado com acesso ao banco de dados. O Integration Runtime (auto-hospedado) é um componente que conecta fontes de dados locais ou em uma VM do Azure aos serviços de nuvem de maneira segura e gerenciada. Confira o artigo de [tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

> [!IMPORTANT]
> Ao copiar dados para o Banco de Dados SQL do Azure ou o SQL Server, configure o **SqlSink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName**. Para obter detalhes sobre a propriedade, consulte as seguintes artigos sobre o conector: [Banco de Dados SQL do Azure](connector-azure-sql-database.md) e [SQL Server](connector-sql-server.md). Não há suporte para invocar um procedimento armazenado ao copiar dados em uma análise de Synapse do Azure (anteriormente Azure SQL Data Warehouse) usando uma atividade de cópia. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um SQL Data Warehouse. 
>
> Durante a cópia de dados de uma SQL Server do Azure ou do Azure Synapse Analytics (anteriormente chamado de SQL Data Warehouse do Azure), você pode configurar **sqlsource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando a propriedade **sqlReaderStoredProcedureName** . Para obter mais informações, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure Synapse Analytics (anteriormente chamado de SQL data warehouse do Azure)](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes da sintaxe
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenado:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A seguinte tabela descreve essas propriedades JSON:

| Property                  | Descrição                              | Obrigatório |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nome da atividade                     | Sim      |
| descrição               | Texto que descreve qual a utilidade da atividade | Não       |
| type                      | Para a atividade de procedimento armazenado, o tipo de atividade é **SqlServerStoredProcedure** | Sim      |
| linkedServiceName         | Referência ao **banco de dados SQL do Azure** ou ao **Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse)** ou **SQL Server** registrado como um serviço vinculado no data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| storedProcedureName       | Especifique o nome do procedimento armazenado para invocar. | Sim      |
| storedProcedureParameters | Especifique os valores para parâmetros de procedimento armazenado. Use `"param1": { "value": "param1Value","type":"param1Type" }` para passar os valores de parâmetro e seu tipo com suporte da fonte de dados. Se você precisar passar null para um parâmetro, use `"param1": { "value": null }` (tudo em letras minúsculas). | Não       |

## <a name="parameter-data-type-mapping"></a>Mapeamento de tipo de dados de parâmetro
O tipo de dados especificado para o parâmetro é o tipo de Azure Data Factory que é mapeado para o tipo de dados na fonte de dados que você está usando. Você pode encontrar os mapeamentos de tipo de dados para sua fonte de dados na área conectores. Alguns exemplos são

| fonte de dados          | Mapeamento de tipo de dados |
| ---------------------|-------------------|
| Azure Synapse Analytics (antigo SQL Data Warehouse do Azure) | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Banco de Dados SQL do Azure   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informações de Erro

Quando um procedimento armazenado falha e retorna detalhes do erro, não é possível capturar as informações de erro diretamente na saída da atividade. No entanto, o Data Factory bombeia todos os seus eventos de execução de atividade para o Azure Monitor. Entre os eventos que o Data Factory bombeia para o Azure Monitor, ele envia detalhes do erro para lá. Você pode, por exemplo, configurar alertas de e-mail desses eventos. Para obter mais informações, consulte [Alertar e monitorar os data factories usando o Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Atividade de execução em lotes do Machine Learning)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
