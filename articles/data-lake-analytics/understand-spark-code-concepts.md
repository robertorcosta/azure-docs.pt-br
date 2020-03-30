---
title: Entenda os conceitos de código Apache Spark para desenvolvedores Azure Data Lake Analytics U-SQL.
description: Este artigo descreve os conceitos do Apache Spark para ajudar os desenvolvedores do U-SQL a entender os conceitos de código Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424010"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Entenda o código Apache Spark para desenvolvedores U-SQL

Esta seção fornece orientação de alto nível sobre a transformação de scripts U-SQL para Apache Spark.

- Começa com uma [comparação dos paradigmas de processamento das duas línguas](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Dá dicas de como:
   - [Transformar scripts](#transform-u-sql-scripts) incluindo [as expressões](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) de conjunto de linhas do U-SQL
   - [Código do .NET](#transform-net-code)
   - [Tipos de dados](#transform-typed-values)
   - [Objetos de catálogo](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Entenda os paradigmas de linguagem e processamento u-sql e spark

Antes de começar a migrar os scripts U-SQL do Azure Data Lake Analytics para o Spark, é útil entender a linguagem geral e as filosofias de processamento dos dois sistemas.

U-SQL é uma linguagem de consulta declarativa semelhante ao SQL que usa um paradigma de fluxo de dados e permite incorporar e dimensionar facilmente o código de usuário escrito em .NET (por exemplo, C#), Python e R. As extensões do usuário podem implementar expressões simples ou funções definidas pelo usuário, mas também podem fornecer ao usuário a capacidade de implementar os chamados operadores definidos pelo usuário que implementam operadores personalizados para executar transformações de nível de linha, extrações e saída de escrita.

Spark é uma estrutura de escala que oferece várias vinculações de idiomas em Scala, Java, Python, .NET etc. onde você escreve principalmente seu código em uma dessas línguas, cria abstrações de dados chamadas conjuntos de dados distribuídos resilientes (RDD), dataframes e conjuntos de dados e em seguida, use uma linguagem específica de domínio semelhante a LINQ (DSL) para transformá-las. Ele também fornece SparkSQL como uma sublinguagem declarativa sobre as abstrações do dataframe e do conjunto de dados. O DSL oferece duas categorias de operações, transformações e ações. A aplicação de transformações nas abstrações de dados não executará a transformação, mas sim o acúmulo do plano de execução que será submetido à avaliação com uma ação (por exemplo, escrever o resultado em uma tabela ou arquivo temporário, ou imprimir o resultado).

Assim, ao traduzir um script U-SQL para um programa Spark, você terá que decidir qual idioma você deseja usar para, pelo menos, gerar a abstração do quadro de dados (que atualmente é a abstração de dados mais usada) e se você deseja escrever o declarativo transformações de fluxo de dados usando o DSL ou sparkSQL. Em alguns casos mais complexos, você pode precisar dividir seu script U-SQL em uma seqüência de Spark e outras etapas implementadas com Funções Azure Batch ou Azure.

Além disso, o Azure Data Lake Analytics oferece U-SQL em um ambiente de serviço de trabalho sem servidor, enquanto tanto o Azure Databricks quanto o Azure HDInsight oferecem o Spark na forma de um serviço de cluster. Ao transformar sua aplicação, você terá que levar em conta as implicações de agora criar, dimensionar, dimensionar e desativar os clusters.

## <a name="transform-u-sql-scripts"></a>Transformar scripts U-SQL

Os scripts U-SQL seguem o seguinte padrão de processamento:

1. Os dados são lidos a partir `EXTRACT` de arquivos não estruturados, usando a instrução, uma especificação de local ou conjunto de arquivos, e o extrator integrado ou definido pelo usuário e o esquema desejado, ou a partir de tabelas U-SQL (tabelas gerenciadas ou externas). Ele é representado como um conjunto de linhas.
2. Os conjuntos de linhas são transformados em várias instruções U-SQL que aplicam expressões U-SQL aos conjuntos de linhas e produzem novos conjuntos de linhas.
3. Finalmente, os conjuntos de linhas resultantes `OUTPUT` são despontados em arquivos usando a declaração que especifica os locais e um desaver incorporado ou definido pelo usuário, ou em uma tabela U-SQL.

O script é avaliado preguiçosamente, o que significa que cada etapa de extração e transformação é composta em uma árvore de expressão e avaliada globalmente (o fluxo de dados).

Os programas spark são semelhantes, na medida em que você usaria conectores Spark para ler os dados e criar os dataframes, em seguida, aplicar as transformações nos dataframes usando o DSL ou SparkSQL semelhante ao LINQ e, em seguida, escrever o resultado em arquivos, tabelas de faísca temporárias, alguns tipos de linguagem de programação, ou o console.

## <a name="transform-net-code"></a>Transformar o código .NET

A linguagem de expressão do U-SQL é C# e oferece uma variedade de maneiras de dimensionar o código .NET personalizado.

Como o Spark atualmente não suporta nativamente a execução do código .NET, você terá que reescrever suas expressões em uma expressão equivalente de Faísca, Scala, Java ou Python ou encontrar uma maneira de chamar em seu código .NET. Se o seu script usar bibliotecas .NET, você terá as seguintes opções:

- Traduza seu código .NET em Scala ou Python.
- Divida seu script U-SQL em várias etapas, onde você usa os processos do Azure Batch para aplicar as transformações .NET (se você conseguir uma escala aceitável)
- Use uma vinculação de idioma .NET disponível no Código Aberto chamada Moebius. Este projeto não está em um estado de apoio.

De qualquer forma, se você tiver uma grande quantidade de lógica .NET em seus scripts U-SQL, entre em contato conosco através de seu representante da Conta Microsoft para obter mais orientações.

Os detalhes a seguir são para os diferentes casos de usos .NET e C# em scripts U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transformar expressões u-sql c# em linha

A linguagem de expressão do U-SQL é C#. Muitas das expressões U-SQL inline escalares são implementadas nativamente para melhorar o desempenho, enquanto expressões mais complexas podem ser executadas através da chamada para o framework .NET.

O Spark tem sua própria linguagem de expressão escalar (seja como parte do DSL ou no SparkSQL) e permite chamar para funções definidas pelo usuário escritas em sua linguagem de hospedagem.

Se você tiver expressões escalares no U-SQL, primeiro você deve encontrar a expressão escalar spark mais apropriada para obter o maior desempenho e, em seguida, mapear as outras expressões em uma função definida pelo usuário da linguagem de hospedagem Spark de sua escolha.

Esteja ciente de que .NET e C# têm semântica de tipo diferente das linguagens de hospedagem Spark e DSL da Spark. Veja [abaixo](#transform-typed-values) para obter mais detalhes sobre as diferenças do sistema de tipo.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transforme funções escalares .NET definidas pelo usuário e agregadores definidos pelo usuário

O U-SQL fornece maneiras de chamar funções escalares .NET arbitrárias e chamar agregadores definidos pelo usuário gravados em .NET.

A Spark também oferece suporte para funções definidas pelo usuário e agregadores definidos pelo usuário escritos na maioria de seus idiomas de hospedagem que podem ser chamados do DSL da Spark e do SparkSQL.

### <a name="transform-user-defined-operators-udos"></a>Transformar operadores definidos pelo usuário (UDOs)

O U-SQL fornece várias categorias de operadores definidos pelo usuário (UDOs) como extratores, saídas, redutores, processadores, aplicadores e combinadores que podem ser escritos em .NET (e - até certo ponto - em Python e R).

A Spark não oferece o mesmo modelo de extensibilidade para operadores, mas tem capacidades equivalentes para alguns.

O equivalente a Faísca para extratores e saídas são conectores Spark. Para muitos extratores U-SQL, você pode encontrar um conector equivalente na comunidade Spark. Para outros, você terá que escrever um conector personalizado. Se o extrator U-SQL for complexo e fizer uso de várias bibliotecas .NET, pode ser preferível construir um conector em Scala que use interop para chamar a biblioteca .NET que faz o processamento real dos dados. Nesse caso, você terá que implantar o tempo de execução do .NET Core no cluster Spark e certificar-se de que as bibliotecas .NET referenciadas são compatíveis com o .NET Standard 2.0.

Os outros tipos de U-SQL UDOs precisarão ser reescritos usando funções e agregadores definidos pelo usuário e a expressão Semanicamente apropriada Spark DLS ou SparkSQL. Por exemplo, um processador pode ser mapeado para um SELECT de uma variedade de invocações udf, empacotado como uma função que toma um dataframe como argumento e retorna um dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Transforme as bibliotecas opcionais do U-SQL

O U-SQL fornece um conjunto de bibliotecas opcionais e demo que oferecem [python,](data-lake-analytics-u-sql-python-extensions.md) [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, suporte a AVRO](https://github.com/Azure/usql/tree/master/Examples/DataFormats)e [alguns recursos de serviços cognitivos.](data-lake-analytics-u-sql-cognitive.md)

A Spark oferece sua própria integração Python e R, pySpark e SparkR, respectivamente, e fornece conectores para ler e escrever JSON, XML e AVRO.

Se você precisar transformar um script fazendo referência às bibliotecas de serviços cognitivos, recomendamos entrar em contato conosco através do seu representante da Conta Microsoft.

## <a name="transform-typed-values"></a>Transformar valores digitados

Como o sistema de tipo do U-SQL é baseado no sistema do tipo .NET e o Spark tem seu próprio sistema de tipo, que é impactado pela vinculação do idioma host, você terá que se certificar de que os tipos em que você está operando estão próximos e, para certos tipos, as faixas de tipo, precisão e/ou escala podem ser ligeiramente diferentes. Além disso, U-SQL `null` e Spark tratam valores de forma diferente.

### <a name="data-types"></a>Tipos de dados

A tabela a seguir fornece os tipos equivalentes em Spark, Scala e PySpark para os tipos U-SQL dado.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Para obter mais informações, consulte:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark Tipos de SQL e DataFrames](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Tipos de valor scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Tratamento de NULL

No Spark, os tipos por padrão permitem valores NULAenquanto no U-SQL, você marca explicitamente escalador, não-objeto como nulo. Embora o Spark permita que você defina uma coluna como não anulada, ela não imporá a restrição e [poderá levar a um resultado errado](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

Em Spark, NULL indica que o valor é desconhecido. Um valor SPARK NULL é diferente de qualquer valor, incluindo ele mesmo. Comparações entre dois valores NULL spark, ou entre um valor NULL e qualquer outro valor, retornam desconhecidos porque o valor de cada NULL é desconhecido.  

Este comportamento é diferente do U-SQL, que segue `null` a semântica C# onde é diferente de qualquer valor, mas igual a si mesmo.  

Assim, uma declaração `SELECT` SparkSQL que usa `WHERE column_name = NULL` linhas de `column_name`retorno zero mesmo se houver valores NULL `column_name` em , `null`enquanto no U-SQL, ele retornaria as linhas onde está definido para . Da mesma forma, uma declaração spark `SELECT` que usa `WHERE column_name != NULL` linhas zero `column_name`retorna mesmo se houver valores não nulos em , enquanto no U-SQL, ele retornaria as linhas que não têm nulo. Assim, se você quiser a semântica de verificação nula u-sql, você deve usar [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) e [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) respectivamente (ou seu equivalente DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transformar objetos de catálogo U-SQL

Uma grande diferença é que os Scripts U-SQL podem fazer uso de seus objetos de catálogo, muitos dos quais não têm equivalente direto do Spark.

A Spark fornece suporte para os conceitos da loja Hive Meta, principalmente bancos de dados e tabelas, para que você possa mapear bancos de dados e esquemas U-SQL para bancos de dados hive, e tabelas U-SQL para tabelas Spark (ver [Dados móveis armazenados em tabelas U-SQL),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)mas não tem suporte para visualizações, funções valorizadas em tabelas (TVFs), procedimentos armazenados, conjuntos U-SQL, fontes de dados externos etc.

Os objetos de código U-SQL, como visualizações, TVFs, procedimentos armazenados e conjuntos podem ser modelados através de funções de código e bibliotecas em Spark e referenciados usando a função da linguagem do host e mecanismos de abstração processual (por exemplo, através da importação Módulos Python ou referência às funções de Scala).

Se o catálogo U-SQL tiver sido usado para compartilhar dados e objetos de código entre projetos e equipes, então mecanismos equivalentes para compartilhamento devem ser usados (por exemplo, Maven para compartilhamento de objetos de código).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transforme expressões de conjunto de linhas U-SQL e expressões escalares baseadas em SQL

A linguagem principal do U-SQL está transformando conjuntos de linhas e é baseada em SQL. A seguir está uma lista não exaustiva das expressões de conjunto de linhas mais comuns oferecidas no U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Agregados+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` expressões
- `CROSS`/`OUTER``APPLY` expressões
- `PIVOT`/`UNPIVOT`Expressões
- `VALUES`construtor rowset

- Definir expressões`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Além disso, o U-SQL fornece uma variedade de expressões escalares baseadas em SQL, como

- `OVER`expressões de janelas
- uma variedade de agregadores embutidos e funções `FIRST` de classificação (etc.)`SUM`
- Algumas das expressões escalares sql `CASE` `LIKE`mais`NOT`familiares: , ( ) `IN`, `AND`, etc. `OR`

Spark oferece expressões equivalentes em sua forma DSL e SparkSQL para a maioria dessas expressões. Algumas das expressões não suportadas nativamente em Spark terão que ser reescritas usando uma combinação das expressões nativas de Faísca e padrões semanticamente equivalentes. Por exemplo, `OUTER UNION` terá que ser traduzido na combinação equivalente de projeções e sindicatos.

Devido ao tratamento diferente dos valores NULL, uma adesão ao U-SQL sempre corresponderá a uma linha se ambas as colunas que estão sendo comparadas contiverem um valor NULL, enquanto uma participação no Spark não corresponderá a essas colunas a menos que verificações nulas explícitas sejam adicionadas.

## <a name="transform-other-u-sql-concepts"></a>Transforme outros conceitos u-sql

O U-SQL também oferece uma variedade de outros recursos e conceitos, como consultas federadas contra bancos de dados SQL Server, parâmetros, variáveis de expressão escalar e lambda, variáveis do sistema, `OPTION` dicas.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Consultas federadas contra bancos de dados sql server/tabelas externas

O U-SQL fornece fontes de dados e tabelas externas, bem como consultas diretas contra o Banco de Dados SQL do Azure. Embora o Spark não ofereça as mesmas abstrações de objeto, ele fornece [conector Spark para o Banco de Dados SQL Do Azure](../sql-database/sql-database-spark-connector.md) que pode ser usado para consultar bancos de dados SQL.

### <a name="u-sql-parameters-and-variables"></a>Parâmetros e variáveis U-SQL

Parâmetros e variáveis de usuário têm conceitos equivalentes no Spark e seus idiomas de hospedagem.

Por exemplo, no Scala, você `var` pode definir uma variável com a palavra-chave:

```
var x = 2 * 3;
println(x)
```

As variáveis do sistema u-sql (variáveis que começam com `@@`) podem ser divididas em duas categorias:

- Definir variáveis de sistema que podem ser definidas para valores específicos para impactar o comportamento dos scripts
- Variáveis do sistema informacional que indagam informações sobre o sistema e o nível de trabalho

A maioria das variáveis de sistema definidas não têm equivalente direto em Spark. Algumas das variáveis do sistema informacional podem ser modeladas passando as informações como argumentos durante a execução do trabalho, outras podem ter uma função equivalente na linguagem de hospedagem da Spark.

### <a name="u-sql-hints"></a>Dicas u-sql

O U-SQL oferece várias maneiras sintáticas de fornecer dicas para o mecanismo de otimização e execução de consulta:  

- Configuração de uma variável de sistema U-SQL
- uma `OPTION` cláusula associada à expressão rowset para fornecer uma dica de dados ou plano
- uma dica de juntar na sintaxe da `BROADCASTLEFT`expressão de juntar (por exemplo, )

O otimizador de consulta baseado em custos da Spark tem seus próprios recursos para fornecer dicas e ajustar o desempenho da consulta. Por favor, consulte a documentação correspondente.

## <a name="next-steps"></a>Próximas etapas

- [Entenda os formatos de dados spark para desenvolvedores U-SQL](understand-spark-data-formats.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Transformar dados usando a atividade do Spark no Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformar dados usando a atividade do Hive do Hadoop no Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
