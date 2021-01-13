---
title: Processadores de telemetria (visualização)-Azure Monitor Application Insights para Java
description: Como configurar processadores de telemetria no Azure Monitor Application Insights para Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133167"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processadores de telemetria (visualização)-Azure Monitor Application Insights para Java

> [!NOTE]
> Este recurso ainda está em versão prévia.

O agente do Java 3,0 para Application Insights agora tem os recursos para processar dados de telemetria antes de os dados serem exportados.

A seguir estão alguns casos de uso dos processadores de telemetria:
 * Mascarar dados confidenciais
 * Adicionar dimensões personalizadas condicionalmente
 * Atualizar o nome de telemetria usado para agregação e exibição
 * Descartar ou filtrar atributos de span para controlar o custo de ingestão

## <a name="terminology"></a>Terminologia

Antes de passarmos para processadores de telemetria, é importante entender o que são rastreamentos e spans.

### <a name="traces"></a>Rastreamentos

Os rastreamentos rastreiam a progressão de uma única solicitação, chamada a `trace` , conforme é manipulada pelos serviços que compõem um aplicativo. A solicitação pode ser iniciada por um usuário ou um aplicativo. Cada unidade de trabalho em um `trace` é chamada a `span` ; a `trace` é uma árvore de Spans. Um `trace` é composto pelo único span raiz e por qualquer número de Spans filho.

### <a name="span"></a>Compreende

Os spans são objetos que representam o trabalho que está sendo feito por serviços individuais ou componentes envolvidos em uma solicitação à medida que ele flui através de um sistema. Um `span` contém um `span context` , que é um conjunto de identificadores globalmente exclusivos que representam a solicitação exclusiva para a qual cada span faz parte. 

Abrange encapsulamento:

* O nome do span
* Uma imutável `SpanContext` que identifica exclusivamente a extensão
* Uma extensão pai na forma de um `Span` , `SpanContext` ou NULL
* Um `SpanKind`
* Um carimbo de data/hora inicial
* Um carimbo de data/hora final
* [`Attributes`](#attributes)
* Uma lista de eventos com carimbo de data/hora
* Um `Status`.

Em geral, o ciclo de vida de um Span é semelhante ao seguinte:

* Uma solicitação é recebida por um serviço. O contexto de span é extraído dos cabeçalhos de solicitação, se existir.
* Um novo Span é criado como um filho do contexto de span extraído; Se não houver nenhum, um novo Span raiz será criado.
* O serviço manipula a solicitação. Atributos e eventos adicionais são adicionados ao span que são úteis para entender o contexto da solicitação, como o nome do host do computador que manipula a solicitação ou os identificadores do cliente.
* Novas extensões podem ser criadas para representar o trabalho sendo feito por subcomponentes do serviço.
* Quando o serviço faz uma chamada remota para outro serviço, o contexto de span atual é serializado e encaminhado para o próximo serviço injetando o contexto de span no envelope de cabeçalhos ou de mensagem.
* O trabalho que está sendo feito pelo serviço é concluído, com êxito ou não. O status de span é definido adequadamente e o intervalo é marcado como concluído.

### <a name="attributes"></a>Atributos

`Attributes` Há uma lista de zero ou mais pares chave-valor encapsulados em um `span` . Um atributo deve ter as seguintes propriedades:

A chave de atributo, que deve ser uma cadeia de caracteres não nula e não vazia.
O valor do atributo, que é:
* Um tipo primitivo: String, Boolean, ponto flutuante de precisão dupla (IEEE 754-1985) ou um inteiro de bit de 64 assinado.
* Uma matriz de valores de tipo primitivo. A matriz deve ser homogênea, ou seja, não deve conter valores de tipos diferentes. Para protocolos que não dão suporte nativo a valores de matriz, esses valores devem ser representados como cadeias de caracteres JSON.

## <a name="supported-processors"></a>Processadores com suporte:
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

O processador de atributos e o processador span expõem a opção para fornecer um conjunto de propriedades de um intervalo para correspondência, para determinar se a extensão deve ser incluída ou excluída do processador de telemetria. Para configurar essa opção, em `include` e/ou `exclude` pelo menos um `matchType` e um `spanNames` ou `attributes` é necessário. A configuração de inclusão/exclusão tem suporte para ter mais de uma condição especificada. Todas as condições especificadas devem ser avaliadas como true para que uma correspondência ocorra. 

**Campo obrigatório**: 
* `matchType` controla como os itens `spanNames` nas `attributes` matrizes e são interpretados. Os possíveis valores são `regexp` ou `strict`. 

**Campos opcionais**: 
* `spanNames` deve corresponder a pelo menos um dos itens. 
* `attributes` Especifica a lista de atributos a serem correspondidos. Todos esses atributos devem corresponder exatamente para que ocorra uma correspondência.

> [!NOTE]
> Se ambos `include` e `exclude` forem especificados, as `include` Propriedades serão verificadas antes das `exclude` Propriedades.

#### <a name="sample-usage"></a>Exemplo de uso

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
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
```
Para obter mais noções básicas, confira a documentação [exemplos do processador de telemetria](./java-standalone-telemetry-processors-examples.md) .

## <a name="attribute-processor"></a>Processador de atributos 

O processador de atributos modifica os atributos de um Span. Opcionalmente, ele dá suporte à capacidade de incluir/excluir spans. Ele usa uma lista de ações que são executadas na ordem especificada no arquivo de configuração. As ações com suporte são:

### `insert`

Insere um novo atributo em spans onde a chave ainda não existe.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
Para a `insert` ação, os itens a seguir são necessários
  * `key`
  * um `value` ou `fromAttribute`
  * `action`:`insert`

### `update`

Atualiza um atributo em spans onde a chave existe

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
Para a `update` ação, os itens a seguir são necessários
  * `key`
  * um `value` ou `fromAttribute`
  * `action`:`update`


### `delete` 

Exclui um atributo de um span

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
Para a `delete` ação, os itens a seguir são necessários
  * `key`
  * `action`: `delete`

### `hash`

Hashes (SHA1) um valor de atributo existente

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
Para a `hash` ação, os itens a seguir são necessários
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Este recurso está apenas no 3.0.1 e posterior

Extrai valores usando uma regra de expressão regular da chave de entrada para chaves de destino especificadas na regra. Se uma chave de destino já existir, ela será substituída. Ele se comporta de forma semelhante à configuração do [processador span](#extract-attributes-from-span-name) `toAttributes` com o atributo existente como a origem.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
Para a `extract` ação, os itens a seguir são necessários
* `key`
* `pattern`
* `action` : `extract`

Para obter mais noções básicas, confira a documentação [exemplos do processador de telemetria](./java-standalone-telemetry-processors-examples.md) .

## <a name="span-processors"></a>Processadores de span

O processador span modifica o nome do SPAN ou os atributos de um Span com base no nome do span. Opcionalmente, ele dá suporte à capacidade de incluir/excluir spans.

### <a name="name-a-span"></a>Nomear um intervalo

A configuração a seguir é necessária como parte da seção de nome:

* `fromAttributes`: O valor do atributo para as chaves é usado para criar um novo nome na ordem especificada na configuração. Todas as chaves de atributo precisam ser especificadas no intervalo para o processador renomeá-lo.

A configuração a seguir pode ser configurada opcionalmente:

* `separator`: Uma cadeia de caracteres especificada será usada para dividir valores
> [!NOTE]
> Se a renomeação for dependente dos atributos que estão sendo modificados pelo processador de atributos, verifique se o processador de span está especificado após o processador de atributos na especificação do pipeline.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>Extrair atributos do nome do span

Usa uma lista de expressões regulares para corresponder ao nome do span e extrair atributos dele com base em subexpressões. Deve ser especificado na `toAttributes` seção.

As seguintes configurações são necessárias:

`rules` : Uma lista de regras para extrair valores de atributo do nome de extensão. Os valores no nome do span são substituídos por nomes de atributo extraídos. Cada regra na lista é a cadeia de caracteres de padrão Regex. O nome do span é verificado em relação ao Regex. Se o Regex corresponder, todas as subexpressões nomeadas do Regex serão extraídas como atributos e adicionadas à extensão. Cada nome de subexpressão se torna um nome de atributo e a subexpressão correspondente de parte se torna o valor do atributo. A parte correspondente no nome do span é substituída pelo nome do atributo extraído. Se os atributos já existirem no span, eles serão substituídos. O processo é repetido para todas as regras na ordem em que são especificadas. Cada regra subsequente funciona no nome do span que é a saída após o processamento da regra anterior.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>Lista de atributos

A seguir, há uma lista de alguns atributos de span comuns que podem ser usados nos processadores de telemetria.

### <a name="http-spans"></a>Spans HTTP

| Atributo  | Type | Descrição | 
|---|---|---|
| `http.method` | string | Método de solicitação HTTP.|
| `http.url` | string | URL de solicitação HTTP completa no formulário `scheme://host[:port]/path?query[#fragment]` . Normalmente, o fragmento não é transmitido por HTTP, mas, se for conhecido, ele deve ser incluído no entanto.|
| `http.status_code` | número | [Código de status de resposta http](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Tipo de protocolo HTTP usado |
| `http.user_agent` | string | Valor do cabeçalho de [agente de usuário http](https://tools.ietf.org/html/rfc7231#section-5.5.3) enviado pelo cliente. |

### <a name="jdbc-spans"></a>Spans JDBC

| Atributo  | Type | Descrição  |
|---|---|---|
| `db.system` | string | Um identificador para o produto do sistema de gerenciamento de banco de dados (DBMS) que está sendo usado. |
| `db.connection_string` | string | A cadeia de conexão usada para se conectar ao banco de dados. É recomendável remover as credenciais inseridas.|
| `db.user` | string | Nome de usuário para acessar o banco de dados. |
| `db.name` | string | Esse atributo é usado para relatar o nome do banco de dados que está sendo acessado. Para comandos que alternam o banco de dados, isso deve ser definido como o banco de dados de destino (mesmo que o comando falhe).|
| `db.statement` | string | A instrução do banco de dados que está sendo executada.|