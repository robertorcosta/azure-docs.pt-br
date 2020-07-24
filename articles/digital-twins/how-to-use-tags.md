---
title: Adicionar marcas ao gêmeos digital
titleSuffix: Azure Digital Twins
description: Veja como implementar marcas no digital gêmeos
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 59f68909e2f3704fea5c38e3f1535f5996b284ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096023"
---
# <a name="add-tags-to-digital-twins"></a>Adicionar marcas ao gêmeos digital 

Você pode usar o conceito de marcas para identificar e categorizar ainda mais seu gêmeos digital. Em particular, os usuários podem querer replicar marcas de sistemas existentes, como [marcas de palheiro](https://project-haystack.org/doc/TagModel), em suas instâncias de gêmeos digitais do Azure. 

Este documento descreve os padrões que podem ser usados para implementar marcas no digital gêmeos.

As marcas são adicionadas pela primeira vez como propriedades dentro do [modelo](concepts-models.md) que descrevem uma teledigital. Essa propriedade é, então, definida no conjunto de entrelaçamento quando ela é criada com base no modelo. Depois disso, as marcas podem ser usadas em [consultas](concepts-query-language.md) para identificar e filtrar seu gêmeos.

## <a name="marker-tags"></a>Marcas de marcador 

Uma **marca de marcador** é uma cadeia de caracteres simples que é usada para marcar ou categorizar um texto digital, como "azul" ou "vermelho". Essa cadeia de caracteres é o nome da marca e as marcas de marcador não têm nenhum valor significativo — a marca é significativa apenas por sua presença (ou ausência). 

### <a name="add-marker-tags-to-model"></a>Adicionar marcas de marcador ao modelo 

Marcas de marcador são modeladas como um mapa de [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` para `boolean` . O booliano `mapValue` é ignorado, já que a presença da marca é tudo importante. 

Aqui está um trecho de um modelo de entrelaçamento que implementa uma marca de marcador como uma propriedade:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "boolean"
    }
  }
}
```

### <a name="add-marker-tags-to-digital-twins"></a>Adicionar marcas de marcador ao gêmeos digital

Depois que a `tags` propriedade faz parte de um modelo de teledigital, você pode definir a marca de marcador na folha de valores digitais, definindo o valor dessa propriedade. 

Aqui está um exemplo que popula o marcador `tags` para três gêmeos:

```csharp
entity-01: "tags": { "red": true, "round": true } 
entity-02: "tags": { "blue": true, "round": true } 
entity-03: "tags": { "red": true, "large": true } 
```

### <a name="query-with-marker-tags"></a>Consulta com marcas de marcador

Depois que as marcas forem adicionadas ao gêmeos digital, as marcas poderão ser usadas para filtrar o gêmeos em consultas. 

Aqui está uma consulta para obter todos os gêmeos que foram marcados como "vermelho": 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Você também pode combinar marcas para consultas mais complexas. Aqui está uma consulta para obter todos os gêmeos que estão redondos e não vermelho: 

```sql
select * from digitaltwins where not is_defined(tags.red) and is_defined(tags.round) 
```

## <a name="value-tags"></a>Marcas de valor 

Uma **marca de valor** é um par chave-valor que é usado para fornecer a cada marca um valor, como `"color": "blue"` ou `"color": "red"` . Depois que uma marca de valor é criada, ela também pode ser usada como uma marca de marcador, ignorando o valor da marca. 

### <a name="add-value-tags-to-model"></a>Adicionar marcas de valor ao modelo 

Marcas de valor são modeladas como um mapa de [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` para `string` . Tanto o `mapKey` quanto o `mapValue` são significativos. 

Aqui está um trecho de um modelo de entrelaçamento que implementa uma marca de valor como uma propriedade:

```json
{
  "@type": "Property",
  "name": "tags",
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "tagName",
      "schema": "string"
    },
    "mapValue": {
      "name": "tagValue",
      "schema": "string"
    }
  }
} 
```

### <a name="add-value-tags-to-digital-twins"></a>Adicionar marcas de valor a gêmeos digital

Assim como nas marcas de marcador, você pode definir a marca de valor em um FileUp digital definindo o valor dessa `tags` Propriedade do modelo. Para usar uma marca de valor como marca de marcador, você pode definir o `tagValue` campo como o valor da cadeia de caracteres vazia ( `""` ). 

Aqui está um exemplo que popula o valor `tags` para três gêmeos:

```csharp
entity-01: "tags": { "red": "", "size": "large" } 
entity-02: "tags": { "purple": "", "size": "small" } 
entity-03: "tags": { "red": "", "size": "small" } 
```

Observe que `red` e `purple` são usados como marcas de marcador neste exemplo.

### <a name="query-with-value-tags"></a>Consulta com marcas de valor

Assim como nas marcas de marcador, você pode usar marcas de valor para filtrar o gêmeos em consultas. Você também pode usar marcas de valor e marcas de marcador juntos.

No exemplo acima, `red` está sendo usado como uma marca de marcador. Aqui está uma consulta para obter todos os gêmeos que foram marcados como "vermelho": 

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Aqui está uma consulta para obter todas as entidades que são pequenas (marca de valor) e não vermelho: 

```sql
select * from digitaltwins where not is_defined(tags.red) and tags.size = 'small' 
```

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como projetar e gerenciar modelos de entrelaçamento digital:
* [*Instruções: Gerenciar modelos personalizados*](how-to-manage-model.md)

Leia mais sobre como consultar o grafo de entrelaçamento:
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)