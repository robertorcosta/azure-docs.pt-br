---
title: Migrar dados do Amazon S3 para o Azure Data Lake Storage Gen2
description: Aprenda a usar um modelo de solução para migrar dados do Amazon S3 usando uma tabela de controle externo para armazenar uma lista de partição no AWS S3 com o Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414771"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrar dados do Amazon S3 para o Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Use os modelos para migrar petabytes de dados que consistem em centenas de milhões de arquivos do Amazon S3 para o Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Se você quiser copiar um pequeno volume de dados do AWS S3 para o Azure (por exemplo, menos de 10 TB), é mais eficiente e fácil de usar a [ferramenta Azure Data Factory Copy Data Data Data .](copy-data-tool.md) O modelo descrito neste artigo é mais do que você precisa.

## <a name="about-the-solution-templates"></a>Sobre os modelos de soluções

A partição de dados é recomendada especialmente ao migrar mais de 10 TB de dados. Para particionar os dados, aproveite a configuração 'prefixo' para filtrar as pastas e arquivos no Amazon S3 pelo nome e, em seguida, cada trabalho de cópia do ADF pode copiar uma partição de cada vez. Você pode executar vários trabalhos de cópia do ADF simultaneamente para obter um melhor throughput.

A migração de dados normalmente requer migração de dados históricos únicas, além de sincronizar periodicamente as alterações do AWS S3 para o Azure. Há dois modelos abaixo, onde um modelo abrange uma migração de dados históricos e outro modelo abrange sincronizar as alterações do AWS S3 para o Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para que o modelo migre dados históricos do Amazon S3 para o Azure Data Lake Storage Gen2

Este modelo *(nome do modelo: migrar dados históricos do AWS S3 para o Azure Data Lake Storage Gen2*) assume que você escreveu uma lista de partição em uma tabela de controle externo no Banco de Dados Azure SQL. Assim, ele usará uma atividade *de Procurar* para recuperar a lista de partição da tabela de controle externo, iterar sobre cada partição e fazer com que cada trabalho de cópia do ADF copie uma partição de cada vez. Uma vez concluído qualquer trabalho de cópia, ele usa a atividade *Procedimento Armazenado* para atualizar o status de copiar cada partição na tabela de controle.

O modelo contém cinco atividades:
- **A pesquisa** recupera as partições que não foram copiadas para o Azure Data Lake Storage Gen2 de uma tabela de controle externo. O nome da tabela é *s3_partition_control_table* e a consulta para carregar dados da tabela é *"SELECT PartitionPrefix FROM s3_partition_control_table WHERE SuccessOrFailure = 0"*.
- **ForEach** obtém a lista de partição da atividade *Procurar* e itera cada partição para a atividade *TriggerCopy.* Você pode definir o *batchCount* para executar vários trabalhos de cópia do ADF simultaneamente. Nós definimos 2 neste modelo.
- **ExecutePipeline** *executa copyfolderpartitionFromS3* pipeline. A razão pela qual criamos outro pipeline para fazer com que cada cópia de trabalho de cópia de uma partição é porque isso tornará fácil refazer o trabalho de cópia com falha para recarregar essa partição específica novamente do AWS S3. Todos os outros trabalhos de cópia carregando outras partições não serão impactados.
- **Copiar** cópias de cada partição do AWS S3 para o Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** atualiza o status de copiar cada partição na tabela de controle.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o nome do seu balde no AWS S3 de onde você deseja migrar dados. Se você quiser migrar dados de vários baldes no AWS S3, você pode adicionar mais uma coluna em sua tabela de controle externo para armazenar o nome do balde para cada partição, e também atualizar seu pipeline para recuperar dados dessa coluna de acordo.
- **Azure_Storage_fileSystem** é o nome do seu arquivoSistema no Azure Data Lake Storage Gen2 para onde você deseja migrar dados.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para que o modelo copie arquivos alterados apenas do Amazon S3 para o Azure Data Lake Storage Gen2

Este modelo *(nome do modelo: copie dados delta do AWS S3 para o Azure Data Lake Storage Gen2*) usa LastModifiedTime de cada arquivo para copiar os arquivos novos ou atualizados apenas do AWS S3 para o Azure. Esteja ciente se seus arquivos ou pastas já foram particionados com informações de timeslice como parte do arquivo ou nome da pasta no AWS S3 (por exemplo, /yyyy/mm/dd/file.csv), você pode ir a este [tutorial](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) para obter a abordagem mais performática para carregamento incremental de novos arquivos. Este modelo assume que você escreveu uma lista de partição em uma tabela de controle externo no Banco de Dados SQL do Azure. Assim, ele usará uma atividade *de Procurar* para recuperar a lista de partição da tabela de controle externo, iterar sobre cada partição e fazer com que cada trabalho de cópia do ADF copie uma partição de cada vez. Quando cada trabalho de cópia começa a copiar os arquivos do AWS S3, ele conta com a propriedade LastModifiedTime para identificar e copiar apenas os arquivos novos ou atualizados. Uma vez concluído qualquer trabalho de cópia, ele usa a atividade *Procedimento Armazenado* para atualizar o status de copiar cada partição na tabela de controle.

O modelo contém sete atividades:
- **A lookup** recupera as partições de uma tabela de controle externa. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"selecionar Partição distintaPrefix de s3_partition_delta_control_table"*.
- **ForEach** obtém a lista de partição da atividade *'Procurar'* e itera cada partição para a atividade *TriggerDeltaCopy.* Você pode definir o *batchCount* para executar vários trabalhos de cópia do ADF simultaneamente. Nós definimos 2 neste modelo.
- **ExecutePipeline** executa o pipeline *DeltaCopyFolderPartitionFromS3.* A razão pela qual criamos outro pipeline para fazer com que cada cópia de trabalho de cópia de uma partição é porque isso tornará fácil refazer o trabalho de cópia com falha para recarregar essa partição específica novamente do AWS S3. Todos os outros trabalhos de cópia carregando outras partições não serão impactados.
- **A lookup** recupera o tempo de execução do último trabalho de cópia da tabela de controle externo para que os arquivos novos ou atualizados possam ser identificados via LastModifiedTime. O nome da tabela é *s3_partition_delta_control_table* e a consulta para carregar dados da tabela é *"select max (JobRunTime) as LastModifiedTime from s3_partition_delta_control_table where PartitionPrefix = '@{pipeline().parameters.prefixStr}' e SuccessOrFailure = 1"*.
- **Copiar** arquivos novos ou alterados apenas para cada partição do AWS S3 para o Azure Data Lake Storage Gen2. A propriedade do *DatetimeStart modificado* é definida como o último tempo de execução do trabalho de cópia. A propriedade do *DatetimeEnd modificado* é definida como o tempo de execução do trabalho de cópia atual. Esteja ciente de que a hora é aplicada ao fuso horário UTC.
- **SqlServerStoredProcedure** atualiza o status de copiar cada partição e copiar o tempo de execução na tabela de controle quando for bem sucedido. A coluna successorfailure está definida como 1.
- **SqlServerStoredProcedure** atualiza o status de copiar cada partição e copiar o tempo de execução na tabela de controle quando ele falha. A coluna SuccessOrFailure está definida como 0.

O modelo contém dois parâmetros:
- **AWS_S3_bucketName** é o nome do seu balde no AWS S3 de onde você deseja migrar dados. Se você quiser migrar dados de vários baldes no AWS S3, você pode adicionar mais uma coluna em sua tabela de controle externo para armazenar o nome do balde para cada partição, e também atualizar seu pipeline para recuperar dados dessa coluna de acordo.
- **Azure_Storage_fileSystem** é o nome do seu arquivoSistema no Azure Data Lake Storage Gen2 para onde você deseja migrar dados.

## <a name="how-to-use-these-two-solution-templates"></a>Como usar esses dois modelos de soluções

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para que o modelo migre dados históricos do Amazon S3 para o Azure Data Lake Storage Gen2 

1. Crie uma tabela de controle no Banco de Dados SQL do Azure para armazenar a lista de partição do AWS S3. 

    > [!NOTE]
    > O nome da mesa é s3_partition_control_table.
    > O esquema da tabela de controle é PartitionPrefix e SuccessOrFailure, onde PartitionPrefix é a configuração de prefixo em S3 para filtrar as pastas e arquivos no Amazon S3 pelo nome, e SuccessOrFailure é o status de copiar cada partição: 0 significa que esta partição não foi copiada para o Azure e 1 significa que esta partição foi copiada para o Azure com sucesso.
    > Existem 5 partições definidas na tabela de controle e o status padrão de copiar cada partição é 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Crie um procedimento armazenado no mesmo banco de dados SQL do Azure para a tabela de controle. 

    > [!NOTE]
    > O nome do Procedimento Armazenado é sp_update_partition_success. Ele será invocado pela atividade SqlServerStoredProcedure em seu pipeline ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Vá para o **modelo Migrar dados históricos do AWS S3 para o modelo Azure Data Lake Storage Gen2.** Insira as conexões para sua tabela de controle externo, AWS S3 como o armazenamento de origem de dados e Azure Data Lake Storage Gen2 como a loja de destino. Esteja ciente de que a tabela de controle externo e o procedimento armazenado são referência para a mesma conexão.

    ![Crie uma nova conexão](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Você vê que os 2 pipelines e 3 conjuntos de dados foram criados, como mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.

    ![Clique em **Debug**](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Você vê resultados semelhantes ao seguinte exemplo:

    ![Revisar o resultado](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Para que o modelo copie arquivos alterados apenas do Amazon S3 para o Azure Data Lake Storage Gen2

1. Crie uma tabela de controle no Banco de Dados SQL do Azure para armazenar a lista de partição do AWS S3. 

    > [!NOTE]
    > O nome da mesa é s3_partition_delta_control_table.
    > O esquema da tabela de controle é PartitionPrefix, JobRunTime e SuccessOrFailure, onde PartitionPrefix é a configuração de prefixo em S3 para filtrar as pastas e arquivos no Amazon S3 pelo nome, JobRunTime é o valor de data-hora quando os trabalhos de cópia são executados, e SuccessOrFailure é o status de copiar cada partição: 0 significa que esta partição não foi copiada para o Azure e 1 significa que esta partição foi copiada para a zure com sucesso.
    > Existem 5 partições definidas na tabela de controle. O valor padrão do JobRunTime pode ser o momento em que a migração de dados históricos é iniciada. A atividade de cópia do ADF copiará os arquivos no AWS S3 que foram modificados pela última vez após esse período. O status padrão de copiar cada partição é 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Crie um procedimento armazenado no mesmo banco de dados SQL do Azure para a tabela de controle. 

    > [!NOTE]
    > O nome do Procedimento Armazenado é sp_insert_partition_JobRunTime_success. Ele será invocado pela atividade SqlServerStoredProcedure em seu pipeline ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Vá para o **modelo Copiar dados delta do AWS S3 para o modelo Azure Data Lake Storage Gen2.** Insira as conexões para sua tabela de controle externo, AWS S3 como o armazenamento de origem de dados e Azure Data Lake Storage Gen2 como a loja de destino. Esteja ciente de que a tabela de controle externo e o procedimento armazenado são referência para a mesma conexão.

    ![Crie uma nova conexão](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Você vê que os 2 pipelines e 3 conjuntos de dados foram criados, como mostrado no exemplo a seguir:

    ![Revisar o pipeline](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Selecione **Depurar,** digite **os Parâmetros**e, em seguida, **selecione Concluir**.

    ![Clique em **Debug**](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Você vê resultados semelhantes ao seguinte exemplo:

    ![Revisar o resultado](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Você também pode verificar os resultados da tabela de controle por uma consulta *"select * de s3_partition_delta_control_table",* você verá a saída semelhante ao seguinte exemplo:

    ![Revisar o resultado](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md)
- [Mover arquivos](solution-template-move-files.md)