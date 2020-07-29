---
title: Atividade de Fluxo de Dados
description: Como executar fluxos de dados de dentro de um pipeline de data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 04/30/2020
ms.openlocfilehash: 1004f7fcc8ff93a170b724a6d8b1c2216b9c39b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84726944"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Atividade de fluxo de dados no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a atividade fluxo de dados para transformar e mover dados por meio de fluxos de dados de mapeamento. Se você não estiver familiarizado com fluxos de dados, consulte [visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md)

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
Flow | A referência ao fluxo de dados que está sendo executado | DataFlowReference | Sim
integrationRuntime | O ambiente de computação no qual o fluxo de dados é executado. Se não for especificado, a solução de tempo de execução de integração do Azure será usada automaticamente. | IntegrationRuntimeReference | Não
Compute. coreCount | O número de núcleos usados no cluster do Spark. Só poderá ser especificado se a resolução automática do tempo de execução de integração do Azure for usada | 8, 16, 32, 48, 80, 144, 272 | Não
Compute. computetype | O tipo de computação usado no cluster do Spark. Só poderá ser especificado se a resolução automática do tempo de execução de integração do Azure for usada | "Geral", "ComputeOptimized", "MemoryOptimized" | Não
preparo. linkedService | Se você estiver usando uma fonte ou coletor do SQL DW, a conta de armazenamento usada para preparo do polybase | LinkedServiceReference | Somente se o fluxo de dados lê ou grava em um SQL DW
preparo. folderPath | Se você estiver usando uma fonte ou coletor do SQL DW, o caminho da pasta na conta de armazenamento de BLOBs usada para preparo do polybase | String | Somente se o fluxo de dados lê ou grava em um SQL DW

![Executar fluxo de dados](media/data-flow/activity-data-flow.png "Executar fluxo de dados")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dimensionar dinamicamente a computação de fluxo de dados em tempo de execução

As propriedades de tipo de computação e contagem de núcleo podem ser definidas dinamicamente para ajustar o tamanho de seus dados de origem de entrada em tempo de execução. Use atividades de pipeline como Pesquisar ou obter metadados para localizar o tamanho dos dados do conjunto de dados de origem. Em seguida, use adicionar conteúdo dinâmico nas propriedades da atividade fluxo de dados.

![Fluxo de Dados Dinâmicos](media/data-flow/dyna1.png "Fluxo de dados dinâmicos")

[Este é um breve tutorial de vídeo explicando essa técnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Tempo de execução de integração de fluxo de dados

Escolha qual Integration Runtime usar para a execução da atividade de fluxo de dados. Por padrão, Data Factory usará a solução de tempo de execução de integração do Azure automaticamente com quatro núcleos de trabalho e nenhuma TTL (vida útil). Esse IR tem um tipo de computação de uso geral e é executado na mesma região que a sua fábrica. Você pode criar seus próprios tempos de execução de integração do Azure que definem regiões específicas, tipo de computação, contagens de núcleos e TTL para a execução da atividade de fluxo de dados.

Para execuções de pipeline, o cluster é um cluster de trabalho, que leva vários minutos para ser iniciado antes do início da execução. Se nenhum TTL for especificado, esse tempo de inicialização será necessário em cada execução de pipeline. Se você especificar um TTL, um pool de clusters quente permanecerá ativo durante o tempo especificado após a última execução, resultando em tempos de inicialização mais curtos. Por exemplo, se você tiver um TTL de 60 minutos e executar um fluxo de dados nele uma vez por hora, o pool de clusters permanecerá ativo. Para obter mais informações, consulte [tempo de execução de integração do Azure](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> A seleção de Integration Runtime na atividade de fluxo de dados só se aplica a *execuções disparadas* de seu pipeline. A depuração do pipeline com fluxos de dados é executada no cluster especificado na sessão de depuração.

### <a name="polybase"></a>PolyBase

Se você estiver usando um SQL Data Warehouse do Azure como um coletor ou fonte, deverá escolher um local de preparo para a carga do lote do polybase. O polybase permite o carregamento em lote em massa, em vez de carregar os dados linha por linha. O polybase reduz drasticamente o tempo de carregamento no SQL DW.

## <a name="parameterizing-data-flows"></a>Parametrizando fluxos de dados

### <a name="parameterized-datasets"></a>Conjuntos de valores com parâmetros

Se o fluxo de dados usar conjuntos de dados com parâmetros, defina os valores de parâmetro na guia **configurações** .

![Executar parâmetros de fluxo de dados](media/data-flow/params.png "Parâmetros")

### <a name="parameterized-data-flows"></a>Fluxos de dados com parâmetros

Se o fluxo de dados for parametrizado, defina os valores dinâmicos dos parâmetros de fluxo de dados na guia **parâmetros** . Você pode usar a linguagem de expressão de pipeline do ADF ou a linguagem de expressão de fluxo de dados para atribuir valores de parâmetro dinâmicos ou literais. Para obter mais informações, consulte [parâmetros de fluxo de dados](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Propriedades de computação parametrizadas.

Você pode parametrizar a contagem de núcleos ou o tipo de computação se usar a resolução automática do tempo de execução de integração do Azure e especificar valores para COMPUTE. coreCount e COMPUTE. computetype.

![Exemplo do parâmetro executar fluxo de dados](media/data-flow/parameterize-compute.png "Exemplo de parâmetro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Depuração de pipeline da atividade de fluxo de dados

Para executar uma execução de pipeline de depuração com uma atividade de fluxo de dados, você deve alternar o modo de depuração do fluxo de dados por meio do controle deslizante de **depuração do fluxo de dados** na barra superior. O modo de depuração permite executar o fluxo de dados em um cluster do Spark ativo. Para saber mais, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

![Botão depurar](media/data-flow/debugbutton.png "Botão depurar")

O pipeline de depuração é executado no cluster de depuração ativa, não no ambiente do Integration Runtime especificado nas configurações de atividade do fluxo de dados. Você pode escolher o ambiente de computação de depuração ao iniciar o modo de depuração.

## <a name="monitoring-the-data-flow-activity"></a>Monitorando a atividade de fluxo de dados

A atividade de fluxo de dados tem uma experiência de monitoramento especial, na qual você pode exibir informações sobre particionamento, tempo de estágio e linhagem de dados. Abra o painel Monitoramento por meio do ícone óculos em **ações**. Para obter mais informações, consulte [monitorando fluxos de dados](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Usar resultados de atividade de fluxo de dados em uma atividade subsequente

A atividade de fluxo de dados gera métricas referentes ao número de linhas gravadas em cada coletor e linhas lidas de cada fonte. Esses resultados são retornados na `output` seção do resultado da execução da atividade. As métricas retornadas estão no formato do JSON abaixo.

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

Por exemplo, para chegar ao número de linhas gravadas em um coletor chamado ' sink1 ' em uma atividade denominada ' dataflowActivity ', use `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Para obter o número de linhas lidas de uma fonte denominada ' origem1 ' que foi usada nesse coletor, use `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Se um coletor tiver zero linhas gravadas, ele não aparecerá em métricas. A existência pode ser verificada usando a `contains` função. Por exemplo, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` verificará se alguma linha foi gravada em sink1.

## <a name="next-steps"></a>Próximas etapas

Consulte atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
