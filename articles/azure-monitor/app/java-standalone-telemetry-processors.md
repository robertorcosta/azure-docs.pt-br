---
title: Processadores de telemetria (visualização)-Azure Monitor Application Insights Java
description: Processadores de telemetria para Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e3c41a7a9968a7de743f0c513b1f2b194501d0df
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425789"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Processadores de telemetria (versão prévia) para Azure Monitor Application Insights Java

> [!NOTE]
> Este recurso ainda está em versão prévia.

O agente do Java 3,0 para Application Insights agora tem os recursos para processar dados de telemetria antes de os dados serem exportados.

### <a name="some-use-cases"></a>Alguns casos de uso:
 * Mascarar dados confidenciais
 * Adicionar dimensões personalizadas condicionalmente
 * Atualizar o nome de telemetria usado para agregação e exibição

### <a name="supported-processors"></a>Processadores com suporte:
 * Processador de atributos
 * Processador span

## <a name="to-get-started"></a>Para começar

Crie um arquivo de configuração chamado `applicationinsights.json` e coloque-o no mesmo diretório do `applicationinsights-agent-***.jar` , com o modelo a seguir.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Incluir/excluir spans

O processador de atributo e o processador span expõem a opção para fornecer um conjunto de propriedades de um span para correspondência, para determinar se a extensão deve ser incluída ou excluída do processador. Para configurar essa opção, em `include` e/ou `exclude` pelo menos um `matchType` e um `spanNames` ou `attributes` é necessário. A configuração de inclusão/exclusão tem suporte para ter mais de uma condição especificada. Todas as condições especificadas devem ser avaliadas como true para que uma correspondência ocorra. 

**Campo obrigatório** : 
* `matchType` controla como os itens `spanNames` nas `attributes` matrizes e são interpretados. Os possíveis valores são `regexp` ou `strict`. 

**Campos opcionais** : 
* `spanNames` deve corresponder a pelo menos um dos itens. 
* `attributes` Especifica a lista de atributos a serem correspondidos. Todos esses atributos devem corresponder exatamente para que ocorra uma correspondência.

> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` Propriedades serão verificadas antes das `exclude` Propriedades.

#### <a name="sample-usage"></a>Amostra de uso

O seguinte demonstra como especificar o conjunto de propriedades de span para indicar a qual extensão esse processador deve ser aplicado. As `include` Propriedades de indicam quais devem ser incluídas e as `exclude` Propriedades filtram ainda mais os intervalos que não devem ser processados.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

Com a configuração acima, os seguintes intervalos correspondem às propriedades e as ações do processador são aplicadas:

* Nome do Span1: atributos ' svcB ': {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Nome do Span2: atributos ' svcA ': {env: preparo, test_request: false, redact_trace: true}

As seguintes extensões não correspondem às propriedades include e as ações do processador não são aplicadas:

* Nome do Span3: atributos ' svcB ': {env: Production, test_request: true, credit_card: 1234, redact_trace: false}

* Nome do Span4: atributos ' svcC ': {env: dev, test_request: false}

## <a name="attribute-processor"></a>Processador de atributos 

O processador de atributos modifica os atributos de um Span. Opcionalmente, ele dá suporte à capacidade de incluir/excluir spans.
Ele usa uma lista de ações que são executadas na ordem especificada no arquivo de configuração. As ações com suporte são:

* `insert` : Insere um novo atributo em spans onde a chave ainda não existe
* `update` : Atualiza um atributo em spans onde a chave existe
* `delete` : Exclui um atributo de um span
* `hash`   : Hashes (SHA1) um valor de atributo existente

Para as ações `insert` e `update`
* `key` é necessário
* um `value` ou `fromAttribute` é obrigatório
* `action` é obrigatório.

Para a `delete` ação,
* `key` é necessário
* `action`: `delete` é necessário.

Para a `hash` ação,
* `key` é necessário
* `action` : `hash` é necessário.

A lista de ações pode ser composta para criar cenários avançados, como o recheio de atributos, a cópia de valores em uma nova chave, a edição de informações confidenciais.

#### <a name="sample-usage"></a>Amostra de uso

O exemplo a seguir demonstra a inserção de chaves/valores em spans:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

O exemplo a seguir demonstra como configurar o processador para atualizar somente as chaves existentes em um atributo:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

O exemplo a seguir demonstra como processar intervalos que têm um nome de extensão que corresponde aos padrões de RegExp.
Esse processador removerá o atributo "token" e ofuscará o atributo "password" em Spans, onde o nome do span corresponde a "auth \* ". e onde o nome do span não corresponde a "login. \* ".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Processadores de span

O processador span modifica o nome do SPAN ou os atributos de um Span com base no nome do span. Opcionalmente, ele dá suporte à capacidade de incluir/excluir spans.

### <a name="name-a-span"></a>Nomear um intervalo

A configuração a seguir é necessária como parte da seção de nome:

* `fromAttributes`: O valor do atributo para as chaves é usado para criar um novo nome na ordem especificada na configuração. Todas as chaves de atributo precisam ser especificadas no intervalo para o processador renomeá-lo.

A configuração a seguir pode ser configurada opcionalmente:

* `separator`: Uma cadeia de caracteres especificada será usada para dividir valores
> [!NOTE]
> Se a renomeação for dependente dos atributos que estão sendo modificados pelo processador de atributos, verifique se o processador de span está especificado após o processador de atributos na especificação do pipeline.

#### <a name="sample-usage"></a>Amostra de uso

O exemplo a seguir especifica os valores do atributo "DB. svc", "Operation" e "ID" formará o novo nome do span, nessa ordem, separados pelo valor "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Extrair atributos do nome do span

Usa uma lista de expressões regulares para corresponder ao nome do span e extrair atributos dele com base em subexpressões. Deve ser especificado na `toAttributes` seção.

As seguintes configurações são necessárias:

`rules` : Uma lista de regras para extrair valores de atributo do nome de extensão. Os valores no nome do span são substituídos por nomes de atributo extraídos. Cada regra na lista é a cadeia de caracteres de padrão Regex. O nome do span é verificado em relação ao Regex. Se o Regex corresponder, todas as subexpressões nomeadas do Regex serão extraídas como atributos e adicionadas à extensão. Cada nome de subexpressão se torna um nome de atributo e a subexpressão correspondente de parte se torna o valor do atributo. A parte correspondente no nome do span é substituída pelo nome do atributo extraído. Se os atributos já existirem no span, eles serão substituídos. O processo é repetido para todas as regras na ordem em que são especificadas. Cada regra subsequente funciona no nome do span que é a saída após o processamento da regra anterior.

#### <a name="sample-usage"></a>Amostra de uso

Vamos supor que o nome da extensão de entrada seja/API/v1/Document/12345678/Update. Aplicar os seguintes resultados no nome do span de saída/api/v1/document/{documentId}/update adicionará um novo atributo "DocumentID" = "12345678" à extensão.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

O a seguir demonstra como renomear o nome do span como "{operation_website}" e adicionar o atributo {Key: operation_website, value: oldSpanName} quando o span tem as seguintes propriedades:
- O nome de extensão contém '/' em qualquer lugar na cadeia de caracteres.
- O nome do span não é ' não cercamento/Change '.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```