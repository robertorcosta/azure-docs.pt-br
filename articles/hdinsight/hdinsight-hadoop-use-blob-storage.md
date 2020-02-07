---
title: Consultar dados do armazenamento do Azure compatível com o HDFS - Azure HDInsight
description: Aprenda a consultar dados do Armazenamento do Azure e do Azure Data Lake Storage para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 55cddf5317938dea353517cde7260a1aa531d1df
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061251"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usar o Armazenamento do Azure com clusters HDInsight

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [armazenamento do Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)ou uma combinação. Essas opções de armazenamento permitem que você exclua com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário.

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou, com o HDInsight 3.6, você pode selecionar o Armazenamento do Azure ou o Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 como o sistema de arquivos padrão com algumas exceções. Para o suporte do uso do Data Lake Storage Gen 1 como o armazenamento padrão e o vinculado, veja [Disponibilidade para o cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, você aprenderá como funciona o Armazenamento do Azure com clusters HDInsight. Para saber como o Data Lake Storage Gen 1 funciona com clusters HDInsight, veja [Usar o Azure Data Lake Storage com clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Para saber mais sobre a criação de um cluster HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> O tipo de conta de armazenamento **BlobStorage** pode ser usado apenas como armazenamento secundário para clusters HDInsight.

| Tipo de conta de armazenamento | Serviços com suporte | Níveis de desempenho compatíveis | Camadas de acesso compatíveis |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (uso geral v2)  | Blob     | Standard                    | \* quente, esporádico e de arquivo   |
| Armazenamento (uso geral v1)   | Blob     | Standard                    | N/D                    |
| BlobStorage                    | Blob     | Standard                    | \* quente, esporádico e de arquivo   |

Não recomendamos o contêiner de blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de blobs padrão após cada uso para reduzir o custo de armazenamento. O contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

Não há suporte para o compartilhamento de um contêiner de blob como o sistema de arquivos padrão para vários clusters.

> [!NOTE]  
> A camada de acesso de arquivamento é uma camada offline que tem uma latência de recuperação de várias horas e não é recomendada para uso com o HDInsight. Para saber mais, consulte [Camada de acesso aos arquivos](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Há várias maneiras de acessar os arquivos no Data Lake Storage em um cluster HDInsight. O esquema de URI fornece acesso sem criptografia (com o prefixo *wasb:* ) e acesso criptografado SSL (com *wasbs*). Recomendamos usar *wasbs* sempre que possível, mesmo ao acessar dados que residem dentro da mesma região do Azure.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Usando o formato de caminho encurtado**. Com essa abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos são baseados em uma [conexão SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) com o nó principal do cluster. Os exemplos usam todos os três esquemas de URI. Substituir `CONTAINERNAME` e `STORAGEACCOUNT` pelos valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos do HDFS

1. Crie um arquivo simples no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de cluster.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie dados do armazenamento local para o armazenamento de cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Liste o conteúdo do diretório no armazenamento de cluster.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhecem o formato WASB e, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Criando uma tabela Hive

Três locais de arquivo são mostrados para fins ilustrativos. Para a execução real, use apenas uma das entradas de `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identificar o caminho de armazenamento do Ambari

* Para identificar o caminho completo para o repositório padrão configurado, navegue até:

    O **HDFS** > **configurações** e insere `fs.defaultFS` na caixa de entrada de filtro.

* Para verificar se o repositório do WASB está configurado como armazenamento secundário, navegue até:

    O **HDFS** > **configurações** e insere `blob.core.windows.net` na caixa de entrada de filtro.

## <a name="blob-containers"></a>Contêineres de blob

Para usar BLOBs, você primeiro cria uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Como parte desse processo, especifique uma região do Azure na qual a conta de armazenamento será criada. O cluster e a conta de armazenamento devem ser hospedados na mesma região. O banco de dados SQL Server do metastore do Hive e o banco de dados SQL Server do metastore do Oozie do Apache também devem estar localizados na mesma região.

Independentemente de onde estiverem, cada blob que você criar pertencerá a um contêiner na sua conta de armazenamento do Azure. Esse contêiner pode ser um contêiner de armazenamento de blob existente criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight.

O contêiner de blob padrão armazena informações específicas do cluster como logs e o histórico do trabalho. Não compartilhe um contêiner de armazenamento de blob padrão com vários clusters HDInsight. Isso pode corromper o histórico de trabalhos. É recomendável usar um contêiner diferente para cada cluster e colocar dados compartilhados em uma conta de armazenamento vinculada especificada na implantação de todos os clusters relevantes em vez da conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculadas, consulte [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). No entanto, você pode reutilizar um contêiner de armazenamento padrão depois que o cluster HDInsight original for excluído. Para clusters HBase, você pode realmente manter o esquema de tabela do HBase e os dados criando um novo cluster HBase usando o contêiner de blob padrão que é usado por um cluster HBase que foi excluído.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagindo com o armazenamento do Azure

A Microsoft fornece as seguintes ferramentas para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [PowerShell do Azure](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Usar contas de armazenamento adicionais

Ao criar um cluster HDInsight, você especifica a conta de armazenamento do Azure que deseja associar a ele. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação ou após a criação de um cluster. Para obter instruções sobre como adicionar mais contas de armazenamento, veja [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento do Azure compatível com HDFS com o HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução ao Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar assinaturas de acesso compartilhado do armazenamento do Azure para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Tutorial: extrair, transformar e carregar dados usando a consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
