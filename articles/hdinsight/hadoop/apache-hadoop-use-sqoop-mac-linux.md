---
title: Apache Sqoop com Apache Hadoop - Azure HDInsight
description: Aprenda a usar o Apache Sqoop para importar e exportar entre o Apache Hadoop no HDInsight e um banco de dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769380"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Use o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e o banco de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Apache Sqoop para importar e exportar entre um cluster do Apache Hadoop no HDInsight do Azure e no banco de dados SQL do Azure ou do Microsoft SQL Server. As etapas deste documentam usam o comando `sqoop` diretamente do nó principal do cluster Hadoop. Use o SSH para se conectar ao nó principal e executar os comandos neste documento. Este artigo é uma continuação de [Use Apache Sqoop with Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de [teste configurar](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) do Uso [apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiaridade com Sqoop. Para obter mais informações, consulte [o Guia do Usuário sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Configuração

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para facilitar o uso, defina variáveis. Substituir `PASSWORD` `MYSQLSERVER`, `MYDATABASE` e com os valores relevantes e, em seguida, digitar os comandos abaixo:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Exportação do Sqoop

Da Colmeia ao SQL Server.

1. Para verificar se o Sqoop pode ver seu banco de dados SQL, digite o comando abaixo em sua conexão SSH aberta. Este comando retorna uma lista de bancos de dados.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Digite o seguinte comando para ver uma lista de tabelas para o banco de dados especificado:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Para exportar dados da `hivesampletable` tabela `mobiledata` Hive para a tabela no Banco de Dados SQL, digite o comando abaixo em sua conexão SSH aberta:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Para verificar se os dados foram exportados, use as seguintes consultas da sua conexão SSH para visualizar os dados exportados:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Importação do Sqoop

Do sql server ao armazenamento Azure.

1. Digite o comando abaixo em sua conexão `mobiledata` SSH aberta para importar `wasbs:///tutorials/usesqoop/importeddata` dados da tabela no Banco de Dados SQL, para o diretório no HDInsight. Os campos nos dados que são separados por um caractere de tabulação, e as linhas serão encerradas por um caractere de nova linha.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Alternativamente, você também pode especificar uma tabela Colmeia:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Uma vez concluída a importação, digite o seguinte comando na conexão SSH aberta para listar os dados no novo diretório:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Use [a linha beeline](./apache-hadoop-use-hive-beeline.md) para verificar se a tabela foi criada em Colmeia.

    1. Conectar

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Execute cada consulta abaixo de uma de cada vez e revise a saída:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Saia beeline `!exit`com .

## <a name="limitations"></a>Limitações

* Exportação em massa - Com o HDInsight baseado em Linux, o conector Sqoop usado para exportar dados para o Microsoft SQL Server ou O Banco de Dados SQL do Azure não suporta inserções em massa.

* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="important-considerations"></a>Considerações importantes

* O HDInsight e o SQL Server devem estar na mesma Rede Virtual do Azure.

    Para obter um exemplo, consulte o documento [Conectar o HDInsight à sua rede local](./../connect-on-premises-network.md).

    Para saber mais sobre como usar o HDInsight com Redes Virtuais do Azure, veja o documento [Estender o HDInsight usando a Rede Virtual do Azure](../hdinsight-plan-virtual-network-deployment.md). Para saber mais sobre Rede Virtual do Azure, veja o documento [Visão Geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md).

* O SQL Server também deve ser configurado para permitir autenticação do SQL. Para obter mais informações, consulte o documento [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx).

* Você precisará configurar o SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Próximas etapas

Agora você aprendeu a usar Sqoop. Para obter mais informações, consulte:

* [Use o Apache Oozie com HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho Oozie.
* [Analise os dados de atraso de voo usando o HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Use a Consulta Interativa para analisar dados de atraso de voo e, em seguida, use o Sqoop para exportar dados para um banco de dados Do Azure SQL.
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.
