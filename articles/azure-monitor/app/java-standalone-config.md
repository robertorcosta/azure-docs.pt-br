---
title: Monitorar aplicativos Java em qualquer lugar-Azure Monitor Application Insights
description: Monitoramento de desempenho de aplicativos sem código para aplicativos Java em execução em qualquer ambiente, sem instrumentar o aplicativo. Encontre a causa raiz dos problemas d usando o rastreamento distribuído e o mapa do aplicativo.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9b90f8b9336111438b4b832d557d448470959255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537650"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opções de configuração-agente autônomo Java para Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Cadeia de conexão e nome da função

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

A cadeia de conexão é necessária e o nome da função é importante sempre que você estiver enviando dados de aplicativos diferentes para o mesmo recurso de Application Insights.

Você encontrará mais detalhes e opções de configuração adicionais abaixo para obter mais detalhes.

## <a name="configuration-file-path"></a>Caminho do arquivo de configuração

Por padrão, Application Insights visualização do Java 3,0 espera que o arquivo de configuração seja nomeado `ApplicationInsights.json` e esteja localizado no mesmo diretório que `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Você pode especificar seu próprio caminho de arquivo de configuração usando o

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` variável de ambiente ou
* `applicationinsights.configurationFile` Propriedade do sistema Java

Se você especificar um caminho relativo, ele será resolvido em relação ao diretório onde `applicationinsights-agent-3.0.0-PREVIEW.5.jar` está localizado.

## <a name="connection-string"></a>Cadeia de conexão

Isso é necessário. Você pode encontrar a cadeia de conexão em seu recurso de Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights cadeia de conexão":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Você também pode definir a cadeia de conexão usando a variável de ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Se a cadeia de conexão não for definida, o agente Java será desabilitado.

## <a name="cloud-role-name"></a>Nome da função de nuvem

O nome da função de nuvem é usado para rotular o componente no mapa do aplicativo.

Se você quiser definir o nome da função de nuvem:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Se o nome da função de nuvem não for definido, o nome do recurso de Application Insights será usado para rotular o componente no mapa do aplicativo.

Você também pode definir o nome da função de nuvem usando a variável de ambiente `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Instância de função de nuvem

A instância de função de nuvem usa como padrão o nome do computador.

Se você quiser definir a instância de função de nuvem para algo diferente, em vez do nome da máquina:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Você também pode definir a instância de função de nuvem usando a variável de ambiente `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Captura de log de aplicativo

Application Insights visualização do Java 3,0 captura automaticamente o log do aplicativo por meio de Log4J, Logback e Java. util. Logging.

Por padrão, ele capturará todo o log executado no `INFO` nível ou acima.

Se você quiser alterar esse limite:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

Você também pode definir o limite de log usando a variável de ambiente `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` .

Esses são os `threshold` valores válidos que você pode especificar no `ApplicationInsights.json` arquivo e como eles correspondem aos níveis de log entre diferentes estruturas de registro em log:

| valor de limite   | Log4J  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAIS             | FATAIS  | ERRO   | SEVERE  |
| ERRO (ou grave) | ERRO  | ERRO   | SEVERE  |
| AVISAR (ou aviso) | AVISAR   | AVISAR    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| Depurar (ou bem)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| RASTREAMENTO (ou melhor) | RASTREAMENTO  | RASTREAMENTO   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Métricas JMX

Se você tiver algumas métricas JMX que você está interessado em capturar:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Você também pode definir as métricas JMX usando a variável de ambiente `APPLICATIONINSIGHTS_JMX_METRICS` .

Esse conteúdo de variável de ambiente deve ser dados JSON que correspondem à estrutura acima, por exemplo, `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Metaspace", "attribute": "Usage.used", "display": "MetaSpace Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (incluindo métricas do acionador do Spring boot)

Se seu aplicativo usar [micrometer](https://micrometer.io), Application insights 3,0 (começando com a visualização. 2) agora captura métricas enviadas ao registro global micrometer.

Se seu aplicativo usar o [acionador do Spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), Application insights 3,0 (começando com a visualização. 4) agora captura métricas configuradas pelo acionador do Spring boot (que usa micrometer, mas não usa o registro global micrometer).

Se você quiser desabilitar esses recursos:

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

Por padrão, Application Insights visualização do Java 3,0 envia uma métrica de pulsação uma vez a cada 15 minutos. Se você estiver usando a métrica de pulsação para disparar alertas, poderá aumentar a frequência dessa pulsação:

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
> Não é possível diminuir a frequência dessa pulsação, pois os dados de pulsação também são usados para rastrear Application Insights uso.

## <a name="sampling"></a>amostragem

A amostragem será útil se você precisar reduzir o custo.
A amostragem é executada como uma função na ID da operação (também conhecida como ID de rastreamento), para que a mesma ID de operação sempre resulte na mesma decisão de amostragem. Isso garante que você não obterá partes de uma transação distribuída amostrada enquanto outras partes dela são amostradas.

Por exemplo, se você definir a amostragem como 10%, verá apenas 10% das suas transações, mas cada uma dessas 10% terá detalhes completos da transação de ponta a ponta.

Veja um exemplo de como definir a amostragem para **10% de todas as transações** -certifique-se de definir a taxa de amostragem que está correta para o caso de uso:

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

Você também pode definir o percentual de amostragem usando a variável de ambiente `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

## <a name="http-proxy"></a>Proxy HTTP

Se seu aplicativo estiver protegido por um firewall e não puder se conectar diretamente a Application Insights (consulte [endereços IP usados por Application insights](./ip-addresses.md)), você poderá configurar Application insights visualização do Java 3,0 para usar um proxy http:

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

"Self-Diagnostics" refere-se ao log interno de Application Insights visualização do Java 3,0.

Isso pode ser útil para descobrir e diagnosticar problemas com Application Insights si mesmo.

Por padrão, ele faz logon no console com nível `warn` , correspondendo a essa configuração:

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

Os níveis válidos são,,,, `OFF` `ERROR` `WARN` `INFO` `DEBUG` e `TRACE` .

Se você quiser fazer logon em um arquivo em vez de fazer logon no console do:

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

Ao usar o registro em log de arquivo, quando o arquivo atingir `maxSizeMB` , ele será sobreposição, mantendo apenas o arquivo de log concluído mais recentemente, além do arquivo de log atual.
