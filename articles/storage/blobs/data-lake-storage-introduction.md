---
title: Introdução ao Azure Data Lake Storage Gen2
description: Fornece uma visão geral do Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942925"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Azure Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado sobre o [Armazenamento de Blobs do Azure](storage-blobs-introduction.md). O Data Lake Storage Gen2 é o resultado obtido ao convergir os recursos de nossos dois serviços de armazenamento existentes, o Armazenamento de Blobs do Azure e o Azure Data Lake Storage Gen1. Os recursos do [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), como semântica do sistema de arquivos, diretório e segurança e escala em nível de arquivo, são combinados com armazenamento em camadas de baixo custo, alta disponibilidade/recursos de recuperação de desastre do [Armazenamento de Blobs do Azure](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Projetado para análise de big data da empresa

O Data Lake Storage Gen2 torna o armazenamento do Azure a fundação para a criação de data lakes empresariais no Azure. Projetado desde o início para atender a vários petabytes de informações enquanto mantém centenas de gigabits de taxa de transferência, o Data Lake Storage Gen2 permite que você gerencie de uma maneira fácil grandes quantidades de dados.

Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](data-lake-storage-namespace.md) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados. Uma convenção de nomenclatura de armazenamento de objetos comum usa barras no nome para imitar uma estrutura de diretórios hierárquica. Essa estrutura se torna real com o Data Lake Storage Gen2. Operações como renomear ou excluir um diretório tornam-se operações únicas de metadados atômicos no diretório, em vez de enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

O Data Lake Storage Gen2 se baseia no Armazenamento de Blobs e aprimora o desempenho, o gerenciamento e a segurança das seguintes maneiras:

-   **O desempenho** é otimizado porque você não precisa copiar ou transformar dados como um pré-requisito para análise. Em comparação com o namespace simples do Armazenamento de Blobs, o namespace hierárquico aprimora muito o desempenho das operações de gerenciamento de diretório, o que aprimora o desempenho geral do trabalho.

-   **Gerenciamento** é mais fácil porque você pode organizar e manipular arquivos por meio de diretórios e subdiretórios.

-   **Segurança** é aplicável porque você pode definir as permissões POSIX em arquivos individuais ou diretórios.

Além disso, o Data Lake Storage Gen2 é muito econômico, pois se baseia no [Armazenamento de Blobs do Azure](storage-blobs-introduction.md) de baixo custo. Os recursos adicionais reduzem ainda mais o custo total de propriedade para executar a análise de big data no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais recursos do Data Lake Storage Gen2

-   **Acesso compatível com Hadoop**: O Azure Data Lake Storage Gen2 permite gerenciar e acessar dados como você faria com um [Sistema de Arquivos Distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [driver ABFS](data-lake-storage-abfs-driver.md) está disponível em todos os ambientes Apache Hadoop, incluindo [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) e [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) para acessar dados armazenados no Data Lake Storage Gen2.

-   **Um superconjunto de permissões POSIX**: O modelo de segurança do Data Lake Gen2 dá suporte a permissões POSIX e ACL juntamente com alguma granularidade adicional específica para o Data Lake Storage Gen2. As configurações podem ser definidas por meio do Gerenciador de Armazenamento ou por meio de estruturas como Hive e Spark.

-   **Econômico**: O Data Lake Storage Gen2 oferece transações e capacidade de armazenamento de baixo custo. À medida que os dados transitam por todo o seu ciclo de vida, as taxas de faturamento mudam, mantendo os custos no mínimo por meio de recursos integrados, como [ ciclo de vida de armazenamento do Blur do Azure ](storage-lifecycle-management-concepts.md).

-   **Driver otimizado**: O driver ABFS é [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de Big Data. As APIs REST correspondentes são exibidas pelo ponto de extremidade, `dfs.core.windows.net`.

### <a name="scalability"></a>Escalabilidade

O Armazenamento do Azure é dimensionável por design, independentemente de você acessar via Data Lake Storage Gen2 ou interfaces de armazenamento do Blob. É capaz de armazenar e servir *muitos exabytes de dados*. Essa quantidade de armazenamento está disponível com taxa de transferência medida em gigabits por segundo (Gbps) em altos níveis de operações de entrada/saída por segundo (IOPS). Além da persistência, o processamento é executado em latências por solicitação quase constantes que são medidas nos níveis de serviço, conta e arquivo.

### <a name="cost-effectiveness"></a>Custo-benefício

Um dos muitos benefícios da criação do Data Lake Storage Gen2 no Armazenamento de Blobs do Azure é o baixo custo de capacidade de armazenamento e transações. Ao contrário de outros serviços de armazenamento em nuvem, os dados armazenados no Data Lake Storage Gen2 não precisam ser movidos ou transformados antes de executar a análise. Para obter mais informações sobre preços, confira [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage).

Além disso, recursos como o [namespace hierárquico](data-lake-storage-namespace.md) melhoram significativamente o desempenho geral de muitas tarefas de análise. Essa melhoria no desempenho significa que você precisa de menos poder computacional para processar a mesma quantidade de dados, resultando em um custo total de propriedade (TCO) mais baixo para o trabalho analítico de ponta a ponta.

### <a name="one-service-multiple-concepts"></a>Um único serviço, vários conceitos

O Data Lake Storage Gen2 é um recurso adicional para análise de big data, criado com base em Armazenamento de Blobs do Azure. Embora haja muitos benefícios ao aproveitar os componentes da plataforma existente de Blobs para criar e operar data lakes para análise, isso leva a vários conceitos que descrevem as mesmas coisas, compartilhadas.

A seguir estão as entidades equivalentes, conforme descrito pelos diferentes conceitos. Salvo especificado em contrário, essas entidades são sinônimos diretos:

| Conceito                                | Organização de nível superior | Organização de nível inferior                                            | Contêiner de dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – Armazenamento de objetos de uso geral | Contêiner              | Diretório virtual (SDK somente – não fornece a manipulação atômica) | Blob           |
| Azure Data Lake Storage Gen2 – Armazenamento de Análise          | Contêiner            | Diretório                                                           | Arquivo           |

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

Recursos do Armazenamento de Blobs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md) e [políticas de gerenciamento do ciclo de vida do armazenamento de blobs](storage-lifecycle-management-concepts.md) agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de Armazenamento de Blobs sem perder o acesso a esses recursos. 

Para obter uma lista de recursos do Armazenamento de Blobs com suporte, confira [Recursos do Armazenamento de Blobs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviço do Azure com suporte

O Data Lake Storage Gen2 é compatível com vários serviços do Azure que você pode usar para ingerir dados, executar análises e criar representações visuais. Para obter uma lista de serviços do Azure com suporte, confira [Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre suportam o Data Lake Storage Gen2. Para obter uma lista completa, confira [Plataformas de software livre que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


