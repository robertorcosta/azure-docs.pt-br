---
title: Criar dependências de gatilho de janela de queda
description: Saiba como criar dependência em um gatilho de janela em cascata no Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 0557c9b9eb65654c4a11c1389ace4776ab60a61d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532563"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de gatilho de janela em cascata

Este artigo fornece etapas para criar uma dependência em um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos de Janela em Cascata, consulte [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certificar-se de que um gatilho executará somente após a execução com êxito de outro gatilho no data factory, use esse recurso avançado para criar uma dependência de janela em cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na interface do usuário do Data Factory

Para criar dependência em um gatilho, selecione **Gatilho > Avançado > Novo** e, em seguida, escolha o gatilho para depender do deslocamento e tamanho apropriados. Selecione **Concluir** e publique as alterações do data factory para que as dependências entrem em vigor.

![Criação de Dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Criação de Dependência")

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Um gatilho de janela de queda com uma dependência tem as seguintes propriedades:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

A tabela a seguir fornece a lista de atributos necessários para definir uma dependência de Janela em Cascata.

| **Nome da propriedade** | **Descrição**  | **Tipo** | **Obrigatório** |
|---|---|---|---|
| type  | Todos os gatilhos da janela em cascata existentes são exibidos neste menu suspenso. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerTriggerReference | Sim |
| deslocamento | Deslocamento do gatilho de dependência. Fornecer um valor no formato de intervalo de tempo e compensações negativas e positivas são permitidas. Essa propriedade é obrigatória se o gatilho estiver dependendo de si mesmo e em todos os outros casos for opcional. A autodependência deverá sempre ser um deslocamento negativo. Se nenhum valor especificado, a janela é a mesma do próprio gatilho. | Timespan<br/>(hh:mm:ss) | Autodependência: Sim<br/>Outro: Não |
| tamanho | Tamanho da janela em cascata de dependência. Fornecer um valor de tempo positivo. Essa propriedade é opcional. | Timespan<br/>(hh:mm:ss) | Não  |

> [!NOTE]
> Um gatilho de janela de queda pode depender de um máximo de dois outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de autodependência de janela em cascata

Em cenários em que o gatilho não deve prosseguir para a próxima janela até que a janela anterior seja concluída com sucesso, construa uma autodependência. Um gatilho de autodependência que depende do sucesso de corridas anteriores de si mesmo dentro do rh anterior terá as propriedades abaixo:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Cenários de uso e exemplos

Abaixo estão ilustrações de cenários e uso de propriedades de dependência de janelas em queda.

### <a name="dependency-offset"></a>Deslocamento de dependência

![Exemplo de deslocamento](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Exemplo de deslocamento")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Autodependência

![Autodependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Autodependência")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho de janela em cascata

Um trabalho diário de processamento de telemetria, dependendo de outro trabalho diário, agregando a produção dos últimos sete dias e gera fluxos de janela de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si próprio

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de autodependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exemplo de autodependência")

Para uma demonstração sobre como criar pipelines dependentes em sua Fábrica de Dados Do Azure usando o gatilho da janela de queda, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Monitorar dependências

Você pode monitorar a cadeia de dependência e as janelas correspondentes da página de monitoramento de execução de gatilhos. Navegue até  **Monitoramento > Execuções de Gatilho**. Na coluna ações, você pode executar o gatilho ou visualizar suas dependências.

![Monitorar execuções de gatilho](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorar execuções de gatilho")

Se você clicar em 'Exibir dependências do gatilho', poderá ver o status das dependências. Se um dos gatilhos de dependência falhar, você deve reexecutá-lo com sucesso para que o gatilho dependente seja executado. Um gatilho de janela caindo esperará por dependências por sete dias antes de cronometrar.

![Monitorar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorar dependências")

Para obter um visual mais visual para visualizar o cronograma de dependência do gatilho, selecione a exibição de Gantt.

![Monitorar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorar dependências")

## <a name="next-steps"></a>Próximas etapas

* Revisão [Como criar um gatilho de janela caindo](how-to-create-tumbling-window-trigger.md)
