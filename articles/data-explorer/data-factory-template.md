---
title: Copiar em massa de um banco de dados para o Azure Data Explorer usando o modelo azure Data Factory
description: Neste artigo, você aprende a usar um modelo de Fábrica de Dados do Azure para copiar em massa de um banco de dados para o Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293548"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Copiar em massa de um banco de dados para o Azure Data Explorer usando o modelo azure Data Factory 

O Azure Data Explorer é um serviço de análise de dados rápido, totalmente gerenciado. Ele oferece análise em tempo real em grandes volumes de dados que são transmitidos de muitas fontes, como aplicativos, sites e dispositivos IoT. 

Para copiar dados de um banco de dados no Oracle Server, Netezza, Teradata ou SQL Server para o Azure Data Explorer, você tem que carregar grandes quantidades de dados de várias tabelas. Normalmente, os dados devem ser particionados em cada tabela para que você possa carregar linhas com vários segmentos em paralelo a partir de uma única tabela. Este artigo descreve um modelo a ser usado nesses cenários.

[Os modelos de fábrica de dados do Azure](/azure/data-factory/solution-templates-introduction) são gasodutos predefinidos da Fábrica de Dados. Esses modelos podem ajudá-lo a começar rapidamente com a Fábrica de Dados e reduzir o tempo de desenvolvimento em projetos de integração de dados. 

Você cria o *modelo Bulk Copy from Database to Azure Data Explorer* usando as atividades *Lookup* e *ForEach.* Para uma cópia de dados mais rápida, você pode usar o modelo para criar muitos pipelines por banco de dados ou por tabela. 

> [!IMPORTANT]
> Certifique-se de usar a ferramenta apropriada para a quantidade de dados que deseja copiar.
> * Use o *modelo Bulk Copy from Database to Azure Data Explorer* para copiar grandes quantidades de dados de bancos de dados, como servidor SQL e Google BigQuery, para o Azure Data Explorer. 
> * Use a [*ferramenta Data Factory Copy Data*](data-factory-load-data.md) para copiar algumas tabelas com pequenas ou moderadas quantidades de dados no Azure Data Explorer. 

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md).
* [Crie uma fábrica de dados](data-factory-load-data.md#create-a-data-factory).
* Uma fonte de dados em um banco de dados.

## <a name="create-controltabledataset"></a>Criar controltableDataset

*ControlTableDataset* indica quais dados serão copiados da fonte para o destino no pipeline. O número de linhas indica o número total de pipelines necessários para copiar os dados. Você deve definir ControlTableDataset como parte do banco de dados de origem.

Um exemplo do formato da tabela de origem do SQL Server é mostrado no seguinte código:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Os elementos de código são descritos na tabela a seguir:

|Propriedade  |Descrição  | Exemplo
|---------|---------| ---------|
|PartitionId   |  A ordem de cópia | 1  |  
|Fonte Query   |  A consulta que indica quais dados serão copiados durante o tempo de execução do pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|Nome adxtable  |  O nome da tabela de destino | MyAdxTable       |  

Se o controltableDataset estiver em um formato diferente, crie um conjunto de dados de controltable para o seu formato.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Use a cópia em massa do modelo Banco de Dados para O Dozure Data Explorer

1. No **painel Vamos começar,** selecione **Criar pipeline a partir do modelo** para abrir o painel da galeria **Template.**

    ![O painel "Vamos começar" da fábrica de dados do Azure](media/data-factory-template/adf-get-started.png)

1. Selecione o **modelo Bulk Copy from Database to Azure Data Explorer.**
 
    ![O modelo "Cópia em massa do banco de dados para o Azure Data Explorer"](media/data-factory-template/pipeline-from-template.png)

1.  No **painel Bulk Copy from Database to Azure Data Explorer,** em **Inputs do usuário,** especifique seus conjuntos de dados fazendo o seguinte: 

    a. Na lista de paradas **do ControlTableDataset,** selecione o serviço vinculado à tabela de controle que indica quais dados são copiados da origem para o destino e onde serão colocados no destino. 

    b. Na lista de paradas **do SourceDataset,** selecione o serviço vinculado ao banco de dados de origem. 

    c. Na lista de paradas do **AzureDataExplorerTable,** selecione a tabela Azure Data Explorer. Se o conjunto de dados não existir, [crie o serviço vinculado ao Azure Data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) para adicionar o conjunto de dados.

    d. Selecione **Usar este modelo**.

    ![O painel "Bulk Copy from Database to Azure Data Explorer"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selecione uma área na tela, fora das atividades, para acessar o pipeline de modelos. Selecione a guia **Parâmetros** para inserir os parâmetros da tabela, incluindo **Nome** (nome da tabela de controle) e **Valor Padrão** (nomes de coluna).

    ![Parâmetros de pipeline](media/data-factory-template/pipeline-parameters.png)

1.  Em **'Procurar',** selecione **GetPartitionList** para exibir as configurações padrão. A consulta é criada automaticamente.
1.  Selecione a atividade Comando, **ForEachPartition,** selecione a guia **Configurações** e faça o seguinte:

    a. Na caixa **de contagem de lotes,** digite um número de 1 a 50. Esta seleção determina o número de pipelines executados em paralelo até que o número de linhas *ControlTableDataset* seja atingido. 

    b. Para garantir que os lotes do pipeline sejam executados em paralelo, *não* selecione a caixa de seleção **seqüencial.**

    ![Configurações forEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > A melhor prática é executar muitos pipelines em paralelo para que seus dados possam ser copiados mais rapidamente. Para aumentar a eficiência, particione os dados na tabela de origem e aloque uma partição por pipeline, de acordo com a data e tabela.

1. Selecione **Validar tudo** para validar o pipeline da Fábrica de Dados do Azure e, em seguida, visualize o resultado no painel **de saída de validação de pipeline.**

    ![Validar pipelines de modelos](media/data-factory-template/validate-template-pipelines.png)

1. Se necessário, selecione **Depurar**e, em seguida, **selecione Adicionar gatilho** para executar o pipeline.

    ![Os botões "Debug" e "Run pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

Agora você pode usar o modelo para copiar eficientemente grandes quantidades de dados de seus bancos de dados e tabelas.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [copiar dados para o Azure Data Explorer usando o Azure Data Factory](data-factory-load-data.md).
* Saiba mais sobre o [conector Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) na Fábrica de Dados Do Azure.
* Saiba mais sobre [as consultas do Azure Data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.






