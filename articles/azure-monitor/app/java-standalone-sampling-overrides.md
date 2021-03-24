---
title: Substituições de amostragem (visualização)-Azure Monitor Application Insights para Java
description: Saiba como configurar substituições de amostragem em Azure Monitor Application Insights para Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 03d3093f14d97b2cc64d91e0d1b7adf34204a021
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962451"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Substituições de amostragem (visualização)-Azure Monitor Application Insights para Java

> [!NOTE]
> O recurso de substituições de amostragem está em visualização.

Aqui estão alguns casos de uso para as substituições de amostragem:
 * Suprimir coleta de telemetria para verificações de integridade.
 * Suprimir coleta de telemetria para chamadas de dependência ruidosas.
 * Reduza o ruído de verificações de integridade ou de chamadas de dependência ruidosas sem suprime-las completamente.
 * Colete 100% da telemetria para um tipo de solicitação importante (por exemplo, `/login` ) mesmo que você tenha a amostragem padrão configurada para algo menor.

## <a name="terminology"></a>Terminologia

Antes de aprender sobre as substituições de amostragem, você deve entender o termo *span*. Uma extensão é um termo geral para:

* Uma solicitação de entrada.
* Uma dependência de saída (por exemplo, uma chamada remota para outro serviço).
* Uma dependência em processo (por exemplo, trabalho sendo feito por subcomponentes do serviço).

Para substituições de amostragem, esses componentes de span são importantes:

* Atributos

Os atributos de span representam as propriedades padrão e personalizadas de uma determinada solicitação ou dependência.

## <a name="getting-started"></a>Introdução

Para começar, crie um arquivo de configuração chamado *applicationinsights.jsem*. Salve-o no mesmo diretório que *applicationinsights-Agent- \* . jar*. Use o modelo a seguir.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Como ele funciona

Quando um Span é iniciado, os atributos presentes no intervalo nesse momento são usados para verificar se alguma das substituições de amostragem corresponde.

Se uma das substituições de amostragem corresponder, o percentual de amostragem será usado para decidir se a amostragem deve ser amostrada ou não.

Somente a primeira substituição de amostragem que corresponde é usada.

Se nenhuma substituição de amostragem corresponder:

* Se esse for o primeiro intervalo no rastreamento, a porcentagem de [amostragem normal](./java-standalone-config.md#sampling) será usada.
* Se essa não for a primeira extensão no rastreamento, a decisão de amostragem pai será usada.

> [!IMPORTANT]
> Quando uma decisão é tomada para não coletar um intervalo, todas as extensões de downstream também não serão coletadas, mesmo se houver substituições de amostragem que correspondam ao span downstream.
> Esse comportamento é necessário porque os rastreamentos desfeitos de outra forma resultaram, com o aumento dos estendes de downstream, mas sendo pai de Spans que não foram coletados.

> [!NOTE]
> A decisão de amostragem baseia-se no hash de TraceID (também conhecido como operationId) em um número entre 0 e 100, e esse hash é comparado com a porcentagem de amostragem.
> Como todos os spans em um determinado rastreamento terão o mesmo TraceID, eles terão o mesmo hash e, portanto, a decisão de amostragem será consistente em todo o rastreamento.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Exemplo: suprimir coleta de telemetria para verificações de integridade

Isso irá suprimir a coleta de telemetria para todas as solicitações para `/health-checks` .

Isso também suprimirá a coleta de qualquer extensão downstream (dependências) que normalmente seria coletada `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Exemplo: suprimir coleta de telemetria para uma chamada de dependência ruidosa

Isso irá suprimir a coleta de telemetria para todas as `GET my-noisy-key` chamadas de Redis.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Exemplo: coletar 100% da telemetria para um tipo de solicitação importante

Isso coletará 100% da telemetria para `/login` .

Como as extensões de downstream (dependências) respeitam a decisão de amostragem do pai (ausente em qualquer substituição de amostragem para esse intervalo downstream), elas também serão coletadas para todas as solicitações '/login '.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Atributos de span comuns

Esta seção lista alguns atributos de span comuns que as substituições de amostragem podem usar.

### <a name="http-spans"></a>Spans HTTP

| Atributo  | Type | Descrição | 
|---|---|---|
| `http.method` | Cadeia de caracteres | Método de solicitação HTTP.|
| `http.url` | Cadeia de caracteres | URL de solicitação HTTP completa no formulário `scheme://host[:port]/path?query[#fragment]` . O fragmento geralmente não é transmitido por HTTP. Mas se o fragmento for conhecido, ele deverá ser incluído.|
| `http.status_code` | número | [Código de status de resposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | Cadeia de caracteres | Tipo de protocolo HTTP. |
| `http.user_agent` | Cadeia de caracteres | Valor do cabeçalho de [agente de usuário http](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>Spans JDBC

| Atributo  | Type | Descrição  |
|---|---|---|
| `db.system` | Cadeia de caracteres | Identificador do produto do sistema de gerenciamento de banco de dados (DBMS) que está sendo usado. |
| `db.connection_string` | Cadeia de caracteres | A cadeia de conexão usada para se conectar ao banco de dados. É recomendável remover as credenciais inseridas.|
| `db.user` | Cadeia de caracteres | Nome de usuário para acessar o banco de dados. |
| `db.name` | Cadeia de caracteres | Cadeia de caracteres usada para relatar o nome do banco de dados que está sendo acessado. Para comandos que alternam o banco de dados, essa cadeia de caracteres deve ser definida para o banco de dados de destino, mesmo que o comando falhe.|
| `db.statement` | Cadeia de caracteres | A instrução Database que está sendo executada.|
