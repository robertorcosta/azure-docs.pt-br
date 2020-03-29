---
title: Como depurar UDFs - Azure Digital Twins | Microsoft Docs
description: Saiba mais sobre abordagens recomendadas para depurar funções definidas pelo usuário no Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511630"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Como depurar funções definidas pelo usuário em Gêmeos Digitais do Azure

Este artigo resume como diagnosticar e depurar funções definidas pelo usuário no Azure Digital Twins. Em seguida, ele identifica alguns dos cenários mais comuns encontrados ao depurá-las.

>[!TIP]
> Leia [Como configurar monitoramento e registro em log](./how-to-configure-monitoring.md) para saber mais sobre a configuração de ferramentas de depuração em Gêmeos Digitais do Azure usando Logs de Atividades, Logs de Diagnóstico e Azure Monitor.

## <a name="debug-issues"></a>Problemas de depuração

Saber como diagnosticar problemas dentro do Azure Digital Twins permite analisar efetivamente os problemas, identificar as causas dos problemas e fornecer soluções adequadas para eles.

Uma variedade de ferramentas de registro, análise e diagnóstico são fornecidas para esse fim.

### <a name="enable-logging-for-your-instance"></a>Habilite o registro para sua instância

Os Gêmeos Digitais do Azure dão suporte a registro em log, monitoramento e análise robustos. Os desenvolvedores de soluções podem usar registros do Monitor Do Azure, registros de diagnóstico, registros de atividades e outros serviços para suportar as complexas necessidades de monitoramento de um aplicativo IoT. As opções de registro em log podem ser combinadas para consultar ou exibir registros em vários serviços e para fornecer cobertura de registro em log granular para muitos serviços.

* Para configurar configuração específica do Azure Digital Twins, leia [Como configurar monitoramento e registro](./how-to-configure-monitoring.md).
* Consulte a visão geral do [Azure Monitor](../azure-monitor/overview.md) para saber mais sobre as configurações de log poderosas habilitadas através do Azure Monitor.
* Revise o artigo [Colete e consuma dados de log de seus recursos do Azure](../azure-monitor/platform/platform-logs-overview.md) para configurar configurações de registro de diagnóstico em Gêmeos Digitais Azure através do portal Azure, Azure CLI ou PowerShell.

Uma vez configurado, você poderá selecionar todas as categorias de log, métricas e usar poderosos espaços de trabalho de análise de log do Azure Monitor para suportar seus esforços de depuração.

### <a name="trace-sensor-telemetry"></a>Sensor de telemetria de rastreamento

Para rastrear telemetria do sensor, verifique se as configurações de diagnóstico estão habilitadas para sua instância dos Gêmeos Digitais do Azure. Em seguida, verifique se todas as categorias de log desejadas estão selecionadas. Por último, confirme se os registros desejados estão sendo enviados para os registros do Monitor do Azure.

Para corresponder a uma mensagem de telemetria do sensor para seus respectivos logs, você pode especificar uma ID de Correlação nos dados de evento que estão sendo enviados. Para fazer isso, defina a propriedade `x-ms-client-request-id` para um GUID.

Depois de enviar a telemetria, abra as análises de log do Azure Monitor para consultar os logs usando o ID de correlação definido:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valor da consulta | Substitua por |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | A ID de Correlação que foi especificada nos dados de evento |

Para ler todas as consultas de logs de telemetria recentes:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Se você habilitar o registro para sua função definida pelo usuário, `UserDefinedFunction`esses logs aparecerão na instância de análise de log com a categoria . Para recuperá-los, digite a seguinte condição de consulta em análises de log:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Para obter mais informações sobre operações de consulta avançada, leia [Introdução às consultas](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identificar problemas comuns

O diagnóstico e a identificação de problemas comuns são importantes durante a solução de problemas em sua solução. Vários problemas que são comumente encontrados ao desenvolver funções definidas pelo usuário estão resumidos nas subseções a seguir.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Verificar se uma atribuição de função foi criada

Sem uma atribuição de função criada na API de Gerenciamento, a função definida pelo usuário não tem acesso para executar nenhuma ação, como enviar notificações, recuperar metadados e definir valores computadores na topologia.

Verifique se existe uma atribuição de função para sua função definida pelo usuário por meio de sua API de Gerenciamento:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Valor de parâmetro | Substitua por |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | A ID da função definida pelo usuário para a qual serão recuperadas as atribuições de função|

Leia [Como criar uma atribuição de função para a função definida pelo usuário](./how-to-user-defined-functions.md), se nenhuma atribuição de função existir.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Verifique se o correspondente funciona para a telemetria de um sensor

Com a chamada a seguir à API de Gerenciamento das instâncias dos Gêmeos Digitais do Azure, você poderá determinar se um determinado correspondente se aplica para o sensor determinado.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A ID do correspondente que você deseja avaliar |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor que você deseja avaliar |

Resposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Verifique o que um sensor dispara

Com a seguinte chamada para as APIs de Gerenciamento dos Gêmeos Digitais do Azure, você pode determinar os identificadores de suas funções definidas pelo usuário disparadas pela telemetria de entrada do sensor determinado:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor para enviar telemetria |

Resposta:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problema com o recebimento de notificações

Quando você não estiver recebendo notificações da função definida pelo usuário disparada, confirme que seu parâmetro de tipo de objeto de topologia corresponde ao tipo de identificador que está sendo usado.

**Incorreto** Exemplo:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Esse cenário ocorre porque o identificador usado refere-se a um sensor, enquanto o tipo de objeto de topologia especificado é `Space`.

**Correto** Exemplo:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A maneira mais fácil de não ter esse problema é usar o método `Notify` no objeto de metadados.

Exemplo:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Exceções de diagnóstico comuns

Se habilitar as configurações de diagnóstico, você poderá encontrar estas exceções comuns:

1. **Limitação**: se sua função definida pelo usuário exceder os limites da taxa de execução descritos no artigo [Limites de serviço](./concepts-service-limits.md), ela será limitada. Nenhuma outra operação será executada com êxito até que os limites de limitação expirem.

1. **Dados Não Encontrados**: se sua função definida pelo usuário tentar acessar metadados que não existem, a operação falhará.

1. **Não Autorizado**: se sua função definida pelo usuário não tiver uma atribuição de função definida ou não tiver permissões suficientes para acessar alguns metadados da topologia, a operação falhará.

## <a name="next-steps"></a>Próximas etapas

- Saiba como habilitar [logs e monitoramento](./how-to-configure-monitoring.md) nos Gêmeos Digitais do Azure.

- Leia o [artigo de registro de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md) para obter mais opções de registro do Azure.
