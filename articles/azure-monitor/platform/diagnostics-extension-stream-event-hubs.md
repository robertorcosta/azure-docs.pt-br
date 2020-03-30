---
title: Envie dados da extensão de diagnósticos do Windows Azure para o Azure Event Hubs
description: Configure a extensão de diagnóstico no Azure Monitor para enviar dados ao Azure Event Hub para que você possa encaminhá-los para locais fora do Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672524"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Envie dados da extensão de diagnósticos do Windows Azure para o Azure Event Hubs
A extensão de diagnóstico do Azure é um agente no Azure Monitor que coleta dados de monitoramento do sistema operacional convidado e cargas de trabalho de máquinas virtuais do Azure e outros recursos de computação. Este artigo descreve como enviar dados da extensão do Windows Azure Diagnostic (WAD) para [o Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) para que você possa encaminhar para locais fora do Azure.

## <a name="supported-data"></a>Dados suportados

Os dados coletados do sistema operacional convidado que podem ser enviados para o Event Hubs incluem o seguinte. Outras fontes de dados coletadas pela WAD, incluindo registros de IIS e dumps de acidentes, não podem ser enviadas para o Event Hubs.

* Eventos de ETW (Rastreamento de Eventos para Windows)
* Contadores de desempenho
* Registros de eventos do Windows, incluindo registros de aplicativos no registro de eventos do Windows
* Logs de infraestrutura do Diagnóstico do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Extensão de diagnóstico do Windows 1.6 ou superior. Consulte [as versões do esquema de configuração de extensão do Azure Diagnostics e o histórico](diagnostics-extension-versions.md) de um histórico de versões e uma visão geral da [extensão do Azure Diagnostics](diagnostics-extension-overview.md) para obter recursos suportados.
* O namespace do Event Hubs deve ser sempre provisionado. Consulte Comece com o Event Hubs para obter [detalhes.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>Esquema de configuração
Consulte Instalar e configurar a [extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md) para obter diferentes opções para habilitar e configurar a extensão de diagnóstico e o esquema de configuração do [Azure Diagnostics](diagnostics-extension-schema-windows.md) para uma referência do esquema de configuração. O resto deste artigo descreverá como usar essa configuração para enviar dados para um hub de eventos. 

O Azure Diagnostics sempre envia registros e métricas para uma conta do Azure Storage. Você pode configurar um ou mais *saqueadores de dados* que enviam dados para locais adicionais. Cada pia é definida no [elemento SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) da configuração pública com informações confidenciais na configuração privada. Esta configuração para hubs de eventos usa os valores na tabela a seguir.

| Propriedade | Descrição |
|:---|:---|
| Nome | Nome descritivo para a pia. Usado na configuração para especificar quais fontes de dados enviar para o dissipador. |
| Url  | Url do hub de \<eventos no formulário event-hubs-namespace\>.servicebus.windows.net/\<nome do hub de eventos\>.          |
| CompartilhadoAccessKeyName | Nome de uma política de acesso compartilhado para o hub de eventos que tenha pelo menos **Enviar** autoridade. |
| SharedAccessKey     | Chave primária ou secundária da política de acesso compartilhado para o hub de eventos. |

Exemplos de configurações públicas e privadas são mostrados abaixo. Esta é uma configuração mínima com um único contador de desempenho e registro de eventos para ilustrar como configurar e usar o dissipador de dados do hub de eventos. Consulte o esquema de [configuração do Azure Diagnostics](diagnostics-extension-schema-windows.md) para obter um exemplo mais complexo.

### <a name="public-configuration"></a>Configuração pública

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Configuração privada

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Opções de configuração
Para enviar dados para um dissipador de dados, você especifica o atributo **sinks** no nó da fonte de dados. Onde você coloca o atributo **das pias** determina o escopo da atribuição. No exemplo a seguir, o atributo **sinks** é definido para o nó **PerformanceCounters,** que fará com que todos os contadores de desempenho infantil sejam enviados para o hub de eventos.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


No exemplo a seguir, o atributo **sinks** é aplicado diretamente em três contadores, o que fará com que apenas os contadores de desempenho sejam enviados para o hub de eventos. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Validação de configuração
Você pode usar uma variedade de métodos para validar que os dados estão sendo enviados para o hub de eventos. o método ne simples é usar a captura do Event Hubs conforme descrito em [eventos de captura através do Azure Event Hubs no Azure Blob Storage ou no Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Solucionar problemas dos coletores dos Hubs de Eventos

- Veja a tabela de armazenamento do Azure **WADDiagnosticInfrastructureLogsTable** que contém registros e erros para o próprio Azure Diagnostics. Uma opção é usar uma ferramenta, como o [Gerenciador de Armazenamento do Azure](https://www.storageexplorer.com) , para se conectar a essa conta de armazenamento, exibir essa tabela e adicionar uma consulta por carimbo de data/hora nas últimas 24 horas. Você pode usar a ferramenta para exportar um arquivo .csv e abri-lo em um aplicativo, como o Microsoft Excel. O Excel facilita a pesquisa de cadeias de caracteres de cartão, como **EventHubs**, para ver qual erro é relatado.  

- Verifique se o seu hub de eventos foi provisionado com êxito. Todas as informações de conexão na seção **PrivateConfig** da configuração devem corresponder aos valores do seu recurso, conforme visto no portal. Certifique-se de que você tem uma política SAS definida (*SendRule* no exemplo) no portal e que a permissão *enviar* é concedida.  

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de Hubs de Evento](../../event-hubs/event-hubs-about.md)
* [Criar um hub de eventos](../../event-hubs/event-hubs-create.md)
* [Perguntas frequentes dos Hubs de Eventos](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



