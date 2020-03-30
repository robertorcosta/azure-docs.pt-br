---
title: Atividade de Fluxo de Dados
description: Como executar fluxos de dados de dentro de um pipeline de fábrica de dados.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463043"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Atividade de fluxo de dados na fábrica de dados do Azure

Use a atividade fluxo de dados para transformar e mover dados através do mapeamento de fluxos de dados. Se você é novo em fluxos de dados, consulte [visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md)

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Fluxo | A referência ao Fluxo de Dados que está sendo executado | Referência ao DataFlow | Sim
integraçãoTempo de execução | O ambiente de computação em que o fluxo de dados é executado. Se não for especificado, o tempo de execução do Azure Integration de resolução automática será usado | IntegraçãoRuntimeReference | Não
compute.coreCount | O número de núcleos usados no cluster de faíscas. Só pode ser especificado se o tempo de execução do Azure Integration de resolução automática for usado | 8, 16, 32, 48, 80, 144, 272 | Não
computação.computeType | O tipo de computação usada no cluster de faíscas. Só pode ser especificado se o tempo de execução do Azure Integration de resolução automática for usado | "Geral", "ComputeOptimized", "MemoryOptimized" | Não
staging.linkedService | Se você estiver usando uma fonte ou pia SQL DW, a conta de armazenamento usada para o estágio PolyBase | Referência de Serviço de Linked | Somente se o fluxo de dados ler ou escrever para um SQL DW
staging.folderPath | Se você estiver usando uma fonte de DW SQL ou afundar, o caminho da pasta na conta de armazenamento blob usada para o estágio PolyBase | String | Somente se o fluxo de dados ler ou escrever para um SQL DW

![Executar fluxo de dados](media/data-flow/activity-data-flow.png "Executar fluxo de dados")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Computação de fluxo de dados de tamanho dinâmico em tempo de execução

As propriedades Core Count e Compute Type podem ser definidas dinamicamente para ajustar-se ao tamanho dos dados de origem de entrada em tempo de execução. Use atividades de pipeline como Lookup ou Get Metadata para encontrar o tamanho dos dados do conjunto de dados de origem. Em seguida, use Adicionar conteúdo dinâmico nas propriedades de atividade fluxo de dados.

![Fluxo dinâmico de dados](media/data-flow/dyna1.png "Fluxo dinâmico de dados")

[Aqui está um breve tutorial de vídeo explicando esta técnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Tempo de execução da integração de fluxo de dados

Escolha qual tempo de execução de integração usar para a execução da atividade do Fluxo de Dados. Por padrão, a Data Factory usará o tempo de execução do Azure Integration de resolução automática com quatro núcleos de trabalhadores e sem tempo de vida (TTL). Este IR tem um tipo de computação de propósito geral e funciona na mesma região que sua fábrica. Você pode criar seus próprios Runtimes de integração do Azure que definem regiões específicas, tipo de computação, contagem de núcleos e TTL para a execução da atividade de fluxo de dados.

Para execuções de dutos, o cluster é um cluster de trabalho, que leva vários minutos para iniciar antes do início da execução. Se nenhum TTL for especificado, esse tempo de inicialinicializar será necessário em cada execução do pipeline. Se você especificar um TTL, um pool de cluster quente permanecerá ativo pelo tempo especificado após a última execução, resultando em tempos de inicialinicialidade mais curtos. Por exemplo, se você tiver um TTL de 60 minutos e executar um fluxo de dados nele uma vez por hora, o pool de clusterpermanecerá ativo. Para obter mais informações, consulte [o tempo de execução da integração do Azure](concepts-integration-runtime.md).

![Tempo de execução da integração do Azure](media/data-flow/ir-new.png "Tempo de execução da integração do Azure")

> [!NOTE]
> A seleção de tempo de execução de integração na atividade Fluxo de dados só se aplica a *execuções desencadeadas* do seu pipeline. Depuração do pipeline com fluxos de dados é executado no cluster especificado na sessão de depuração.

### <a name="polybase"></a>PolyBase

Se você estiver usando um Azure SQL Data Warehouse como um dissipador ou fonte, você deve escolher um local de preparação para a carga em lote do PolyBase. O PolyBase permite o carregamento em lote em massa, em vez de carregar a linha de dados por linha. O PolyBase reduz drasticamente o tempo de carga no SQL DW.

## <a name="parameterizing-data-flows"></a>Parametrização de fluxos de dados

### <a name="parameterized-datasets"></a>Conjuntos de dados parametrizados

Se o fluxo de dados usar conjuntos de dados parametrizados, defina os valores dos parâmetros na guia **Configurações.**

![Executar parâmetros de fluxo de dados](media/data-flow/params.png "Parâmetros")

### <a name="parameterized-data-flows"></a>Fluxos de dados parametrizados

Se o fluxo de dados for parametrizado, defina os valores dinâmicos dos parâmetros de fluxo de dados na guia **Parâmetros.** Você pode usar a linguagem de expressão do pipeline ADF (apenas para tipos de string) ou a linguagem de expressão de fluxo de dados para atribuir valores de parâmetros dinâmicos ou literais. Para obter mais informações, consulte [Parâmetros de fluxo de dados](parameters-data-flow.md).

![Executar exemplo de parâmetro de fluxo de dados](media/data-flow/parameter-example.png "Exemplo de parâmetro")

### <a name="parameterized-compute-properties"></a>Propriedades computacionais parametrizadas.

Você pode parametrizar a contagem de núcleos ou o tipo de computação se usar o tempo de execução do Azure Integration de resolução automática e especificar valores para compute.coreCount e compute.computeType.

![Executar exemplo de parâmetro de fluxo de dados](media/data-flow/parameterize-compute.png "Exemplo de parâmetro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Depuração do pipeline da atividade de fluxo de dados

Para executar uma execução de pipeline de depuração com uma atividade de fluxo de dados, você deve ligar o modo de depuração do fluxo de dados através do controle deslizante **Debug de fluxo de dados** na barra superior. O modo Debug permite executar o fluxo de dados contra um cluster Spark ativo. Para obter mais informações, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

![Botão depurar](media/data-flow/debugbutton.png "Botão depurar")

O pipeline de depuração é executado contra o cluster de depuração ativa, não o ambiente de tempo de execução de integração especificado nas configurações de atividade fluxo de dados. Você pode escolher o ambiente de computação de depuração ao iniciar o modo de depuração.

## <a name="monitoring-the-data-flow-activity"></a>Monitorando a atividade de fluxo de dados

A atividade de Fluxo de Dados tem uma experiência especial de monitoramento onde você pode visualizar particionamento, tempo de estágio e informações de linhagem de dados. Abra o painel de monitoramento através do ícone de óculos em **Ações**. Para obter mais informações, consulte [Monitorando fluxos de dados](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Use a atividade de fluxo de dados resulta em uma atividade subseqüente

A atividade de fluxo de dados produz métricas sobre o número de linhas escritas em cada pia e linhas lidas de cada fonte. Esses resultados são devolvidos na `output` seção do resultado da execução da atividade. As métricas retornadas estão no formato do json abaixo.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Por exemplo, para chegar ao número de linhas escritas em um dissipador chamado 'sink1' em uma atividade chamada 'dataflowActivity', use `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`.

Para obter o número de linhas lidas de uma fonte chamada 'source1' que foi usada naquela pia, use `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`.

> [!NOTE]
> Se uma pia tiver zero linhas escritas, ela não aparecerá em métricas. A existência pode ser `contains` verificada usando a função. Por exemplo, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` verificará se alguma linha foi escrita para afundar1.

## <a name="next-steps"></a>Próximas etapas

Consulte as atividades de fluxo de controle suportadas pela Data Factory: 

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
