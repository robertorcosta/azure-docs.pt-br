---
title: Use apache ambari hive view com Apache Hadoop no Azure HDInsight
description: Saiba como usar o Modo de Exibição do Hive em seu navegador da Web para enviar consultas do Hive. O Modo de exibição do Hive faz parte da Interface de Usuário da Web do Ambari fornecida com o cluster HDInsight baseado em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: 787d88d336abcf3b0ba9b14c3d3798850b665eca
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745099"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Use o Apache Ambari Hive View com o Apache Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como executar as consultas do Hive usando o Apache Ambari Hive View. A Exibição do Hive permite que você crie, otimize e execute consultas de Hive do seu navegador da Web.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Executar um trabalho do Hive

1. No [portal Azure,](https://portal.azure.com/)selecione seu cluster.  Consulte [Lista e mostre clusters](../hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto em uma nova visão de portal.

1. Em **cluster dashboards,** selecione **exibições Ambari**. Quando solicitado a autenticar, use o nome e senha da conta de logon de cluster (padrão `admin`) que você forneceu ao criar o cluster. Você também pode `https://CLUSTERNAME.azurehdinsight.net/#/main/views` navegar para `CLUSTERNAME` o seu navegador onde está o nome do seu cluster.

1. Na lista de exibições, selecione __Exibição de Hive__.

    ![Apache Ambari seleciona vista apache colmeia](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    A página de exibição do Hive é semelhante à seguinte imagem:

    ![Imagem da planilha de consulta para a exibição de Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Da guia __Consulta__, cole as seguintes instruções HiveQL na planilha:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Essas declarações fazem as seguintes ações:

    |de | Descrição |
    |---|---|
    |DROP TABLE|Exclui a tabela e o arquivo de dados, caso a tabela já exista.|
    |CRIAR TABELA EXTERNA|Cria uma nova tabela "externa" no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.|
    |FORMATO DA LINHA|Mostra como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.|
    |ARMAZENADO COMO LOCAL DE ARQUIVO DE TEXTO|Mostra o local em que os dados são armazenados e se estão armazenados como texto.|
    |SELECT|Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].|

   > [!IMPORTANT]  
   > Deixe a seleção __Banco de dados__ em __padrão__. Os exemplos neste documento usam o banco de dados padrão incluído no HDInsight.

1. Para iniciar a consulta, **selecione Execute** abaixo da planilha. O botão fica laranja e o texto é alterado para **Parar**.

1. Depois que a consulta for concluída, a seção **Resultados** exibirá os resultados da operação. O texto a seguir é o resultado da consulta:

        loglevel       count
        [ERROR]        3

    Você pode usar a guia **LOG** para visualizar as informações de registro que o trabalho criou.

   > [!TIP]  
   > Baixe ou salve resultados da caixa de diálogo sestár **ações** na guia **Resultados.**

### <a name="visual-explain"></a>Explicação visual

Para exibir uma visualização do plano de consulta, selecione a guia **Explicação Visual** abaixo da planilha.

O modo de exibição **Explicação Visual** da consulta pode ser útil na compreensão do fluxo de consultas complexas.

### <a name="tez-ui"></a>Interface de usuário do Tez

Para exibir a ida de ida de Tez para a consulta, selecione a guia **Tez UI** abaixo da planilha.

> [!IMPORTANT]  
> O Tez não é usado para resolver todas as consultas. Muitas consultas podem ser resolvidas sem usar o Tez.

## <a name="view-job-history"></a>Exibir histórico de trabalho

A guia __Trabalhos__ exibe um histórico das consultas de Hive.

![Apache Hive ver emprego situou-se](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabelas de banco de dados

Você pode usar a guia __tabelas__ para trabalhar com tabelas em um banco de dados de Hive.

![Imagem da guia tabelas da Colmeia Apache](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Consultas salvas

Na guia **Consulta,** você pode, opcionalmente, salvar consultas. Depois de salvar uma consulta, você pode reutilizá-la da guia __Consultas Salvas__.

![Apache Hive visualiza guia de consultas salvas](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Consultas salvas são armazenadas no armazenamento de cluster padrão. Você pode encontrar as consultas salvas no caminho `/user/<username>/hive/scripts`. Elas são armazenadas como arquivos de texto sem formatação `.hql`.
>
> Se você excluir o cluster, mas manter o armazenamento, pode usar um utilitário como o [Gerenciador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) ou o Gerenciador de Armazenamento do Data Lake (do [Portal do Azure](https://portal.azure.com)) para recuperar as consultas.

## <a name="user-defined-functions"></a>Funções definidas pelo usuário

O Hive pode ser estendido por meio de UDF (funções definidas pelo usuário). Use uma UDF para implementar funcionalidade ou lógica que não é facilmente modelada em HiveQL.

Declare e salve um conjunto de UDFs usando a guia **UDF** na parte superior da exibição do Hive. Essas UDFs podem ser usadas com o **Editor de Consultas**.

![Exibição da guia Apache Hive UDFs](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Um botão **Inserir udfs** é exibido na parte inferior do **Editor de consulta**. Esta entrada exibe uma lista de sossegitos das UDFs definidas na Exibição colmeia. A seleção de uma UDF adiciona instruções HiveQL à sua consulta para habilitar a UDF.

Por exemplo, se você definiu um UDF com as seguintes propriedades:

* Nome de recurso: myudfs

* Caminho do recurso: /myudfs.jar

* Nome da UDF: myawesomeudf

* Nome de classe da  UDF: com.myudfs.Awesome

O uso do botão **Inserir udfs** exibe uma entrada denominada **myudfs**, com outra lista suspensa para cada UDF definida para esse recurso. Nesse caso, ela é **myawesomeudf**. A seleção dessa entrada adiciona o seguinte ao início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Você pode usar a UDF em sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para saber mais sobre como usar UDFs com Hive no HDInsight, consulte os seguintes artigos:

* [Usar o Python com Apache Hive e Apache Pig no HDInsight](python-udf-hdinsight.md)
* [Usar um Java UDF com Apache Hive no HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Configurações do Hive

Você pode alterar diversas configurações do Hive, por exemplo, alterar o mecanismo de execução do Hive de Tez (o padrão) para MapReduce.

## <a name="next-steps"></a>Próximas etapas

Para informações gerais sobre o Hive no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
