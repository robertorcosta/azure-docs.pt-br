---
title: Criar as dependências de gatilho de janela em cascata no Azure Data Factory
description: Saiba como criar dependência em um gatilho de janela em cascata no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 3780a50429b95c402459866b7e3c87e8a91207d3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683695"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Criar uma dependência de gatilho de janela em cascata

Este artigo fornece etapas para criar uma dependência em um gatilho de janela em cascata. Para obter informações gerais sobre gatilhos de Janela em Cascata, consulte [Como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md).

Para criar uma cadeia de dependência e certificar-se de que um gatilho executará somente após a execução com êxito de outro gatilho no data factory, use esse recurso avançado para criar uma dependência de janela em cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Criar uma dependência na interface do usuário do Data Factory

Para criar dependência em um gatilho, selecione **Gatilho > Avançado > Novo** e, em seguida, escolha o gatilho para depender do deslocamento e tamanho apropriados. Selecione **Concluir** e publique as alterações do data factory para que as dependências entrem em vigor.

![Criação de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Criação de dependência")

## <a name="tumbling-window-dependency-properties"></a>Propriedades de dependência de janela em cascata

Um gatilho de janela em cascata com uma dependência tem as seguintes propriedades:

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

| **Nome da Propriedade** | **Descrição**  | **Tipo** | **Obrigatório** |
|---|---|---|---|
| Tipo  | Todos os gatilhos da janela em cascata existentes são exibidos neste menu suspenso. Escolha o gatilho para assumir a dependência.  | TumblingWindowTriggerDependencyReference ou SelfDependencyTumblingWindowTriggerReference | Sim |
| deslocamento | Deslocamento do gatilho de dependência. Forneça um valor no formato de intervalo de tempo e deslocamentos negativos e positivos são permitidos. Essa propriedade será obrigatória se o gatilho estiver dependendo de si mesmo e em todos os outros casos for opcional. A autodependência deverá sempre ser um deslocamento negativo. Se nenhum valor for especificado, a janela será igual ao próprio gatilho. | Timespan<br/>(hh:mm:ss) | Dependência automática: Sim<br/>Outro: não |
| tamanho | Tamanho da janela em cascata de dependência. Forneça um valor de TimeSpan positivo. Essa propriedade é opcional. | Timespan<br/>(hh:mm:ss) | Não  |

> [!NOTE]
> Um gatilho de janela em cascata pode depender de um máximo de dois outros gatilhos.

## <a name="tumbling-window-self-dependency-properties"></a>Propriedades de autodependência de janela em cascata

Em cenários em que o gatilho não deve prosseguir para a próxima janela até que a janela anterior seja concluída com êxito, crie uma autodependência. Um gatilho de dependência automática dependente do sucesso das execuções anteriores de si mesmo no RH anterior terá as propriedades abaixo:

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
## <a name="usage-scenarios-and-examples"></a>Cenários e exemplos de uso

Veja abaixo as ilustrações de cenários e o uso de propriedades de dependência de janela em cascata.

### <a name="dependency-offset"></a>Deslocamento de dependência

![Exemplo de deslocamento](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Exemplo de deslocamento")

### <a name="dependency-size"></a>Tamanho da dependência

![Exemplo de tamanho](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Exemplo de tamanho")

### <a name="self-dependency"></a>Autodependência

![Dependência automática](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Autodependência")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dependência de outro gatilho de janela em cascata

Um trabalho de processamento diário de telemetria dependendo de outro trabalho diário agregando a saída dos últimos sete dias e gera fluxos de janela sem interrupção de sete dias:

![Exemplo de dependência](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Exemplo de dependência")

### <a name="dependency-on-itself"></a>Dependência de si próprio

Um trabalho diário sem lacunas nos fluxos de saída do trabalho:

![Exemplo de dependência automática](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Exemplo de dependência automática")

## <a name="monitor-dependencies"></a>Monitorar dependências

Você pode monitorar a cadeia de dependência e as janelas correspondentes na página monitoramento de execução de gatilho. Navegue até  **Monitoramento > Execuções de Gatilho**. Na coluna ações, você pode executar novamente o gatilho ou exibir suas dependências.

![Monitorar execuções de gatilho](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorar execuções de gatilho")

Se você clicar em ' Exibir dependências de gatilho ', poderá ver o status das dependências. Se um dos disparadores de dependência falhar, você deverá executá-lo com êxito para que o gatilho dependente seja executado. Um gatilho de janela em cascata aguardará as dependências por sete dias antes de atingir o tempo limite.

![Monitorar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorar dependências")

Para obter mais visual para exibir a agenda de dependência do gatilho, selecione o modo de exibição de Gantt.

![Monitorar dependências](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorar dependências")

## <a name="next-steps"></a>Próximas etapas

* Examine [como criar um gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md)
