---
title: Monitorar a atividade da cópia
description: Saiba como monitorar a execução da atividade de cópia na Fábrica de Dados do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 6494352bf957af83b45488493bf12a094c730c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79125752"
---
# <a name="monitor-copy-activity"></a>Monitorar a atividade da cópia

Este artigo descreve como monitorar a execução da atividade de cópia na Fábrica de Dados do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="monitor-visually"></a>Monitorar visualmente

Depois de criar e publicar um pipeline na Azure Data Factory, você pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Você pode monitorar todas as suas operações de pipeline nativamente na experiência do usuário da Fábrica de Dados Do Azure. Saiba mais sobre o monitoramento da Fábrica de Dados do Azure em geral a partir do [Visualmonitor Azure Data Factory](monitor-visually.md).

Para monitorar a execução da atividade do Copiar, vá para a sua fábrica de dados **Autor & Monitor UI.** Na guia **Monitor,** você vê uma lista de corridas de pipeline, clique no link do nome do **pipeline** para acessar a lista de atividades executadas na execução do pipeline.

![Monitorar a execução da atividade de cópia](./media/copy-activity-overview/monitor-pipeline-run.png)

Neste nível, você pode ver links para copiar entrada de atividade, saída e erros (se a execução da atividade copiar falhar), bem como estatísticas como duração/status. Clicar no botão **Detalhes** (óculos) ao lado do nome da atividade de cópia lhe dará detalhes profundos sobre a execução da atividade de cópia. 

![Monitorar a execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run.png)

Nesta exibição de monitoramento gráfico, a Fábrica de Dados do Azure apresenta as informações de execução da atividade de cópia, incluindo volume de leitura/escrita de dados, número de arquivos/linhas de dados copiados da fonte para o sink, throughput, as configurações aplicadas para o seu cenário de cópia, os passos que a atividade de cópia passa com durações e detalhes correspondentes e muito mais. Consulte [esta tabela](#monitor-programmatically) sobre cada métrica possível e sua descrição detalhada. 

Em alguns cenários, quando você executa uma atividade copiar na Fábrica de Dados, você verá **"Dicas de ajuste de desempenho"** na parte superior da exibição de monitoramento de atividade de cópia, conforme mostrado no exemplo. As dicas dizem o gargalo identificado pela ADF para a execução específica da cópia, juntamente com sugestão sobre o que mudar para impulsionar o throughput de cópia. Saiba mais sobre [dicas de ajuste de desempenho automático](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Os **detalhes e durações da execução** inferior descrevem os principais passos que sua atividade de cópia passa, o que é especialmente útil para solucionar problemas no desempenho da cópia. O gargalo da sua cópia é aquele com maior duração. Consulte o [desempenho da atividade de cópia de solução de problemas](copy-activity-performance-troubleshooting.md) para saber o que cada etapa representa e a orientação detalhada de solução de problemas.

**Exemplo: Copiar do Amazon S3 para o Azure Data Lake Storage Gen2**

![Monitorar detalhes da execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorar programaticamente

Os detalhes de execução da atividade de cópia e as características de desempenho também são retornados na seção**Saída de resultado**  > da execução de atividade de **cópia,** que é usada para renderizar a exibição de monitoramento de iu. A seguir está uma lista completa de propriedades que podem ser devolvidas. Você verá apenas as propriedades aplicáveis ao seu cenário de cópia. Para obter informações sobre como monitorar a atividade é executada de forma programática em geral, consulte [Programáticamente monitorar uma fábrica de dados do Azure](monitor-programmatically.md).

| Nome da propriedade  | Descrição | Unidade na saída |
|:--- |:--- |:--- |
| dataRead | A quantidade real de dados lidos da fonte. | Valor Int64, em bytes |
| dataWritten | O real montagem de dados escritos/comprometidos com a pia. O tamanho pode `dataRead` ser diferente do tamanho, pois relaciona como cada armazenamento de dados armazena os dados. | Valor Int64, em bytes |
| filesRead | O número de arquivos lidos da fonte baseada em arquivos. | Valor Int64 (nenhuma unidade) |
| filesWritten | O número de arquivos gravados/comprometidos com a pia baseada em arquivos. | Valor Int64 (nenhuma unidade) |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas no armazenamento de dados de origem durante a execução da atividade Copiar. | Valor Int64 (nenhuma unidade) |
| sinkPeakConnections | Número máximo de conexões simultâneas estabelecidas no armazenamento de dados do dissipador durante a execução da atividade Copiar. | Valor Int64 (nenhuma unidade) |
| linhasLeia | Número de linhas lidas a partir da fonte (não aplicável para cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsCopied | Número de linhas copiadas para afundar (não aplicável para cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de filas incompatíveis que foram ignoradas. Você pode permitir que linhas incompatíveis `enableSkipIncompatibleRow` sejam ignoradas por configuração para true. | Valor Int64 (nenhuma unidade) |
| copyDuration | Duração da execução da cópia. | Valor int32, em segundos |
| throughput | Taxa de transferência de dados. | Número de ponto flutuante, em KBps |
| sourcePeakConnections | Número máximo de conexões simultâneas estabelecidas no armazenamento de dados de origem durante a execução da atividade Copiar. | Valor Int32 (sem unidade) |
| sinkPeakConnections| Número máximo de conexões simultâneas estabelecidas no armazenamento de dados do dissipador durante a execução da atividade Copiar.| Valor Int32 (sem unidade) |
| sqlDwPolyBase | Se o PolyBase é usado quando os dados são copiados para o SQL Data Warehouse. | Boolean |
| redshiftUnload | Se o UNLOAD é usado quando os dados são copiados do Redshift. | Boolean |
| hdfsDistcp | Se o DistCp é usado quando os dados são copiados do HDFS. | Boolean |
| effectiveIntegrationRuntime | O tempo de execução de integração (IR) ou `<IR name> (<region if it's Azure IR>)`tempos de execução usados para alimentar a execução da atividade, no formato . | Texto (cadeia de caracteres) |
| usedDataIntegrationUnits | As unidades de integração de dados efetivas durante a cópia. | Valor Int32 |
| usedParallelCopies | ParallelCopies efetivos durante a cópia. | Valor Int32 |
| redirectRowPath | Caminho para o registro de linhas incompatíveis ignoradas no `redirectIncompatibleRowSettings` armazenamento blob que você configura na propriedade. Ver [Tolerância a falhas](copy-activity-overview.md#fault-tolerance). | Texto (cadeia de caracteres) |
| executionDetails | Mais detalhes sobre as etapas pelas quais a atividade copiar passa e as etapas, durações, configurações e assim por diante. Nós não recomendamos que você analise esta seção porque ela pode mudar. Para entender melhor como ele ajuda você a entender e solucionar o desempenho da cópia, consulte a seção [Monitorar visualmente.](#monitor-visually) | Array |
| perfRecomendação | Copiar dicas de ajuste de desempenho. Consulte [dicas de ajuste de desempenho](copy-activity-performance-troubleshooting.md#performance-tuning-tips) para obter detalhes. | Array |

**Exemplo:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

\-[Visão geral da atividade de cópia](copy-activity-overview.md)

\-[Desempenho da atividade de cópia](copy-activity-performance.md)