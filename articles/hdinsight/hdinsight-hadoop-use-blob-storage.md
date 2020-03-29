---
title: Consultar dados do armazenamento do Azure compatível com o HDFS - Azure HDInsight
description: Aprenda a consultar dados do Armazenamento do Azure e do Azure Data Lake Storage para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: e62f6f8df51c6acf4e2ad8e28e431d264c2c99e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037239"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usar o Armazenamento do Azure com clusters HDInsight

Para analisar os dados no cluster HDInsight, você pode armazenar os dados no [Azure Storage,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)ou em uma combinação. Essas opções de armazenamento permitem que você exclua com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário.

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou, com o HDInsight 3.6, você pode selecionar o Armazenamento do Azure ou o Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 como o sistema de arquivos padrão com algumas exceções. Para o suporte do uso do Data Lake Storage Gen 1 como o armazenamento padrão e o vinculado, veja [Disponibilidade para o cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, você aprenderá como funciona o Armazenamento do Azure com clusters HDInsight. Para saber como o Data Lake Storage Gen 1 funciona com clusters HDInsight, veja [Usar o Azure Data Lake Storage com clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Para saber mais sobre a criação de um cluster HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> Tipo de conta de armazenamento **O BlobStorage** só pode ser usado como armazenamento secundário para clusters HDInsight.

| Tipo de conta de armazenamento | Serviços com suporte | Níveis de desempenho compatíveis |Níveis de desempenho não suportados| Camadas de acesso compatíveis |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (uso geral v2)  | Blob     | Standard                    |Premium| Quente, legal, arquivo\*   |
| Armazenamento (uso geral v1)   | Blob     | Standard                    |Premium| N/D                    |
| BlobStorage                    | Blob     | Standard                    |Premium| Quente, legal, arquivo\*   |

Não recomendamos o contêiner de blobs padrão para armazenar dados corporativos. É uma prática recomendada excluir o contêiner de blobs padrão após cada uso para reduzir o custo de armazenamento. O contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

O compartilhamento de um recipiente blob como o sistema de arquivos padrão para vários clusters não é suportado.

> [!NOTE]  
> O nível de acesso ao Archive é um nível offline que tem uma latência de recuperação de várias horas e não é recomendado para uso com hdInsight. Para saber mais, consulte [Camada de acesso aos arquivos](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Arquivos de acesso de dentro do cluster

Há várias maneiras de acessar os arquivos no Data Lake Storage em um cluster HDInsight. O esquema de URI fornece acesso sem criptografia (com o prefixo *wasb:*) e acesso criptografado SSL (com *wasbs*). Recomendamos usar *wasbs* sempre que possível, mesmo ao acessar dados que residem dentro da mesma região do Azure.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Usando o formato de caminho encurtado**. Com esta abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Exemplos são baseados em uma [conexão ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) com o nó principal do cluster. Os exemplos usam todos os três esquemas URI. Substituir `CONTAINERNAME` `STORAGEACCOUNT` e com os valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos hdfs

1. Crie um arquivo simples no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento em cluster.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiar dados do armazenamento local para o armazenamento em cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Listar o conteúdo do diretório no armazenamento em cluster.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhecem o formato WASB e, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Criando uma mesa colmeia

Três locais de arquivo são mostrados para fins ilustrativos. Para execução real, use `LOCATION` apenas uma das entradas.

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

## <a name="access-files-from-outside-cluster"></a>Arquivos de acesso de cluster externo

A Microsoft fornece as seguintes ferramentas para trabalhar com o Azure Storage:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Identificar o caminho de armazenamento da Ambari

* Para identificar o caminho completo para o armazenamento padrão configurado, navegue até:

    **HDFS** > **Configs** `fs.defaultFS` e digite na caixa de entrada do filtro.

* Para verificar se o wasb store está configurado como armazenamento secundário, navegue até:

    **HDFS** > **Configs** `blob.core.windows.net` e digite na caixa de entrada do filtro.

Para obter o caminho usando a API Ambari REST, consulte [Obter o armazenamento padrão](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage).

## <a name="blob-containers"></a>Contêineres de blob

Para usar blobs, primeiro crie uma [conta do Azure Storage](../storage/common/storage-create-storage-account.md). Como parte desse processo, especifique uma região do Azure na qual a conta de armazenamento será criada. O cluster e a conta de armazenamento devem ser hospedados na mesma região. O banco de dados SQL Server do metastore do Hive e o banco de dados SQL Server do metastore do Oozie do Apache também devem estar localizados na mesma região.

Independentemente de onde estiverem, cada blob que você criar pertencerá a um contêiner na sua conta de armazenamento do Azure. Esse contêiner pode ser um contêiner de armazenamento de blob existente criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight.

O contêiner de blob padrão armazena informações específicas do cluster como logs e o histórico do trabalho. Não compartilhe um contêiner de armazenamento de blob padrão com vários clusters HDInsight. Isso pode corromper o histórico de trabalhos. Recomenda-se usar um contêiner diferente para cada cluster e colocar dados compartilhados em uma conta de armazenamento vinculada especificada na implantação de todos os clusters relevantes em vez da conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculadas, veja [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). No entanto, você pode reutilizar um contêiner de armazenamento padrão depois que o cluster HDInsight original for excluído. Para clusters HBase, você pode realmente manter o esquema e os dados da tabela HBase criando um novo cluster HBase usando o contêiner blob padrão que é usado por um cluster HBase que foi excluído.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Usar contas de armazenamento adicionais

Ao criar um cluster HDInsight, você especifica a conta de armazenamento do Azure que deseja associar a ele. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação ou após a criação de um cluster. Para obter instruções sobre como adicionar mais contas de armazenamento, veja [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o armazenamento do Azure compatível com HDFS com o HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Comece com o Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução ao Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados para hdinsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Use o Azure Data Lake Storage Gen2 com clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Tutorial: Extraia, transforme e carregue dados usando consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
