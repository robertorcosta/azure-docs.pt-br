---
title: Referência host.json para o Azure Functions 2.x
description: Documentação de referência do arquivo host.json do Azure Functions com o runtime v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7b3c9d15fc8cf3a1651c44a5656f731a7820e344
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405479"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>referência host.json para Funções Azure 2.x e posteriores 

> [!div class="op_single_selector" title1="Selecione a versão do tempo de execução das funções do Azure que você está usando: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2+](functions-host-json.md)

O arquivo de metadados *host.json* contém opções de configuração global que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações disponíveis a partir da versão 2.x do tempo de execução funções do Azure.  

> [!NOTE]
> Este artigo é para as funções do Azure 2.x e versões posteriores.  Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicativos de função são gerenciadas nas configurações do aplicativo (para aplicativos [implantados)](functions-app-settings.md) ou no arquivo [local.settings.json](functions-run-local.md#local-settings-file) (para desenvolvimento local).

As configurações no host.json relacionadas às vinculações são aplicadas igualmente a cada função no aplicativo de função. 

## <a name="sample-hostjson-file"></a>Arquivo host.json de exemplo

O seguinte arquivo *host.json* de exemplo para a versão 2.x+ tem todas as opções possíveis especificadas (excluindo qualquer um que seja apenas para uso interno).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

As seções seguintes deste artigo explicam cada propriedade de nível superior. Todas são opcionais, a menos que seja indicado o contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Essa configuração é a filha de [Registro em log](#logging).

Controla opções de Insights de Aplicativos, incluindo [opções de amostragem](./functions-monitoring.md#configure-sampling).

Para obter a estrutura JSON completa, consulte o exemplo anterior [do arquivo host.json](#sample-hostjson-file).

> [!NOTE]
> A amostragem de log pode fazer com que algumas execuções não apareçam na folha do Monitor do Application Insights. Para evitar a `excludedTypes: "Request"` amostragem `samplingSettings` de troncos, adicione ao valor.

| Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| amostragemConfigurações | n/d | Consulte [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| habilitarLiveMetrics | true | Permite a coleta de métricas ao vivo. |
| habilitardependencyTracking | true | Permite o rastreamento de dependência. |
| habilitarPerformanceCountersCollection | true | Permite a coleta de contadores de desempenho Kudu. |
| liveMetricsInicializationDelay | 00:00:15 | Apenas para uso interno. |
| httpAutoCollectionOptions | n/d | Consulte [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| configuração de instantâneos | n/d | Consulte [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| isEnabled | true | Habilita ou desabilita a amostragem. | 
| maxTelemetryItemsPerSecond | 20 | O número de itens de telemetria de destino registrado por segundo em cada host de servidor. Se o aplicativo for executado em muitos hosts, reduza esse valor para permanecer dentro da sua taxa de tráfego de destino global. | 
| avaliaçãoIntervalo | 01:00:00 | O intervalo em que a taxa atual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Talvez você queira reduzir esse intervalo se a sua telemetria estiver sujeita a picos repentinos. |
| initialSamplingPercentage| 1.0 | O percentual amostral inicial aplicado no início do processo amostral variou dinamicamente o percentual. Não reduza o valor enquanto estiver depurando. |
| amostragemPercentagePercentageIncreaseTimeTimeout | 00:00:01 | Quando o valor percentual de amostragem muda, essa propriedade determina quanto tempo depois o Application Insights é permitido aumentar a porcentagem de amostragem novamente para capturar mais dados. |
| amostragemPercentagePercentageDecreaseTimeout | 00:00:01 | Quando o valor percentual de amostragem muda, essa propriedade determina quanto tempo depois o Application Insights é permitido reduzir novamente a porcentagem amostral para capturar menos dados. |
| minSamplingPercentageRcent | 0,1 | Como o percentual amostral varia, esta propriedade determina o percentual mínimo de amostragem permitido. |
| maxSamplingPercentagepercentage | 0,1 | Como o percentual amostral varia, esta propriedade determina o percentual máximo de amostragem permitido. |
| movingAverageRatio | 1.0 | No cálculo da média móvel, o peso atribuído ao valor mais recente. Use um valor menor ou igual a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas. |
| excluídosTipos | nulo | Uma lista delimitada de pontos e vírgulas de tipos que você não quer ser amostrado. Os tipos `Dependency`reconhecidos são: `Event`, `Exception`, , `PageView`, `Request`e `Trace`. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não são especificados são amostrados. |
| incluídoTipos | nulo | Uma lista delimitada de ponto e vírgula de tipos que você deseja ser amostrado; uma lista vazia implica todos os tipos. Tipo listado `excludedTypes` em tipos de substituição listados aqui. Os tipos `Dependency`reconhecidos são: `Event`, `Exception`, , `PageView`, `Request`e `Trace`. As instâncias dos tipos especificados são amostradas; os tipos que não são especificados ou implícitos são transmitidos sem amostragem. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| habilitarHttpTriggerExtendedInfoCollection | true | Ativa ou desativa informações de solicitação HTTP estendidas para gatilhos HTTP: cabeçalhos de correlação de solicitação recebidas, suporte a chaves de multi-instrumentação, método HTTP, caminho e resposta. |
| habilitarW3CDistributedTracing | true | Ativa ou desativa o suporte ao protocolo de rastreamento distribuído W3C (e ativa esquema de correlação legado). Ativado por padrão `enableHttpTriggerExtendedInfoCollection` se for verdade. Se `enableHttpTriggerExtendedInfoCollection` for falsa, esta bandeira se aplica apenas a solicitações de saída, não a solicitações recebidas. |
| habilitarResponseHeaderInjection | true | Permite ou desativa a injeção de cabeçalhos de correlação de vários componentes em respostas. A injeção de habilitação permite que o Application Insights construa um Mapa de Aplicativos para quando várias teclas de instrumentação forem usadas. Ativado por padrão `enableHttpTriggerExtendedInfoCollection` se for verdade. Esta configuração não `enableHttpTriggerExtendedInfoCollection` se aplica se for falsa. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguração

Para obter mais informações sobre instantâneos, consulte [Depurar instantâneos em exceções em aplicativos .NET](/azure/azure-monitor/app/snapshot-debugger) e [problemas de solução de problemas que permitem o Depurador de instantâneos do Application Insights ou a visualização de instantâneos](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Propriedade | Padrão | Descrição |
| --------- | --------- | --------- | 
| agentEndpoint | nulo | O ponto final usado para se conectar ao serviço Application Insights Snapshot Debugger. Se for nulo, um ponto final padrão é usado. |
| capturarSnapshotMemoryDememória | 0.5 | O peso dado ao tamanho da memória do processo atual ao verificar se há memória suficiente para tirar um instantâneo. O valor esperado é maior que 0 fração adequada (0 < CaptureSnapshotMemoryWeight < 1). |
| falhalimiteLimite de solicitação | 3 | O limite do número de solicitações com falha para solicitar instantâneos antes que o processador de telemetria seja desativado.|
| handleUntrackedExceptions | true | Habilita ou desativa o rastreamento de exceções que não são rastreadas pela telemetria Application Insights. |
| isEnabled | true | Habilita ou desativa a coleta de instantâneos | 
| isEnabledInDeveloperMode | false | Habilitaou ou desativa a coleta de instantâneos está ativada no modo desenvolvedor. |
| isEnabledWhenOarquivamento | true | Habilita ou desativa a criação de instantâneos mesmo se o Profiler do Application Insights estiver coletando uma sessão detalhada de criação de perfil. |
| isExceptionPontospontosativados | false | Ativa ou desativa a filtragem de exceções. |
| isLowPrioritySnapshotUploader | true | Determina se deve executar o processo SnapshotUploader em prioridade abaixo do normal. |
| máximoCollectionPlanSize | 50 | O número máximo de problemas que podemos rastrear a qualquer momento em uma faixa de um a 9999. |
| máximosnapshotsnecessário | 3 | O número máximo de instantâneos coletados para um único problema, em uma faixa de um a 999. Um problema pode ser considerado como uma declaração de lançamento individual em sua aplicação. Uma vez que o número de instantâneos coletados para um problema atinja esse valor, não serão `problemCounterResetInterval`coletados `thresholdForSnapshotting` mais instantâneos para esse problema até que os contadores de problemas sejam redefinidos (veja ) e o limite seja atingido novamente. |
| problemaCounterResetInterval | 24:00:00 | Quantas vezes redefinir os contadores de problemas em uma faixa de um minuto a sete dias. Quando este intervalo é atingido, todas as contagens de problemas são redefinidas para zero. Os problemas existentes que já atingiram o limite para fazer snapshots, `maximumSnapshotsRequired`mas ainda não geraram o número de instantâneos em , permanecem ativos. |
| fornecerTelemetria anônima | true | Determina se deve enviar o uso anônimo e a telemetria de erro para a Microsoft. Essa telemetria pode ser usada se você entrar em contato com a Microsoft para ajudar a solucionar problemas com o Snapshot Debugger. Também é usado para monitorar padrões de uso. |
| reconecteInterval | 00:15:00 | Quantas vezes nos reconectamos ao ponto final do Depurador de instantâneos. O alcance permitido é de um minuto para um dia. |
| pasta de sombrasCopyFolder | nulo | Especifica a pasta a ser usada para copiar binários de cópia de sombra. Se não for definido, as pastas especificadas pelas seguintes variáveis de ambiente são testadas em ordem: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Se for verdade, apenas uma instância do SnapshotUploader coletará e carregará instantâneos para vários aplicativos que compartilham a InstrumentationKey. Se definido como falso, o SnapshotUploader será exclusivo para cada tuple (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se deve ou não processar instantâneos em um segmento de prioridade de IO baixo. Criar um snapshot é uma operação rápida, mas, para carregar um instantâneo no serviço Snapshot Debugger, ele deve primeiro ser gravado no disco como um minidump. Isso acontece no processo SnapshotUploader. Definir esse valor como verdadeiro usa IO de baixa prioridade para escrever o minidump, que não competirá com seu aplicativo de recursos. Definir esse valor como falso acelera a criação de minidump sem a desoneração da sua aplicação. |
| snapshotsPerDayLimit | 30 | O número máximo de instantâneos permitido em um dia (24 horas). Esse limite também é aplicado no lado de serviço do Application Insights. Os uploads são limitados a 50 por dia por aplicativo (ou seja, por chave de instrumentação). Esse valor ajuda a evitar a criação de instantâneos adicionais que eventualmente serão rejeitados durante o upload. Um valor de zero remove totalmente o limite, o que não é recomendado. |
| snapshotsPerTenMinutesLimit | 1 | O número máximo de instantâneos permitido em 10 minutos. Embora não haja limite superior sobre este valor, tenha cuidado em aumbhá-lo sobre as cargas de trabalho de produção, pois isso poderia afetar o desempenho da sua aplicação. Criar um snapshot é rápido, mas criar um minidump do snapshot e enviá-lo para o serviço Snapshot Debugger é uma operação muito mais lenta que competirá com seu aplicativo por recursos (CPU e I/O). |
| tempFolder | nulo | Especifica a pasta para gravar minidumps e arquivos de log de uploader. Se não estiver definido, *então %TEMP%\Dumps* é usado. |
| thresholdForSnapshotting | 1 | Quantas vezes o Application Insights precisa ver uma exceção antes de pedir instantâneos. |
| uploaderProxy | nulo | Substitui o servidor proxy usado no processo Snapshot Uploader. Você pode precisar usar essa configuração se seu aplicativo se conectar à internet através de um servidor proxy. O Snapshot Collector é executado dentro do processo do aplicativo e usará as mesmas configurações de proxy. No entanto, o Snapshot Uploader é executado como um processo separado e você pode precisar configurar o servidor proxy manualmente. Se esse valor for nulo, o Snapshot Collector tentará detectar automaticamente o endereço do proxy examinando System.Net.WebRequest.DefaultWebProxy e repassando o valor para o Uploader de instantâneo. Se esse valor não for nulo, a detecção automática não será usada e o servidor proxy especificado aqui será usado no Uploader de instantâneo. |

## <a name="cosmosdb"></a>cosmosDb

A definição de configuração pode ser encontrada em [Associações e gatilhos do Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>durableTask

A definição de configuração pode ser encontrada em [Associações para Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

As definições de configuração podem ser encontradas em [Associações e gatilhos do Hub de Eventos](functions-bindings-event-hubs-output.md#host-json). 

## <a name="extensions"></a>extensions

Propriedade que retorna um objeto que contém todas as configurações específicas de associação, como [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensãoBundle 

Os pacotes de extensão permitem adicionar um conjunto compatível de extensões de vinculação de funções ao seu aplicativo de função. Para saber mais, consulte [pacotes de extensão para o desenvolvimento local.](functions-bindings-register.md#extension-bundles)

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>funções

Uma lista de funções que o host de trabalho executa. Uma matriz vazia significa que todas as funções serão executadas. Para uso somente quando [em execução localmente](functions-run-local.md). Em aplicativos de funções no Azure, você deve seguir as etapas em [Como desabilitar funções no Azure Functions](disable-function.md) para desabilitar funções específicas em vez de usar essa configuração.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Ele segue o formato de seqüência de string sinuoso. Em um plano de Consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos e o valor padrão é 5 minutos.  

No plano Premium, a faixa válida é de 1 segundo a 60 minutos, e o valor padrão é de 30 minutos.

Em um plano Dedicated (App Service), não há limite total e o valor padrão é de 30 minutos. Recomenda-se `-1` um valor de execução ilimitada, mas é recomendado manter um limite superior fixo.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração para [monitor de integridade de Host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|Habilitado|true|Especifica se o recurso está habilitado. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de integridade em segundo plano. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante usada em conjunto com a configuração `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de integridade pode falhar antes de uma reciclagem de host ser iniciada.| 
|counterThreshold|0.80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

As definições de configuração podem ser encontradas em [Associações e gatilhos HTTP](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>registro em log

Controla os comportamentos de registro em log do aplicativo de funções, incluindo o Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Define qual nível de log de arquivos está habilitado.  As opções são: `never`, `always` e `debugOnly`. |
|logLevel|n/d|Objeto que define a filtragem da categoria de log para funções no aplicativo. As versões 2.x e posteriores seguem o layout ASP.NET Core para filtragem da categoria log. Esta configuração permite filtrar o registro de funções específicas. Para obter mais informações, consulte [Filtragem de logs](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) na documentação do ASP.NET Core. |
|console|n/d| A configuração de log do [console](#console). |
|applicationInsights|n/d| A configuração [applicationInsights](#applicationinsights). |

## <a name="console"></a>console

Essa configuração é a filha de [Registro em log](#logging). Ela controla o log do console quando não está no modo de depuração.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|isEnabled|false|Habilita ou desabilita o log de console.| 

## <a name="manageddependency"></a>dependência gerenciada

Dependência gerenciada é um recurso que atualmente só é suportado com funções baseadas no PowerShell. Ele permite que as dependências sejam gerenciadas automaticamente pelo serviço. Quando `enabled` a propriedade `true`é `requirements.psd1` definida para , o arquivo é processado. As dependências são atualizadas quando quaisquer versões menores são lançadas. Para obter mais informações, consulte [Dependência gerenciada](functions-reference-powershell.md#dependency-management) no artigo PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas em [Associações e gatilhos da fila de armazenamento](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid

A definição de configuração pode ser encontrada em [Associações e gatilhos do SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

A definição de configuração pode ser encontrada em [Associações e gatilhos do Barramento de Serviço](functions-bindings-service-bus-output.md#host-json).

## <a name="singleton"></a>singleton

Parâmetro de configuração para o comportamento de bloqueio de Singleton. Para obter mais informações, consulte [Problema com o GitHub referente ao suporte de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período em que ocorrem os bloqueios de nível de função. Os bloqueios têm renovação automática.| 
|listenerLockPeriod|00:01:00|O período em que ocorrem os bloqueios de ouvinte.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo usado para a recuperação do bloqueio de ouvinte caso não tenha sido possível adquirir um bloqueio de ouvinte durante a inicialização.| 
|lockAcquisitionTimeout|00:01:00|A quantidade máxima de tempo em que o runtime tenta adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|n/d|O intervalo entre as tentativas de aquisição de bloqueio.| 

## <a name="version"></a>version

Este valor indica a versão esquema do host.json. A seqüência de versões `"version": "2.0"` é necessária para um aplicativo de função que tem como alvo o tempo de execução v2 ou uma versão posterior. Não há alterações no esquema host.json entre v2 e v3.

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios de código compartilhado](functions-reference-csharp.md#watched-directories) que devem ser monitorados quanto a alterações.  Garante que, quando o código nesses diretórios é alterado, as alterações sejam coletadas pelas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como atualizar o arquivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte as configurações globais em variáveis de ambiente](functions-app-settings.md)
