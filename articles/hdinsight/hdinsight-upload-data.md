---
title: Carregar dados para trabalhos do Apache Hadoop no HDInsight
description: Saiba como carregar e acessar dados para trabalhos apache hadoop no HDInsight. Use cli clássico do Azure, Azure Storage Explorer, Azure PowerShell, a linha de comando Hadoop ou Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdiseo17may2017
ms.date: 04/07/2020
ms.openlocfilehash: c862633245e75613f9e4f9956486f872b96239f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875003"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos do Apache Hadoop no HDInsight

O HDInsight fornece um sistema de arquivos distribuídos Hadoop (HDFS) sobre o Azure Storage e o Azure Data Lake Storage. Este armazenamento inclui Gen1 e Gen2. O Azure Storage and Data Lake Storage Gen1 e o Gen2 são projetados como extensões HDFS. Eles permitem que o conjunto completo de componentes no ambiente Hadoop opere diretamente nos dados que gerencia. Azure Storage, Data Lake Storage Gen1 e Gen2 são sistemas de arquivos distintos. Os sistemas são otimizados para armazenamento de dados e cálculos sobre esses dados. Para obter informações sobre os benefícios do uso do Azure Storage, consulte [Use Azure Storage com HDInsight](hdinsight-hadoop-use-blob-storage.md). Veja também, [Use Data Lake Storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-store.md)e Use Data Lake Storage [Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) ou [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Conhecimento dos seguintes artigos:
    * [Use o armazenamento Azure com HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Usar o Data Lake Storage Gen1 com HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Usar o Data Lake Storage Gen2 com HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Carregar dados no Armazenamento do Azure

## <a name="utilities"></a>Utilidades

A Microsoft fornece os seguintes utilitários para trabalhar com o Armazenamento do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Comando do Hadoop](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> O comando do Hadoop só está disponível no cluster HDInsight. O comando apenas permite carregar dados do sistema de arquivos local para o Armazenamento do Microsoft Azure.  

## <a name="hadoop-command-line"></a>Linha de comando do Hadoop

A linha de comando do Hadoop só é útil para armazenar dados no Azure Storage Blob quando os dados já estão presentes no nó de cabeçalho do cluster.

Para usar o comando Hadoop, você deve primeiro conectar-se ao headnode usando [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão do HDInsight está no Azure Storage, /exemplo/data/data.txt está realmente no Azure Storage. Você também pode fazer referência ao arquivo como:

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para uma lista de outros comandos Hadoop que funcionam com arquivos, veja[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Em clusters do Apache HBase, o tamanho do bloco padrão usado na gravação de dados é de 256 KB. Embora isso funcione bem com APIs HBase ou APIs REST, o uso dos comandos `hadoop` ou `hdfs dfs` para gravar dados com mais de, aproximadamente, 12 GB resultará em um erro. Para obter mais informações, consulte a seção [Exceção de armazenamento para gravar no blob](#storage-exception-for-write-on-blob) neste artigo.

## <a name="graphical-clients"></a>Clientes gráficos

Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o Armazenamento do Azure. A seguinte tabela é uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Gerenciador de Armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer para Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Montar o Armazenamento do Azure como uma unidade local

Consulte [Montar o Armazenamento do Azure como uma unidade local](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Carregar usando serviços

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O serviço Azure Data Factory é um serviço totalmente gerenciado para compor dados: serviços de armazenamento, processamento e movimentação em pipelines de produção de dados simplificados, adaptáveis e confiáveis.

|Tipo de armazenamento|Documentação|
|----|----|
|Armazenamento de Blobs do Azure|[Copiar dados para ou do armazenamento Do Azure Blob usando a Fábrica de Dados Do Azure](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copiar dados de/para o Azure Data Lake Storage Gen1 usando o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Use-os para importar dados de um sistema de gerenciamento de banco de dados relacional (RDBMS), como SQL Server, MySQL ou Oracle. Em seguida, no sistema de arquivos distribuídos hadoop (HDFS). Transforme os dados em Hadoop com MapReduce ou Hive e exporte os dados de volta para um RDBMS.

Para obter mais informações, consulte [Usar Sqoop com HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDKs de desenvolvimento

O Armazenamento do Azure também pode ser acessado usando um SDK do Azure com as seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="storage-exception-for-write-on-blob"></a>Exceção de armazenamento para gravar no blob

**Sintomas**: Ao `hadoop` `hdfs dfs` usar os ou comandos para gravar arquivos que são ~12 GB ou maiores em um cluster HBase, você pode se deparar com o seguinte erro:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa**: HBase em clusters HDInsight padrão para um tamanho de bloco de 256 KB ao escrever para o armazenamento Azure. Embora isso funcione para APIs HBase ou APIs REST, isso resultará em um erro ao usar os utilitários de linha de comando `hadoop` ou `hdfs dfs`.

**Resolução**: use `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Você pode fazer esta modificação por uso `-D` usando o parâmetro. O seguinte comando é um exemplo de uso desse parâmetro com o comando `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando o Apache Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface de usuário do Ambari Web:

1. No navegador, acesse a interface de usuário do Ambari Web para seu cluster. A URL `https://CLUSTERNAME.azurehdinsight.net`é `CLUSTERNAME` , onde está o nome do seu cluster. Quando solicitado, insira o nome de administrador e a senha de administrador para o cluster.
2. No lado esquerdo da tela, escolha **HDFS** e selecione a guia **Configurações**.
3. No campo **Filtrar...**, insira `fs.azure.write.request.size`.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

    ![Imagem de alteração de valor por meio da interface de usuário do Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para saber mais sobre como usar o Ambari, confira [Gerenciar clusters HDInsight interface do usuário Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você entendeu como obter dados no HDInsight, leia os seguintes artigos para aprender a análise:

* [Comece com o Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Enviar trabalhos do Apache Hadoop de forma programática](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](./use-pig.md)
