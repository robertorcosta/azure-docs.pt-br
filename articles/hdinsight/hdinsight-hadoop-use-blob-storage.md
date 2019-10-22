---
title: Consultar dados do armazenamento do Azure compatível com o HDFS – Azure HDInsight
description: Saiba como consultar dados do armazenamento do Azure e Azure Data Lake Storage para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71936868"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Usar o armazenamento do Azure com clusters do Azure HDInsight

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [armazenamento do Azure](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md) /[Azure data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)ou uma combinação. Essas opções de armazenamento permitem que você exclua com segurança os clusters HDInsight que são usados para computação sem perder dados do usuário.

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica um esquema e uma autoridade padrão. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blob no armazenamento do Azure como o sistema de arquivos padrão ou com o HDInsight 3,6, você pode selecionar o armazenamento do Azure ou Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 como os arquivos padrão sistema com algumas exceções. Para obter suporte ao usar Data Lake Storage Gen 1 como o armazenamento padrão e vinculado, consulte [disponibilidade para o cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, você aprende como o armazenamento do Azure funciona com clusters HDInsight. Para saber como Data Lake Storage Gen 1 funciona com clusters HDInsight, confira [usar o Azure data Lake Storage com clusters do Azure hdinsight](hdinsight-hadoop-use-data-lake-store.md). Para obter mais informações sobre como criar um cluster HDInsight, consulte [criar Apache Hadoop clusters no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

O armazenamento do Azure é uma solução de armazenamento de uso geral robusta que se integra perfeitamente com o HDInsight. O HDInsight pode usar um contêiner de blob no armazenamento do Azure como o sistema de arquivos padrão para o cluster. Por meio de uma interface HDFS (sistema de arquivos distribuído Hadoop), o conjunto completo de componentes no HDInsight pode operar diretamente em dados estruturados ou não estruturados armazenados como BLOBs.

> [!IMPORTANT]  
> O tipo de conta de armazenamento **BlobStorage** pode ser usado apenas como armazenamento secundário para clusters HDInsight.

| Tipo de conta de armazenamento | Serviços com suporte | Níveis de desempenho com suporte | Níveis de acesso com suporte |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (uso geral v2)  | Blob     | Standard                    | @No__t_0 quente, esporádico e de arquivo   |
| Armazenamento (uso geral v1)   | Blob     | Standard                    | N/D                    |
| BlobStorage                    | Blob     | Standard                    | @No__t_0 quente, esporádico e de arquivo   |

Não recomendamos que você use o contêiner de blob padrão para armazenar dados corporativos. A exclusão do contêiner de BLOBs padrão após cada uso para reduzir o custo de armazenamento é uma boa prática. O contêiner padrão contém os logs do aplicativo e do sistema. Certifique-se de recuperar os logs antes de excluir o contêiner.

Não há suporte para o compartilhamento de um contêiner de blob como o sistema de arquivos padrão para vários clusters.

> [!NOTE]  
> A camada de acesso de arquivamento é uma camada offline que tem uma latência de recuperação de várias horas e não é recomendada para uso com o HDInsight. Para obter mais informações, consulte [arquivar camada de acesso](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...** para que o HDInsight possa acessar seu armazenamento considerar.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir fornece uma exibição abstrata da arquitetura de armazenamento do HDInsight usando o armazenamento do Azure:

![Os clusters do Hadoop usam a API do HDFS para acessar e armazenar dados no armazenamento de BLOBs](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "Arquitetura de armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de arquivos distribuído que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

    hdfs://<namenodehost>/<path>

Além disso, o HDInsight permite que você acesse os dados armazenados no armazenamento do Azure. A sintaxe é:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Aqui estão algumas considerações ao usar a conta de armazenamento do Azure com clusters HDInsight.

* **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, você tem acesso completo aos blobs nesses contêineres.

* **Contêineres públicos ou blobs públicos nas contas de armazenamento que NÃO estão conectadas a um cluster:** você tem permissão somente leitura para os blobs nos contêineres.
  
> [!NOTE]  
> Os contêineres públicos permitem obter uma lista de todos os blobs que estão disponíveis no contêiner e obter metadados do contêiner. Um blob público somente permite acessar os blobs se você souber a URL exata. Para obter mais informações, consulte [gerenciar o acesso a contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Contêineres privados em contas de armazenamento que não estão conectadas a um cluster:** Não é possível acessar os BLOBs nos contêineres, a menos que você defina a conta de armazenamento ao enviar trabalhos do WebHCat. Isso é explicado mais adiante neste artigo.

As contas de armazenamento que são definidas no processo de criação e suas chaves são armazenadas em `%HADOOP_HOME%/conf/core-site.xml` nos nós de cluster. O comportamento padrão do HDInsight é usar as contas de armazenamento definidas no arquivo Core-site. xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo Apache Hive, MapReduce, Apache Hadoop streaming e Apache Pig, podem conter uma descrição de contas de armazenamento e metadados com eles. (Isso atualmente funciona para Pig com contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [usando um cluster HDInsight com metastores e contas de armazenamento alternativas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os BLOBs podem ser usados para dados estruturados e não estruturados. Os contêineres de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de *entrada* real, mas, devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.

## <a id="benefits"></a>Benefícios do armazenamento do Azure

O custo de desempenho implícito de não colocar clusters de computação e recursos de armazenamento é mitigado pela maneira como os clusters de computação são criados próximos aos recursos da conta de armazenamento na região do Azure, em que a rede de alta velocidade torna a ti eficiente para o nós de computação para acessar os dados dentro do armazenamento do Azure.

Há vários benefícios associados ao armazenamento dos dados no armazenamento do Azure em vez de no HDFS:

* **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Os dados no armazenamento do Azure podem ser acessados por meio das APIs HDFS ou por meio das [APIs REST do armazenamento de BLOBs](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Assim, um conjunto maior de aplicativos (incluindo outros clusters HDInsight) e ferramentas podem ser usados para produzir e consumir os dados.

* **Arquivamento de dados:** Armazenar dados no armazenamento do Azure permite que os clusters HDInsight usados para computação sejam excluídos com segurança sem perder dados do usuário.

* **Custo de armazenamento de dados:** O armazenamento de dados no DFS por longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure, pois o custo de um cluster de computação é maior do que o custo do armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de computação, você também está salvando os custos de carregamento de dados.

* **Escala horizontal elástica:** embora o HDFS forneça um sistema de arquivos escalado horizontalmente, a escala é determinada pelo número de nós que você cria para o seu cluster. A alteração da escala pode se tornar um processo mais complicado do que depender dos recursos de dimensionamento elástico que você obtém automaticamente no armazenamento do Azure.

* **Replicação geográfica:** O armazenamento do Azure pode ser replicado geograficamente. Embora isso dê a você a recuperação geográfica e a redundância de dados, um failover para o local com replicação geográfica afeta seriamente o desempenho e pode incorrer em custos adicionais. Portanto, nossa recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados valer a pena do custo adicional.

Determinados trabalhos e pacotes MapReduce podem criar resultados intermediários que você não deseja realmente armazenar no armazenamento do Azure. Nesse caso, você pode optar por armazenar os dados no HDFS local. Na verdade, o HDInsight usa o DFS para vários desses resultados intermediários em trabalhos do hive e outros processos.

> [!NOTE]  
> A maioria dos comandos do HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) ainda funciona como esperado. Somente os comandos que são específicos para a implementação nativa do HDFS (que é conhecida como DFS), como `fschk` e `dfsadmin`, mostram um comportamento diferente no armazenamento do Azure.

## <a name="address-files-in-azure-storage"></a>Arquivos de endereço no armazenamento do Azure

O esquema de URI para acessar arquivos no armazenamento do Azure do HDInsight é:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

O esquema de URI fornece acesso sem criptografia (com o prefixo *wasb:* ) e acesso criptografado SSL (com *wasbs*). Recomendamos usar *wasbs* sempre que possível, mesmo ao acessar dados que residem dentro da mesma região do Azure.

O `<BlobStorageContainerName>` identifica o nome do contêiner de blob no armazenamento do Azure.
O `<StorageAccountName>` identifica o nome da conta de armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.

Se nem `<BlobStorageContainerName>` nem `<StorageAccountName>` tiverem sido especificadas, o sistema de arquivos padrão será usado. Para os arquivos no sistema de arquivos padrão, você pode usar um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo *hadoop-mapreduce-examples.jar* fornecido com clusters HDInsight pode ser referenciado para usar um dos seguintes procedimentos:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> O nome do arquivo é `hadoop-examples.jar` em clusters do HDInsight versões 2,1 e 1,6.

O caminho é o nome do caminho do HDFS do arquivo ou diretório. Como os contêineres no armazenamento do Azure são repositórios de chave-valor, não há nenhum sistema de arquivos hierárquico verdadeiro. Um caractere "/" dentro de uma chave de blob é interpretado como um separador de diretório. Por exemplo, o nome do blob para *hadoop-mapreduce-examples.jar* é:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhecem o formato WASB e, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>Contêineres de BLOB

Para usar BLOBs, você primeiro cria uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Como parte disso, você especifica uma região do Azure na qual a conta de armazenamento é criada. O cluster e a conta de armazenamento devem ser hospedados na mesma região. O banco de dados do metastore do Hive SQL Server e do Apache Oozie metastore SQL Server também devem estar localizados na mesma região.

Onde quer que ele resida, cada blob que você criar pertence a um contêiner em sua conta de armazenamento do Azure. Esse contêiner pode ser um blob existente que foi criado fora do HDInsight, ou pode ser um contêiner criado para um cluster HDInsight.

O contêiner de blob padrão armazena informações específicas do cluster, como logs e histórico de trabalhos. Não compartilhe um contêiner de armazenamento de blob padrão com vários clusters HDInsight. Isso pode corromper o histórico do trabalho. É recomendável usar um contêiner diferente para cada cluster e colocar dados compartilhados em uma conta de armazenamento vinculada especificada na implantação de todos os clusters relevantes em vez da conta de armazenamento padrão. Para obter mais informações sobre como configurar contas de armazenamento vinculadas, consulte [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). No entanto, você pode reutilizar um contêiner de armazenamento padrão depois que o cluster HDInsight original tiver sido excluído. Para clusters HBase, você pode realmente manter o esquema de tabela do HBase e os dados criando um novo cluster HBase usando o contêiner de blob padrão que é usado por um cluster HBase que foi excluído.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagindo com o armazenamento do Azure

A Microsoft fornece as seguintes ferramentas para trabalhar com o armazenamento do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Usar contas de armazenamento adicionais

Ao criar um cluster HDInsight, você especifica a conta de armazenamento do Azure que deseja associar a ele. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação ou após a criação de um cluster. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu a usar o armazenamento do Azure compatível com HDFS com o HDInsight. Isso permite que você crie soluções de aquisição de dados escalonáveis, de longo prazo e de arquivamento, e use o HDInsight para desbloquear as informações dentro dos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução ao Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar assinaturas de acesso compartilhado do armazenamento do Azure para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Usar Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
