---
title: Atualizando de 2. x Azure Monitor Application Insights Java
description: Atualizando do Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354953"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Atualizando do SDK do Java do Application Insights 2. x

Se você já estiver usando Application Insights Java SDK 2. x em seu aplicativo, não será necessário removê-lo.
O agente do Java 3,0 o detectará e irá capturar e correlacionar qualquer telemetria personalizada que você estiver enviando por meio do SDK do Java 2. x, ao mesmo tempo em que suprimirá qualquer coleção automática executada pelo SDK do Java 2. x para evitar a telemetria duplicada.

Se você estiver usando o agente Application Insights 2. x, será necessário remover o `-javaagent:` ARG JVM que estava apontando para o agente 2. x.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers e TelemetryProcessors

O Java SDK 2. x TelemetryInitializers e o TelemetryProcessors não serão executados ao usar o agente 3,0.
Muitos dos casos de uso exigidos anteriormente podem ser resolvidos em 3,0 Configurando [dimensões personalizadas](./java-standalone-config.md#custom-dimensions) ou configurando [processadores de telemetria](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Vários aplicativos em uma única JVM

Atualmente, o 3,0 dá suporte apenas a uma única [cadeia de conexão e nome de função](./java-standalone-config.md#connection-string-and-role-name) por processo em execução. Em particular, você não pode ter vários aplicativos Web Tomcat na mesma implantação do Tomcat usando cadeias de conexão diferentes ou nomes de função diferentes ainda.

## <a name="http-request-telemetry-names"></a>Nomes de telemetria de solicitação HTTP

Os nomes de telemetria de solicitação HTTP em 3,0 foram alterados para geralmente fornecer uma exibição agregada melhor no portal de Application Insights U/X.

No entanto, para alguns aplicativos, você ainda pode preferir a exibição agregada no U/X que foi fornecido pelos nomes de telemetria anteriores. nesse caso, você pode usar o recurso de visualização de processadores de telemetria no 3,0 para retornar aos nomes anteriores.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Para prefixar o nome da telemetria com o método http ( `GET` , `POST` , etc.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Para definir o nome da telemetria para o caminho de URL completo

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
