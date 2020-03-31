---
title: Execute consultas da Base Apache no Azure HDInsight com apache phoenix
description: Aprenda a usar o Apache Zeppelin para executar consultas da Base Apache com Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392235"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Use o Apache Zeppelin para executar consultas apache phoenix sobre Apache HBase no Azure HDInsight

Apache Phoenix é um banco de dados relacional livre e paralelo em massa criado em camadas em HBase. Phoenix permite que você use SQL como consultas sobre hbase. Phoenix usa drivers JDBC por baixo para permitir que você crie, exclua, altere tabelas SQL, índices, visualizações e seqüências.  Você também pode usar Phoenix para atualizar linhas individualmente e em massa. Phoenix usa uma compilação nativa NOSQL em vez de usar o MapReduce para compilar consultas, permitindo a criação de aplicativos de baixa latência em cima do HBase.

O Apache Zeppelin é um notebook baseado na Web de código aberto que permite criar documentos colaborativos baseados em dados usando análises interativas de dados e idiomas como SQL e Scala. Ele ajuda os desenvolvedores de dados & cientistas de dados a desenvolver, organizar, executar e compartilhar código para manipulação de dados. Ele permite visualizar resultados sem se referir à linha de comando ou precisar dos detalhes do cluster.

Os usuários do HDInsight podem usar o Apache Zeppelin para consultar tabelas do Phoenix. O Apache Zeppelin é integrado ao cluster HDInsight e não há etapas adicionais para usá-lo. Basta criar um Notebook Zeppelin com o intérprete JDBC e começar a escrever suas consultas Phoenix SQL

## <a name="prerequisites"></a>Pré-requisitos

Um cluster Apache HBase no HDInsight. Veja [Começar com o Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Criar uma anotação do Apache Zeppelin

1. Substitua `CLUSTERNAME` pelo nome do cluster na seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Em seguida, insira a URL em um navegador da Web. Insira o nome de usuário e a senha de logon do cluster.

1. Na página do Zeppelin, selecione **Criar nova nota**.

    ![HDInsight Consulta Interativa Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Na caixa de diálogo **Criar anotação**, digite ou selecione os seguintes valores:

    - Nome da nota: Digite um nome para a nota.
    - Intérprete padrão: Selecione **jdbc** na lista de paradas.

    Em seguida, selecione **Criar nota**.

1. Certifique-se de que o cabeçalho do notebook mostra um status conectado. É denotado por um ponto verde no canto superior direito.

    ![Status do bloco de anotações do Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Status do bloco de anotações do Zeppelin")

1. Crie uma tabela do HBase. Digite o seguinte comando e, em seguida, **pressione Shift + Enter**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    A **declaração %jdbc (phoenix)** na primeira linha diz ao notebook para usar o intérprete Phoenix JDBC.

1. Exibir tabelas criadas.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Insira os valores na tabela.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Consulte a tabela.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Exclua um registro.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Descarte a tabela.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Próximas etapas

- [Apache Phoenix agora suporta Zeppelin no Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Gramática Apache Phoenix](https://phoenix.apache.org/language/index.html)
