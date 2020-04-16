---
title: Cópia Delta de um banco de dados usando uma tabela de controle
description: Saiba como usar um modelo de solução para copiar linhas novas ou atualizadas de maneira incremental de um banco de dados apenas com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 01a6d796a9a8306da5bb707111b07786136a66cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414777"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Cópia Delta de um banco de dados com uma tabela de controle
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve um modelo disponível para carregar gradualmente linhas novas ou atualizadas de uma tabela de banco de dados para o Azure usando uma tabela de controle externo que armazena um valor de marca d'água alto.

Este modelo requer que o esquema do banco de dados de origem contenha uma coluna de carimbo de ponto-e-hora ou uma chave de incremento para identificar linhas novas ou atualizadas.

>[!NOTE]
> If you have a timestamp column in your source database to identify new or updated rows but you don't want to create an external control table to use for delta copy, you can instead use the [Azure Data Factory Copy Data tool](copy-data-tool.md) to get a pipeline. Essa ferramenta usa um tempo programado para o gatilho como uma variável para ler novas linhas do banco de dados de origem.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo primeiro recupera o valor antigo da marca d'água e o compara com o valor atual da marca d'água. Depois disso, ele copia apenas as alterações do banco de dados de origem, com base em uma comparação entre os dois valores de marca d'água. Finalmente, ele armazena o novo valor de marca d'água para uma tabela de controle externo para carregamento de dados delta da próxima vez.

O modelo contém três atividades:
- **A procuração** recupera o antigo valor de marca d'água, que é armazenado em uma tabela de controle externo.
- Outra **atividade de procurar** recupera o valor atual de marca d'água do banco de dados de origem.
- **Copiar** cópias apenas alterações do banco de dados de origem para a loja de destino. A consulta que identifica as alterações no banco de dados de origem é semelhante a "SELECT * DA Data_Source_Table ONDE TIMESTAMP_Column > "última marca d'água alta" e TIMESTAMP_Column <= "marca d'água atual".
- **SqlServerStoredProcedure** grava o valor atual de marca d'água em uma tabela de controle externo para cópia delta da próxima vez.

O modelo define os seguintes parâmetros:
- *Data_Source_Table_Name* é a tabela no banco de dados de origem da onde você deseja carregar dados.
- *Data_Source_WaterMarkColumn* é o nome da coluna na tabela de origem que é usada para identificar linhas novas ou atualizadas. O tipo desta coluna é tipicamente *datado,* *INT,* ou similar.
- *Data_Destination_Container* é o caminho raiz do local para onde os dados são copiados em sua loja de destino.
- *Data_Destination_Directory* é o caminho do diretório sob a raiz do local para onde os dados são copiados em sua loja de destino.
- *Data_Destination_Table_Name* é o lugar para onde os dados são copiados em sua loja de destino (aplicável quando "Azure Synapse Analytics (anteriormente SQL DW)" é selecionado como Destino de Dados).
- *Data_Destination_Folder_Path* é o lugar para onde os dados são copiados em sua loja de destino (aplicável quando "Sistema de arquivos" ou "Azure Data Lake Storage Gen1" é selecionado como Destino de Dados).
- *Control_Table_Table_Name* é a tabela de controle externo que armazena o valor da marca d'água.
- *Control_Table_Column_Name* é a coluna na tabela de controle externo que armazena o valor de marca d'água alta.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Explore a tabela de origem que deseja carregar e defina a coluna de marca d'água alta que pode ser usada para identificar linhas novas ou atualizadas. O tipo desta coluna pode ser *data-hora,* *INT,* ou similar. O valor desta coluna aumenta à medida que novas linhas são adicionadas. A partir da tabela de origem da amostra a seguir (data_source_table), podemos usar a coluna *LastModifytime* como a coluna de marca d'água alta.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Crie uma tabela de controle no SQL Server ou no Azure SQL Database para armazenar o valor de marca d'água alto para carregamento de dados delta. No exemplo a seguir, o nome do quadro de controle é *watermarktable*. Nesta tabela, *WatermarkValue* é a coluna que armazena o valor de marca d'água alta, e seu tipo é *data.*

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento armazenado na mesma instância do SQL Server ou do Azure SQL Database que você usou para criar a tabela de controle. O procedimento armazenado é usado para escrever o novo valor de marca d'água na tabela de controle externo para carregamento de dados delta da próxima vez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vá para a cópia Delta do modelo **de banco de dados.** Crie uma **nova** conexão com o banco de dados de origem do que você deseja copiar dados.

    ![Criar uma nova conexão para a tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Crie uma **nova** conexão com o armazenamento de dados de destino para o que você deseja copiar os dados.

    ![Criar uma nova conexão para a tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Crie uma **nova** conexão com a tabela de controle externo e o procedimento armazenado que você criou nas etapas 2 e 3.

    ![Criar uma nova conexão para o armazenamento de dados na tabela de controle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecione **Usar este modelo**.
    
8. Você vê o pipeline disponível, como mostrado no exemplo a seguir:
  
    ![Revisar o pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecione **Procedimento armazenado**. Para **nome do procedimento armazenado,** escolha **[dbo].[ update_watermark]**. Selecione **O parâmetro Importar**e, em seguida, **selecione Adicionar conteúdo dinâmico**.  

    ![Defina a atividade do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Escreva o conteúdo ** \@{atividade('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** e, em seguida, selecione **Concluir**.  

    ![Escreva o conteúdo para os parâmetros do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.

    ![Selecione **Debug**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Resultados semelhantes aos seguintes exemplos são exibidos:

    ![Revisar o resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. É possível criar novas linhas na tabela de origem. Aqui está a linguagem SQL de exemplo para criar novas linhas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Para executar o pipeline novamente, selecione **Depuração,** digite os **Parâmetros**e selecione **Concluir**.

    Você verá que apenas novas linhas foram copiadas para o destino.

15. (Opcional:) Se você selecionar o Azure Synapse Analytics (anteriormente SQL DW) como destino dos dados, você também deve fornecer uma conexão ao armazenamento Azure Blob para estágio, o que é exigido pelo SQL Data Warehouse Polybase. O modelo irá gerar um caminho de contêiner para você. Após o gerenciamento do gasoduto, verifique se o contêiner foi criado no armazenamento Blob.
    
    ![Configurar o Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Cópia em massa de um banco de dados usando uma tabela de controle com a Fábrica de Dados Do Azure](solution-template-bulk-copy-with-control-table.md)
- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
