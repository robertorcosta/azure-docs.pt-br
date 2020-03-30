---
title: Armazenamento transacional e analítico distribuído globalmente (em pré-visualização privada) para contêineres Azure Cosmos
description: Saiba mais sobre armazenamento transacional e analítico e suas opções de configuração para contêineres Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623391"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Armazenamento transacional e analítico distribuído globalmente para contêineres Azure Cosmos

O contêiner Azure Cosmos é apoiado internamente por dois motores de armazenamento - motor de armazenamento transacional e um mecanismo de armazenamento analítico updatable (em visualização privada). Ambos os mecanismos de armazenamento são estruturados por log e otimizados para gravação para atualizações mais rápidas. No entanto, cada um deles é codificado de forma diferente:

* **Mecanismo de armazenamento transacional** – É codificado em formato orientado a linha para leituras e consultas transacionais rápidas.

* **Mecanismo de armazenamento analítico** - É codificado em formato columnar para consultas e varreduras analíticas rápidas.

![Motores de armazenamento e mapeamento da API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

O mecanismo de armazenamento transacional é apoiado por SSDs locais, enquanto o armazenamento analítico é armazenado em um armazenamento SSD off-cluster barato. A tabela a seguir captura as diferenças notáveis entre o armazenamento transacional e o analítico.


|Recurso  |Armazenamento transacional  |Armazenamento analítico |
|---------|---------|---------|
|Armazenamento máximo por um contêiner Azure Cosmos |   Ilimitado      |    Ilimitado     |
|Armazenamento máximo por uma chave de partição lógica   |   20 GB      |   Ilimitado      |
|Codificação de armazenamento  |   Orientado a linhas, usando um formato interno.   |   Orientado a colunas, usando o formato Apache Parquet. |
|Localidade de armazenamento |   Armazenamento replicado com backup de SSDs locais/intra-cluster. |  Armazenamento replicado com suporte por SSDs remotos/off-cluster baratos.       |
|Durabilidade  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|APIs que acessam os dados  |   SQL, MongoDB, Cassandra, Gremlin, Tables, etc.       | Apache Spark         |
|Retenção (Tempo de vida ou TTL)   |  Orientado por políticas, configurado no contêiner Azure Cosmos usando a `DefaultTimeToLive` propriedade.       |   Orientado por políticas, configurado no contêiner Azure Cosmos usando a `ColumnStoreTimeToLive` propriedade.      |
|Preço por GB    |   Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Preço para transações de armazenamento    |  Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Benefícios do armazenamento transacional e analítico

### <a name="no-etl-operations"></a>Sem operações de ETL

Os gasodutos analíticos tradicionais são complexos com vários estágios cada um exigindo operações De extract-transform-load (ETL) de e para os níveis de computação e armazenamento. Resulta em arquiteturas complexas de processamento de dados. O que significa altos custos para múltiplos estágios de armazenamento e computação, e alta latência devido a grandes volumes de dados transferidos entre vários estágios de armazenamento e computação.  

Não há sobrecarga de realização de operações ETL com o Azure Cosmos DB. Cada contêiner Do Azure Cosmos é apoiado por mecanismos de armazenamento transacionais e analíticos, e a transferência de dados entre o mecanismo de armazenamento transacional e analítico é feita dentro do mecanismo de banco de dados, e sem qualquer saltador de rede. A latência e o custo resultantes são significativamente menores em comparação com as soluções analíticas tradicionais. E você recebe um único sistema de armazenamento distribuído globalmente para cargas de trabalho transacionais e analíticas.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Armazene várias versões, atualize e consulte o armazenamento analítico

O armazenamento analítico é totalmente updatable, e contém o histórico completo da versão de todas as atualizações transacionais que ocorreram no contêiner Azure Cosmos.

Qualquer atualização feita no armazenamento transacional é garantida para ser visível ao armazenamento analítico dentro de 30 segundos. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Armazenamento analítico distribuído globalmente e multimestre

Se a conta do Azure Cosmos for escopo para uma única região, os dados armazenados (em armazenamento transacional e analítico) nos contêineres também são escopo para uma única região. Por outro lado, se a conta do Azure Cosmos for distribuída globalmente, os dados armazenados nos contêineres também são distribuídos globalmente.

Para contas Do Azure Cosmos configuradas com várias regiões de gravação, as gravações no contêiner (tanto para o armazenamento transacional quanto para o armazenamento analítico) são sempre realizadas na região local e, portanto, são rápidas.

Para contas azure Cosmos de uma única ou várias regiões, independentemente se a região de gravação única (único mestre) ou várias regiões de gravação (também conhecidas como multi-master), tanto leituras/consultas transacionais quanto analíticas são realizadas localmente na determinada região.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolamento de desempenho entre cargas transacionais e analíticas

Em uma determinada região, as cargas de trabalho transacionais operam contra o armazenamento transacional/linha do contêiner. Por outro lado, as cargas de trabalho analíticas operam contra o armazenamento analítico/coluna do seu contêiner. Os dois motores de armazenamento operam de forma independente e proporcionam um isolamento rigoroso de desempenho entre as cargas de trabalho.

As cargas transacionais consomem o throughput provisionado (RUs). Ao contrário das cargas de trabalho transacionais, o throughput de cargas de trabalho analíticas é baseado no consumo real. As cargas de trabalho analíticas consomem recursos demanda.

## <a name="next-steps"></a>Próximas etapas

* [Vida útil no Azure Cosmos DB](time-to-live.md)
