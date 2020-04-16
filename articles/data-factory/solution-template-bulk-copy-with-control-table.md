---
title: Cópia em massa de um banco de dados usando tabela de controle
description: Aprenda a usar um modelo de solução para copiar dados em massa de um banco de dados usando uma tabela de controle externo para armazenar uma lista de tabelas de origem usando o Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414836"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Cópia em massa de um banco de dados com uma tabela de controle
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Para copiar dados de um data warehouse no Oracle Server, Netezza, Teradata ou SQL Server para o Azure SQL Data Warehouse, você tem que carregar grandes quantidades de dados de várias tabelas. Normalmente, os dados devem ser particionados em cada tabela para que você possa carregar linhas com vários segmentos em paralelo a partir de uma única tabela. Este artigo descreve um modelo a ser usado nesses cenários.

 >! NOTA Se você quiser copiar dados de um pequeno número de tabelas com volume de dados relativamente pequeno para o SQL Data Warehouse, é mais eficiente usar a [ferramenta Azure Data Factory Copy Data Data .](copy-data-tool.md) O modelo descrito neste artigo é mais do que você precisa para esse cenário.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo recupera uma lista de partições de banco de dados de origem para copiar de uma tabela de controle externo. Em seguida, ele itera sobre cada partição no banco de dados de origem e copia os dados para o destino.

O modelo contém três atividades:
- **A lookup** recupera a lista de partições de banco de dados sure de uma tabela de controle externo.
- **ForEach** obtém a lista de partição da atividade Procurar e itera cada partição para a atividade Copiar.
- **Copiar** cópias de cada partição da loja de banco de dados de origem para a loja de destino.

O modelo define os seguintes parâmetros:
- *Control_Table_Name* é a sua tabela de controle externo, que armazena a lista de partição para o banco de dados de origem.
- *Control_Table_Schema_PartitionID* é o nome da coluna na sua tabela de controle externo que armazena cada ID de partição. Certifique-se de que o ID de partição é único para cada partição no banco de dados de origem.
- *Control_Table_Schema_SourceTableName* é a sua tabela de controle externo que armazena cada nome de tabela do banco de dados de origem.
- *Control_Table_Schema_FilterQuery* é o nome da coluna em sua tabela de controle externo que armazena a consulta do filtro para obter os dados de cada partição no banco de dados de origem. Por exemplo, se você particiou os dados por ano, a consulta armazenada em cada linha pode ser semelhante a 'select * de datasource onde LastModifytime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59.999''.
- *Data_Destination_Folder_Path* é o caminho onde os dados são copiados para sua loja de destino (aplicável quando o destino que você escolhe é "Sistema de arquivos" ou "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* é o caminho da pasta raiz para onde os dados são copiados em sua loja de destino. 
- *Data_Destination_Directory* é o caminho do diretório sob a raiz onde os dados são copiados para sua loja de destino. 

Os três últimos parâmetros, que definem o caminho em sua loja de destino, só são visíveis se o destino escolhido for o armazenamento baseado em arquivos. Se você escolher "Azure Synapse Analytics (anteriormente SQL DW)" como a loja de destino, esses parâmetros não serão necessários. Mas os nomes das tabelas e o esquema no SQL Data Warehouse devem ser os mesmos do banco de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controle no SQL Server ou no Azure SQL Database para armazenar a lista de partição do banco de dados de origem para cópia em massa. No exemplo a seguir, há cinco partições no banco de dados de origem. Três partições são para o *datasource_table*, e duas são para o *project_table*. A coluna *LastModifytime* é usada para particionar os dados na tabela *datasource_table* do banco de dados de origem. A consulta usada para ler a primeira partição é 'select * de datasource_table onde LastModifytime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59.999''' Você pode usar uma consulta semelhante para ler dados de outras partições.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Vá para o **modelo Bulk Copy from Database.** Crie uma **nova** conexão com a tabela de controle externo que você criou na etapa 1.

    ![Criar uma nova conexão com a tabela de controle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Crie uma **nova** conexão com o banco de dados de origem do que você está copiando dados.

    ![Criar uma nova conexão com o banco de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Crie uma **nova** conexão com o armazenamento de dados de destino para o que você está copiando os dados.

    ![Criar uma nova conexão com o armazenamento de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selecione **Usar este modelo**.

6. Você vê o pipeline, como mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.

    ![Clique em **Debug**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Você vê resultados semelhantes ao seguinte exemplo:

    ![Revisar o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcional) Se você escolheu "Azure Synapse Analytics (anteriormente SQL DW)" como destino dos dados, você deve inserir uma conexão com o armazenamento Azure Blob para estágio, conforme exigido pelo SQL Data Warehouse Polybase. O modelo gerará automaticamente um caminho de contêiner para o armazenamento do Blob. Verifique se o contêiner foi criado após o tráfego do gasoduto.
    
    ![Configuração do Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)
