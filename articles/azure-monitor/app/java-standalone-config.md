---
title: Monitore aplicativos Java em qualquer lugar - Azure Monitor Application Insights
description: Monitoramento de desempenho de aplicativos sem código para aplicativos Java em execução em qualquer ambiente sem instrumentar o aplicativo. Encontre a causa raiz dos problemas d usando rastreamento distribuído e mapa de aplicativo.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641882"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opções de configuração - Agente autônomo Java para insights de aplicativos do Monitor Azure



## <a name="connection-string-and-role-name"></a>String de conexão e nome da função

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

A seqüência de conexões é necessária e o nome da função é importante sempre que você estiver enviando dados de diferentes aplicativos para o mesmo recurso application Insights.

Você encontrará mais detalhes e opções adicionais de configuração abaixo para mais detalhes.

## <a name="configuration-file-path"></a>Caminho do arquivo de configuração

Por padrão, o Application Insights Java 3.0 `ApplicationInsights.json`Preview espera que o arquivo de `applicationinsights-agent-3.0.0-PREVIEW.jar`configuração seja nomeado e esteja localizado no mesmo diretório que .

Você pode especificar seu próprio caminho de arquivo de configuração usando qualquer um

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`variável de ambiente, ou
* `applicationinsights.configurationFile`Propriedade do sistema Java

Se você especificar um caminho relativo, ele `applicationinsights-agent-3.0.0-PREVIEW.jar` será resolvido em relação ao diretório onde está localizado.

## <a name="connection-string"></a>Cadeia de conexão

Isso é necessário. Você pode encontrar sua seqüência de conexões no recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="String de conexão insights de aplicativos":::

Você também pode definir a seqüência de conexão usando a variável `APPLICATIONINSIGHTS_CONNECTION_STRING`ambiente .

## <a name="cloud-role-name"></a>Nome da função na nuvem

O nome da função cloud é usado para rotular o componente no mapa do aplicativo.

Se você quiser definir o nome da função nuvem:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Se o nome da função na nuvem não estiver definido, o nome do recurso Application Insights será usado para rotular o componente no mapa do aplicativo.

Você também pode definir o nome `APPLICATIONINSIGHTS_ROLE_NAME`da função nuvem usando a variável ambiente .

## <a name="cloud-role-instance"></a>Instância de função de nuvem

A instância da função nuvem é padrão para o nome da máquina.

Se você quiser definir a instância de função de nuvem para algo diferente do nome da máquina:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Você também pode definir a instância `APPLICATIONINSIGHTS_ROLE_INSTANCE`de função na nuvem usando a variável ambiente .

## <a name="application-log-capture"></a>Captura de registro de aplicativos

O Application Insights Java 3.0 Preview captura automaticamente o registro de aplicativos via Log4j, Logback e java.util.logging.

Por padrão, ele capturará `WARN` todos os registros realizados em nível ou acima.

Se você quiser alterar esse limite:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Estes são `threshold` os valores válidos que você pode especificar no `ApplicationInsights.json` arquivo e como eles correspondem aos níveis de registro em diferentes frameworks de registro:

| `threshold`  | Log4j  | Logback | JUL     |
|--------------|--------|---------|---------|
| OFF          | OFF    | OFF     | OFF     |
| Fatal        | Fatal  | ERROR   | SEVERE  |
| ERRO/GRAVE | ERROR  | ERROR   | SEVERE  |
| AVISO/AVISO | Avisar   | Avisar    | WARNING |
| INFO         | INFO   | INFO    | INFO    |
| CONFIG       | DEBUG  | DEBUG   | CONFIG  |
| DEPURAÇÃO/MULTA   | DEBUG  | DEBUG   | FINE    |
| FINER        | DEBUG  | DEBUG   | FINER   |
| TRACE/FINEST | RASTREAMENTO  | RASTREAMENTO   | FINEST  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Métricas JMX

Se você tem algumas métricas JMX que você está interessado em capturar:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Micrometer

Por padrão, se o aplicativo usar [micrometro,](https://micrometer.io)o Application Insights 3.0 (começando com o Preview.2) agora se adiciona ao registro global do Micômetro e captura métricas do Micômetro.

Se você quiser desativar esse recurso:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Pulsação

Por padrão, o Application Insights Java 3.0 Preview envia uma métrica de batimentos cardíacos uma vez a cada 15 minutos. Se você estiver usando a métrica do batimento cardíaco para disparar alertas, você pode aumentar a freqüência deste batimento cardíaco:

```json
{
  "instrumentationSettings": {
    "preview": {
        "heartbeat": {
            "intervalSeconds": 60
        }
    }
  }
}
```

> [!NOTE]
> Não é possível diminuir a freqüência desse batimento cardíaco, pois os dados de batimentos cardíacos também são usados para rastrear o uso do Application Insights.

## <a name="sampling"></a>amostragem

A amostragem é útil se você precisar reduzir o custo.
A amostragem é realizada como uma função no ID da operação (também conhecido como ID de rastreamento), de modo que o mesmo ID de operação sempre resultará na mesma decisão amostral. Isso garante que você não obtenha partes de uma transação distribuída amostradas enquanto outras partes dela são amostradas.

Por exemplo, se você definir a amostragem para 10%, você verá apenas 10% de suas transações, mas cada uma dessas 10% terá detalhes completos de transação completa.

Aqui está um exemplo de como definir a amostragem para **10% de todas as transações** - por favor, certifique-se de definir a taxa de amostragem correta para o seu caso de uso:

```json
{
  "instrumentationSettings": {
    "preview": {
        "sampling": {
            "fixedRate": {
                "percentage": 10
            }
          }
        }
    }
}
```

## <a name="http-proxy"></a>HTTP Proxy

Se o aplicativo estiver por trás de um firewall e não puder se conectar diretamente ao Application Insights (veja [endereços IP usados pelo Application Insights),](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)você pode configurar o Application Insights Java 3.0 Preview para usar um proxy HTTP:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Autodiagnóstico

"Autodiagnóstico" refere-se ao registro interno do Application Insights Java 3.0 Preview.

Isso pode ser útil para detectar e diagnosticar problemas com o próprio Application Insights.

Por padrão, ele faz logon para console com nível, `warn`correspondente a esta configuração:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "console",
            "level": "WARN"
        }
    }
  }
}
```

Os níveis `OFF` `ERROR`válidos `INFO` `DEBUG`são, `TRACE`e `WARN`.

Se você quiser fazer login em um arquivo em vez de fazer login no console:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "file",
            "directory": "/var/log/applicationinsights",
            "level": "WARN",
            "maxSizeMB": 10
        }    
    }
  }
}
```

Ao usar o registro de `maxSizeMB`arquivos, uma vez que o arquivo é atingido, ele será revertido, mantendo apenas o arquivo de log concluído mais recentemente, além do arquivo de log atual.
