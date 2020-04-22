---
title: Referência de linguagem SQL de aceleração de consulta (visualização)
titleSuffix: Azure Storage
description: Aprenda a usar a sintaxe sql de aceleração de consulta.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772113"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Referência de linguagem SQL de aceleração de consulta (visualização)

A aceleração de consulta suporta uma linguagem ansi sql para expressar consultas sobre o conteúdo blob.  O dialeto SQL de aceleração de consulta é um subconjunto do ANSI SQL, com um conjunto limitado de tipos de dados suportados, operadores, etc., mas também se expande no ANSI SQL para suportar consultas sobre formatos hierárquicos de dados semiestruturados, como json. 

> [!NOTE]
> O recurso de aceleração de consultas está em visualização pública e está disponível nas regiões Central e Central do Canadá. Para rever limitações, consulte o artigo [Questões Conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Selecione sintaxe

A única declaração SQL suportada pela aceleração da consulta é a declaração SELECT. Este exemplo retorna todas as linhas para as quais a expressão retorna verdadeira.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Para dados formatados por CSV, *a tabela* deve ser `BlobStorage`.  Isso significa que a consulta será executada contra qualquer bolha que tenha sido especificada na chamada REST.
Para dados formatados por JSON, *a tabela* é um "descritor de tabela".   Consulte a seção [Descritores](#table-descriptors) de tabela deste artigo.

No exemplo a seguir, para cada linha para a qual a *expressão* WHERE retorna verdadeira, esta declaração retornará uma nova linha que é feita a partir da avaliação de cada uma das expressões de projeção.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

O exemplo a seguir retorna uma computação agregada (Por exemplo: o valor médio de uma determinada coluna) sobre cada uma das linhas para as quais a *expressão* retorna verdadeira. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

O exemplo a seguir retorna deslocamentos adequados para dividir uma bolha formatado pelo CSV.  Consulte a seção [Sys.Split](#sys-split) deste artigo.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Tipos de dados

|Tipo de Dados|Descrição|
|---------|-------------------------------------------|
|INT      |Inteiro com sinal de 64 bits.                     |
|FLOAT    |Ponto flutuante de 64 bits ("dupla precisão").|
|STRING   |Seqüência Unicode de comprimento variável.            |
|timestamp|Um ponto no tempo.                           |
|BOOLEAN  |Verdadeiro ou falso.                             |

Ao ler valores de dados formatados pelo CSV, todos os valores são lidos como strings.  Os valores das cordas podem ser convertidos para outros tipos usando expressões CAST.  Os valores podem ser implicitamente lançados para outros tipos, dependendo do contexto. para obter mais informações, consulte [precedência do tipo de dados (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Expressões

### <a name="referencing-fields"></a>Campos de referência

Para dados formatados por JSON ou dados formatados por CSV com uma linha de cabeçalho, os campos podem ser referenciados por nome.  Os nomes de campo podem ser citados ou não citados. Os nomes de campo citados são incluídos em caracteres de citação dupla ("), podem conter espaços e são sensíveis a maiúsculas e minúsculas.  Nomes de campo não citados são insensíveis a casos e podem não conter caracteres especiais.

Em dados formatados por CSV, os campos também podem ser referenciados por ordinal, prefixado com um caractere sublinhado (_).  Por exemplo, o primeiro campo pode ser referenciado como _1, ou o décimo primeiro campo pode ser referenciado como _11.  A referência de campos por ordinal é útil para dados formatados por CSV que não contenham uma linha de cabeçalho, nesse caso a única maneira de referenciar um campo específico é por ordinal.

### <a name="operators"></a>Operadores

Os seguintes operadores Padrão SQL são suportados:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Se os tipos de dados à esquerda e à direita de um operador forem diferentes, a conversão automática será realizada de acordo com as regras especificadas aqui: [Precedência do tipo de dados (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

A linguagem SQL de aceleração de consulta suporta apenas um subconjunto muito pequeno dos tipos de dados discutidos nesse artigo.  Consulte a seção Tipos de [dados](#data-types) deste artigo.

### <a name="casts"></a>Conversões

A linguagem SQL de aceleração de consulta suporta o operador CAST, de acordo com as regras aqui: [Conversão de tipo de dados (Database Engine)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

A linguagem SQL de aceleração de consulta suporta apenas um pequeno subconjunto dos tipos de dados discutidos nesse artigo.  Consulte a seção Tipos de [dados](#data-types) deste artigo.

### <a name="string-functions"></a>Funções de cadeia de caracteres

O idioma SQL de aceleração de consulta suporta as seguintes funções padrão de string SQL:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Aqui estão alguns exemplos:

|Função|Exemplo|Result|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

A função [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ajuda você a procurar um padrão. Aqui estão alguns exemplos que [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) usam a função ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``LIKE para pesquisar a seqüência de dados .

|Consulta|Exemplo|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Funções de data

As seguintes funções padrão de data SQL são suportadas:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Atualmente convertemos todos os [formatos de data do IS08601 padrão](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>função DATE_ADD

A linguagem SQL de aceleração de consulta suporta ano, mês, dia, hora, minuto, segundo para a ``DATE_ADD`` função.

Exemplos:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>função DATE_DIFF

A linguagem SQL de aceleração de consulta suporta ano, mês, dia, hora, minuto, segundo para a ``DATE_DIFF`` função.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>Função EXTRACT

Para EXTRACT, além da parte ``DATE_ADD`` de data suportada para a função, a linguagem SQL de aceleração de consulta suporta timezone_hour e timezone_minute como parte da data.

Exemplos:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING função

Exemplos:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Esta tabela descreve strings que você pode usar ``TO_STRING`` para especificar o formato de saída da função.

|Cadeia de formato    |Saída                               |
|-----------------|-------------------------------------|
|yy               |Ano em formato de 2 dígitos – 1999 como '99'|
|y                |Ano em formato de 4 dígitos               |
|yyyy             |Ano em formato de 4 dígitos               |
|M                |Mês do ano – 1                    |
|MM               |Mês acolchoado Zero – 01               |
|MMM              |Abbr. mês do Ano -JAN            |
|MMMM             |Mês completo – Maio                      |
|d                |Dia do mês (1-31)                  |
|dd               |Zero dia acolchoado do mês (01-31)     |
|a                |AM ou PM                             |
|h                |Hora do dia (1-12)                   |
|hh               |Zero acolchoado Horas od dia (01-12)     |
|H                |Hora do dia (0-23)                   |
|HH               |Zero Hora acolchoada do Dia (00-23)      |
|m                |Minuto da hora (0-59)                |
|MM               |Zero minuto acolchoado (00-59)           |
|s                |Segundo de Minutos (0-59)             |
|ss               |Zero acolchoado Segundos (00-59)          |
|S                |Fração de Segundos (0.1-0.9)        |
|SS               |Fração de Segundos (0.01-0.99)      |
|Sss              |Fração de Segundos (0.001-0.999)    |
|X                |Deslocamento em horas                      |
|XX ou XXXX       |Deslocamento em horas e minutos (+0430)  |
|XXX ou XXXXX     |Deslocamento em horas e minutos (-07:00) |
|x                |Deslocamento em horas (7)                  |
|xx ou xxxx       |Deslocamento em hora e minuto (+0530)    |
|Xxx ou xxxxx     |Deslocamento em hora e minuto (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP função

Apenas os formatos IS08601 são suportados.

Exemplos:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Você também pode ``UTCNOW`` usar a função para obter o tempo do sistema.


## <a name="aggregate-expressions"></a>Expressões agregadas

Uma declaração SELECT pode conter uma ou mais expressões de projeção ou uma única expressão agregada.  As seguintes expressões agregadas são suportadas:

|Expression|Descrição|
|--|--|
|[\*Conde](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Retorna o número de registros que correspondem à expressão predicada.|
|[CONTAGEM (expressão)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Retorna o número de registros para os quais a expressão não é nula.|
|[MÉDIA (expressão)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Retorna a média dos valores não nulos de expressão.|
|[MIN (expressão)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Retorna o valor mínimo não nulo de expressão.|
|[MAX (expressão)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |Retorna o valor máximo não nulo de expressão.|
|[SOMA (expressão)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Retorna a soma de todos os valores não nulos de expressão.|

### <a name="missing"></a>Faltando

O ``IS MISSING`` operador é o único não-padrão que a linguagem SQL de aceleração de consulta suporta.  Para dados JSON, se um campo estiver faltando ``IS MISSING`` em um registro de entrada específico, o campo de expressão avaliará para o valor booleano verdadeiro.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Descritores de mesa

Para dados CSV, o `BlobStorage`nome da tabela é sempre .  Por exemplo:

```sql
SELECT * FROM BlobStorage
```

Para dados JSON, opções adicionais estão disponíveis:

```sql
SELECT * FROM BlobStorage[*].path
```

Isso permite consultas sobre subconjuntos dos dados JSON.

Para consultas JSON, você pode mencionar o caminho em parte da cláusula FROM. Esses caminhos ajudarão a analisar o subconjunto de dados JSON. Esses caminhos podem fazer referência aos valores json array e object.

Vamos dar um exemplo para entender isso com mais detalhes.

Estes são nossos dados amostrais:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Você pode estar interessado `warehouses` apenas no objeto JSON a partir dos dados acima. O `warehouses` objeto é um tipo de matriz JSON, então você pode mencionar isso na cláusula FROM. Sua consulta de amostra pode ser algo assim.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

A consulta obtém todos os campos, mas seleciona apenas a latitude.

Se você quiser acessar `dimensions` apenas o valor do objeto JSON, você poderia usar se referir a esse objeto em sua consulta. Por exemplo:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Isso também limita seu acesso `dimensions` aos membros do objeto. Se você quiser acessar outros membros dos campos JSON e valores internos de objetos JSON, então você pode usar uma consulta como mostrado no exemplo a seguir:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage[\*] ambos referem-se a todo o objeto. No entanto, se você tiver um caminho na cláusula FROM,\*então você precisará usar BlobStorage[].path

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split

Esta é uma forma especial da declaração SELECT, que está disponível apenas para dados formatados pelo CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Use esta declaração nos casos em que você deseja baixar e, em seguida, processar registros de dados CSV em lotes. Dessa forma, você pode processar registros em paralelo em vez de ter que baixar todos os registros ao mesmo tempo. Esta declaração não retorna registros do arquivo CSV. Em vez disso, ele retorna uma coleção de tamanhos de lote. Em seguida, você pode usar cada tamanho de lote para recuperar um lote de registros de dados. 

Use o parâmetro *split_size* para especificar o número de bytes que deseja que cada lote contenha. Por exemplo, se você quiser processar apenas 10 MB de dados por vez, sua declaração seria assim: `SELECT sys.split(10485760)FROM BlobStorage` porque 10 MB é igual a 10.485.760 bytes. Cada lote conterá quantos registros puderem encaixar nesses 10 MB. 

Na maioria dos casos, o tamanho de cada lote será ligeiramente maior do que o número especificado. Isso porque um lote não pode conter um registro parcial. Se o último registro em um lote começar antes do fim do seu limite, o lote será maior para que possa conter o registro completo. O tamanho do último lote provavelmente será menor do que o tamanho que você especificar.

>[!NOTE]
> O split_size deve ser de pelo menos 10 MB (10485760).

## <a name="see-also"></a>Confira também

- [Aceleração da consulta de armazenamento do Lago de Dados Do Azure (visualização)](data-lake-storage-query-acceleration.md)
- [Filtrar dados usando a aceleração da consulta de armazenamento do Lago de Dados do Azure (visualização)](data-lake-storage-query-acceleration-how-to.md)

