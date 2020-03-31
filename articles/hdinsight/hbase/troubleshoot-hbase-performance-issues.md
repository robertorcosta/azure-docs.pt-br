---
title: Solucionar problemas de desempenho do Apache HBase no Azure HDInsight
description: Várias diretrizes e dicas de ajuste de desempenho do Apache HBase para obter o melhor desempenho no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887148"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Solucionar problemas de desempenho do Apache HBase no Azure HDInsight

Este artigo descreve várias diretrizes e dicas de ajuste de desempenho do Apache HBase para obter um desempenho ideal no Azure HDInsight. Muitas dessas dicas dependem da carga de trabalho específica e do padrão de leitura/gravação/varredura. Antes de aplicar as alterações de configuração em um ambiente de produção, teste-as completamente.

## <a name="hbase-performance-insights"></a>Insights de desempenho do HBase

O principal gargalo na maioria das cargas de trabalho do HBase é o Write Ahead Log (WAL). Isso afeta severamente o desempenho de gravação. O HDInsight HBase tem um modelo separado de computação de armazenamento. Os dados são armazenados remotamente no Azure Storage, embora as máquinas virtuais hospedem os servidores da região. Até recentemente, o WAL também foi escrito para a Azure Storage. No HDInsight, esse comportamento ampliou esse gargalo. O recurso [Gravações Aceleradas](./apache-hbase-accelerated-writes.md) foi projetado para resolver esse problema. Ele grava os discos ssd ssd do WAL para Azure Premium. Isso beneficia tremendamente o desempenho de gravação, e ajuda muitos problemas enfrentados por algumas das cargas de trabalho intensivas em gravação.

Para obter uma melhoria significativa nas operações de leitura, use [a Conta de Armazenamento Premium Block Blob](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) como seu armazenamento remoto. Essa opção só é possível se o recurso WAL estiver ativado.

## <a name="compaction"></a>Compactação

A compactação é outro gargalo potencial que é fundamentalmente acordado na comunidade. Por padrão, a compactação principal é desativada nos clusters HDInsight HBase. A compactação está desativada porque, embora seja um processo intensivo em recursos, os clientes têm total flexibilidade para programá-lo de acordo com suas cargas de trabalho. Por exemplo, eles podem programá-lo durante o horário de pico. Além disso, a localidade de dados não é uma preocupação porque nosso armazenamento é remoto (apoiado pelo Azure Storage) em vez de um Sistema de Arquivos Distribuídos Hadoop local (HDFS).

Os clientes devem agendar uma grande compactação à sua conveniência. Se eles não fizerem essa manutenção, a compactação afetará negativamente o desempenho da leitura no longo prazo.

Para operações de varredura, latências médias que são muito superiores a 100 milissegundos devem ser motivo de preocupação. Verifique se a compactação principal foi agendada com precisão.

## <a name="apache-phoenix-workload"></a>Carga de trabalho apache phoenix

Responder às seguintes perguntas irá ajudá-lo a entender melhor sua carga de trabalho Apache Phoenix:

* Todas as suas "leituras" estão traduzindo para exames?
    * Se sim, quais são as características desses exames?
    * Você otimizou seu esquema de tabela phoenix para estes scans, incluindo indexação apropriada?
* Você já `EXPLAIN` usou a declaração para entender os planos de consulta que suas "leituras" geram?
* Seus escritos são "upsert-selects"?
    * Se assim for, eles também estariam fazendo varreduras. A latência esperada para varreduras é de aproximadamente 100 milissegundos, em comparação com 10 milissegundos para o ponto fica no HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia de teste e monitoramento de métricas

Se você está usando benchmarks como o Yahoo! Cloud Serving Benchmark, JMeter ou Pherf para testar e ajustar o desempenho, certifique-se de que:

- As máquinas clientes não se tornam um gargalo. Para fazer isso, verifique o uso da CPU nas máquinas clientes.

- As configurações do lado do cliente, como o número de threads, são ajustadas adequadamente para saturar a largura de banda do cliente.

- Os resultados dos testes são registrados com precisão e sistematicamente.

Se suas consultas de repente começaram a fazer muito pior do que antes, verifique se há possíveis bugs no código do aplicativo. Está gerando de repente grandes quantidades de dados inválidos? Se for, pode aumentar as latências de leitura.

## <a name="migration-issues"></a>Problemas de migração

Se você está migrando para o Azure HDInsight, certifique-se de que sua migração seja feita de forma sistemática e precisa, de preferência via automação. Evite migração manual. Certifique-se de que:

- Os atributos da tabela são migrados com precisão. Atributos podem incluir como compressão, filtros de flores, e assim por diante.

- As configurações de salem em mesas de Phoenix são mapeadas adequadamente para o novo tamanho do cluster. Por exemplo, o número de baldes de sal deve ser um múltiplo do número de nódulos operários no cluster. E você deve usar um múltiplo que é um fator da quantidade de manchas quentes.

## <a name="server-side-configuration-tunings"></a>Ajustes de configuração do lado do servidor

No HDInsight HBase, os HFiles são armazenados em armazenamento remoto. Quando há uma falha no cache, o custo das leituras é maior do que os sistemas locais, porque os dados em sistemas locais são apoiados pelo HDFS local. Para a maioria dos cenários, o uso inteligente de caches HBase (cache de bloco e cache de balde) foi projetado para contornar esse problema. Nos casos em que o problema não é contornado, o uso de uma conta de blob de bloco premium pode ajudar esse problema. O driver do Windows Azure Storage Blob conta com certas propriedades, como `fs.azure.read.request.size` buscar dados em blocos com base no que ele determina para ser lido (seqüencial versus aleatório), de modo que pode continuar a haver casos de latências mais altas com leituras. Através de experimentos empíricos, descobrimos que definir`fs.azure.read.request.size`o tamanho do bloco de solicitação de leitura ( ) para 512 KB e corresponder ao tamanho do bloco das tabelas HBase para ser do mesmo tamanho produz o melhor resultado na prática.

Para a maioria dos clusters de nós `bucketcache` de grande porte, o HDInsight HBase fornece como um arquivo `regionservers`em um SSD Premium local que está conectado à máquina virtual, que é executada . O uso do cache off-heap, em vez disso, pode fornecer alguma melhoria. Esta solução tem a limitação de usar a memória disponível e potencialmente ser menor do que o cache baseado em arquivos, por isso pode nem sempre ser a melhor escolha.

A seguir estão alguns dos outros parâmetros específicos que afinamos, e que pareciam ajudar em diferentes graus:

- Aumentar `memstore` o tamanho de 128 MB padrão para 256 MB. Normalmente, essa configuração é recomendada para cenários de gravação pesada.

- Aumente o número de threads dedicados à compactação, da configuração padrão de **1** para **4**. Esta configuração é relevante se observarmos compações menores freqüentes.

- Evite `memstore` bloquear o flush por causa do limite da loja. Para fornecer este buffer, aumente a `Hbase.hstore.blockingStoreFiles` configuração para **100**.

- Para controlar as descargas, use as seguintes configurações:

    - `Hbase.regionserver.maxlogs`: **140** (evita descargas por causa dos limites do WAL)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0h55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0h60**

- Configurações específicas de Phoenix para ajuste de pool de segmentos:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Outras configurações específicas de Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (se houver grandes ou muitas procurações de índice)

    - `Phoenix.stats.updateFrequency`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 hora**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Tempos de tempo da RPC: **3 minutos**

   - Os tempos de tempo do RPC incluem tempo out do HBase RPC, tempo out do scanner do cliente HBase e tempo de tempo de consulta phoenix. 
   - Certifique-se `hbase.client.scanner.caching` de que o parâmetro está definido para o mesmo valor tanto na extremidade do servidor quanto no final do cliente. Se eles não forem os mesmos, essa configuração `OutOfOrderScannerException`leva a erros de final de cliente relacionados a . Esta configuração deve ser definida como um valor baixo para grandes varreduras. Nós definimos este valor para **100**.

## <a name="other-considerations"></a>Outras considerações

A seguir, parâmetros adicionais para considerar a sintonia:

- `Hbase.rs.cacheblocksonwrite`– por padrão no HDI, esta configuração é definida como **true**.

- Configurações que permitem adiar uma compactação menor para depois.

- Configurações experimentais, como ajustar percentuais de filas reservadas para solicitações de leitura e gravação.

## <a name="next-steps"></a>Próximas etapas

Se o seu problema não for resolvido, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esta é a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ele conecta a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

- Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Sua assinatura do Microsoft Azure inclui acesso ao gerenciamento de assinaturas e suporte de faturamento, e suporte técnico é fornecido através de um dos planos de suporte do [Azure](https://azure.microsoft.com/support/plans/).
