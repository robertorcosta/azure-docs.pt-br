---
title: Visão geral do Azure Data Lake Storage Gen2 no HDInsight
description: Visão geral do Data Lake Storage Gen2 no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873336"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Visão geral do Azure Data Lake Storage Gen2 no HDInsight

O Azure Data Lake Storage Gen2 obtém os recursos de núcleo do Azure Data Lake Storage Gen1 e os integra ao armazenamento de Blobs do Azure. Esses recursos incluem um sistema de arquivos que é compatível com o Hadoop, o Azure Active Directory (Azure AD), e as listas de controle de acesso baseadas em POSIX (ACLs). Essa combinação permite que você aproveite o desempenho do Azure Data Lake Storage Gen1. Ao mesmo tempo em que usa o gerenciamento de hierarquiagem e ciclo de vida dos dados do armazenamento Blob.

Para obter mais informações sobre o Azure Data Lake Storage Gen2, confira [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Funcionalidade principal do Azure Data Lake Storage Gen2

* **Acesso compatível com Hadoop:** No Azure Data Lake Storage Gen2, você pode gerenciar e acessar dados assim como faria com um Hadoop Distributed File System (HDFS). O driver do Sistema de Arquivos de Blobs do Azure (ABFS) está disponível em todos os ambientes do Apache Hadoop, incluindo o Azure HDInsight e o Azure Databricks. Use o ABFS para acessar os dados armazenados no Data Lake Storage Gen2.

* **Um superconjunto de permissões POSIX:** O modelo de segurança do Data Lake Gen2 suporta permissões ACL e POSIX, juntamente com alguma granularidade extra específica do Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas administrativas ou de estruturas como o Apache Hive e o Apache Spark.

* **Custo-benefício:** Data Lake Storage Gen2 oferece capacidade de armazenamento de baixo custo e transações. Os ciclos de vida de armazenamento Do Zure Blob ajudam a reduzir os custos, ajustando as taxas de faturamento à medida que os dados se movem ao longo de seu ciclo de vida.

* **Compatibilidade com ferramentas, frameworks e aplicativos blob:** O Data Lake Storage Gen2 continua a trabalhar com uma ampla gama de ferramentas, frameworks e aplicativos para armazenamento Blob.

* **Driver otimizado:** O driver ABFS é otimizado especificamente para análise de big data. As APIs REST correspondentes são exibidas por meio do ponto de extremidade do Sistema de Arquivos Distribuído (DFS), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Novidades no Azure Data Lake Storage Gen2

### <a name="managed-identities-for-secure-file-access"></a>Identidades gerenciadas para acesso de arquivo seguro

O HDInsight do Azure usa identidades gerenciadas para proteger o acesso do cluster a arquivos no Azure Data Lake Storage Gen2. As identidades gerenciadas são um recurso do Azure Active Directory que fornece aos serviços do Azure um conjunto de credenciais gerenciadas automaticamente. Essas credenciais podem ser usadas para autenticar qualquer serviço com suporte para autenticação do Active Directory. O uso de identidades gerenciadas não exige que você armazene credenciais em código ou arquivos de configuração.

Para saber mais, confira [Gerenciar identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Driver do Sistema de Arquivos de Blobs do Azure

Os aplicativos do Apache Hadoop esperam nativamente ler e gravar dados do armazenamento em disco local. Um driver de sistema de arquivos Hadoop como o ABFS permite que os aplicativos Hadoop funcionem com armazenamento em nuvem. Funciona imitando operações regulares do sistema de arquivos Hadoop. O driver converte esses comandos recebidos do aplicativo em operações entendidas pela plataforma de armazenamento de nuvem verdadeira.

Anteriormente, o driver do sistema de arquivos Hadoop converteu todas as operações do sistema de arquivos para chamadas a API do Azure Storage REST no lado do cliente. E então invocou a API REST. Essa conversão do lado do cliente, no entanto, resultou em várias chamadas à API REST para uma única operação do sistema de arquivos, como a renomeação de um arquivo. O ABFS mudou a lógica do sistema de arquivos Hadoop do lado do cliente para o lado do servidor. A API do Azure Data Lake Storage Gen2 agora é executada em paralelo com a API de Blobs. Essa migração melhora o desempenho porque agora as operações comuns do sistema de arquivos do Hadoop podem ser executadas com uma chamada à API REST.

Para obter mais informações, consulte [O driver Azure Blob Filesystem (ABFS): Um driver de armazenamento Azure dedicado para Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Esquema de URI no Azure Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 usa um novo esquema de URI para acessar arquivos no Armazenamento do Azure a partir do HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

O esquema URI fornece acesso criptografado por SSL.

`<FILE_SYSTEM_NAME>` identifica o caminho do sistema de arquivos Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifica o nome da conta do Armazenamento do Azure. Um FQDN (nome de domínio totalmente qualificado) é necessário.

O `<PATH>` é o nome do caminho de HDFS do arquivo ou diretório.

Se os valores para `<FILE_SYSTEM_NAME>` e `<ACCOUNT_NAME>` não forem especificados, será usado o sistema de arquivos padrão. Para os arquivos no sistema de arquivos padrão, use um caminho absoluto ou um caminho relativo. Por exemplo, o arquivo `hadoop-mapreduce-examples.jar` que vem com clusters HDInsight pode ser referido usando um dos caminhos a seguir:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> O nome do arquivo é `hadoop-examples.jar` nos clusters HDInsight versões 2.1 e 1.6. Quando estiver trabalhando com arquivos fora do HDInsight, você verá que a maioria dos utilitários não reconhece o formato ABFS mas, em vez disso, esperam um formato de caminho básico, como `example/jars/hadoop-mapreduce-examples.jar`.

Para obter mais informações, consulte [Usar URI do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Visão geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)