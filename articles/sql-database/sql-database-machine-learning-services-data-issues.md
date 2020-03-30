---
title: Trabalhe com tipos e objetos de dados R e SQL
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Aprenda a trabalhar com tipos de dados e objetos de dados em R com o Banco de Dados Azure SQL usando serviços de aprendizagem de máquina (visualização), incluindo problemas comuns que você pode encontrar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 0bb3abc7b7102da55c9ededcadd7a301f74065ab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349330"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Trabalhar usando dados do R e do SQL nos Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Este artigo discute alguns dos problemas comuns que você pode encontrar ao mover dados entre O Banco de Dados R e SQL em [Serviços de Aprendizagem de Máquina (com R) no Banco de Dados SQL do Azure](sql-database-machine-learning-services-overview.md). A experiência obtida por meio deste exercício fornece um contexto essencial ao trabalhar usando dados em seu próprio script.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Os problemas comuns que você pode encontrar incluem:

- Os tipos de dados às vezes não correspondem
- Conversões implícitas podem ocorrer
- Às vezes, operações de conversão são necessárias
- R e SQL usam objetos de dados diferentes

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um Banco de dados SQL do Azure com os Serviços do Machine Learning (com R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

## <a name="working-with-a-data-frame"></a>Trabalhando com um quadro de dados

Quando o script retorna os resultados de R para SQL, ele deve retornar os dados como um **data.frame**. Qualquer outro tipo de objeto gerado no seu script, seja uma lista, fator, vetor ou dados binários, deverá ser convertido em uma estrutura de dados se você desejar enviá-lo como parte dos resultados do procedimento armazenado. Felizmente, existem várias funções de R para dar suporte à conversão de outros objetos em um quadro de dados. Você pode até mesmo serializar um modelo binário e devolvê-lo em um quadro de dados, o que você fará mais tarde neste artigo.

Primeiro, vamos experimentar alguns objetos R básicos - vetores, matrizes e listas - e ver como a conversão para um quadro de dados muda a saída passada para SQL.

Compare esses dois scripts "Olá, Mundo" em R. Os scripts parecem quase idênticos, mas o primeiro retorna uma única coluna de três valores, enquanto o segundo retorna três colunas com um único valor cada.

**Exemplo 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Exemplo 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Por que os resultados são tão diferentes?

A resposta geralmente pode ser encontrada usando o comando `str()` de R. Adicione a função `str(object_name)` em qualquer ponto do script R para fazer com que o esquema de dados do objeto R especificado seja retornado como uma mensagem informativa. Você pode visualizar as mensagens na guia **Mensagens** no SSMS.

Para descobrir por que os exemplos 1 e 2 demonstram resultados tão diferentes, insira a linha `str(OutputDataSet)` no final da definição de variável `@script` em cada instrução, dessa forma:

**Exemplo 1 com a função str adicionada**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exemplo 2 com a função str adicionada**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Agora, examine o texto em **Mensagens** para ver por que a saída é diferente.

**Resultados – Exemplo 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultados – Exemplo 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Como você pode ver, uma pequena alteração na sintaxe de R teve um grande efeito no esquema dos resultados. Para todos os detalhes, as diferenças nos tipos de dados R são explicadas em detalhes na seção *Estruturas de Dados* em ["Avançado R" por Hadley Wickham](http://adv-r.had.co.nz).

Por enquanto, apenas esteja ciente de que você precisa verificar os resultados esperados ao moldar objetos R em quadros de dados.

> [!TIP]
> Você também pode usar funções `is.matrix`de `is.vector`identidade R, como , para retornar informações sobre a estrutura de dados internos.

## <a name="implicit-conversion-of-data-objects"></a>Conversão implícita de objetos de dados

Cada objeto de dados R tem suas próprias regras para o modo como os valores são manipulados quando combinados com outros objetos de dados se os objetos de dados têm o mesmo número de dimensões ou se qualquer objeto de dados contém tipos de dados heterogêneos.

Por exemplo, suponha que você deseja executar a multiplicação da matriz usando R. Você deseja multiplicar uma matriz de uma coluna única com os três valores por uma matriz com quatro valores, e esperar uma matriz 4x3 como resultado.

Primeiro, crie uma pequena tabela de dados de teste.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Agora execute o seguinte script.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Nos bastidores, a coluna de três valores é convertida em uma matriz de coluna única. Como uma matriz é apenas um caso especial de uma matriz de R, a matriz `y` é implicitamente moldada em uma matriz de coluna única para que os dois argumentos estejam em conformidade.

**Resultados**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1.400|1500|

No entanto, observe o que acontece quando você altera o tamanho da matriz `y`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Agora, R retorna um único valor como resultado.

**Resultados**
    
|Col1|
|---|
|1542|

Por quê? Nesse caso, como os dois argumentos podem ser tratados como vetores de mesmo tamanho, R retorna o produto interno como uma matriz.  Este é o comportamento esperado de acordo com as regras da álgebra linear. No entanto, pode causar problemas se o aplicativo downstream espera que o esquema de saída nunca mude!

## <a name="merge-or-multiply-columns-of-different-length"></a>Mesclar ou multiplicar colunas de tamanhos diferentes

O R fornece uma ótima flexibilidade para trabalhar com vetores de tamanhos diferentes e combinar essas estruturas semelhantes a colunas em estruturas de dados. Listas de vetores podem se parecer com uma tabela, mas elas não seguem as regras que regem as tabelas de banco de dados.

Por exemplo, o script a seguir define uma matriz numérica de tamanho 6 e a armazena na variável `df1` de R. A matriz numérica é então combinada com os inteiros da tabela RTestData (criada acima) que contém três (3) valores, para fazer um novo quadro de dados, `df2`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Para preencher o quadro de dados, R repete os elementos recuperados do RTestData quantas vezes forem necessárias para corresponder ao número de elementos na matriz `df1`.

**Resultados**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Lembre-se que um quadro de dados só se parece com uma tabela, mas na verdade é uma lista de vetores.

## <a name="cast-or-convert-sql-data"></a>Lançar ou converter dados SQL

R e SQL não usam os mesmos tipos de dados, então quando você executa uma consulta no SQL para obter dados e, em seguida, passar isso para o tempo de execução R, algum tipo de conversão implícita geralmente ocorre. Outro conjunto de conversões ocorre quando você retorna dados de R para SQL.

- O SQL empurra os dados da consulta para o processo R e converte-os em uma representação interna para maior eficiência.
- O runtime do R carrega os dados em uma variável data.frame e executa suas próprias operações nos dados.
- O mecanismo de banco de dados retorna os dados ao SQL usando uma conexão interna segura e apresenta os dados em termos de tipos de dados SQL.
- Você obtê os dados conectando-se ao SQL usando um cliente ou biblioteca de rede que pode emitir consultas SQL e lidar com conjuntos de dados tabulares. Esse aplicativo cliente pode afetar os dados de outras maneiras.

Para ver como isso funciona, execute uma consulta como esta no data warehouse [AdventureWorksDW.](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) Essa exibição retorna dados de vendas usados na criação de previsões.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Você pode usar qualquer versão do AdventureWorks ou criar uma consulta diferente usando um banco de dados próprio. O ponto é tentar lidar com alguns dados que contenham texto, data e valores numéricos.

Agora, tente usar essa consulta como entrada para o procedimento armazenado.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Se você receber um erro, provavelmente precisará fazer algumas modificações no texto da consulta. Por exemplo, predicado de cadeia de caracteres na cláusula WHERE deve estar entre dois conjuntos de aspas simples.

Depois de fazer a consulta funcionar, examine os resultados da função `str` para ver como R trata os dados de entrada.

**Resultados**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A coluna datetime foi processada usando o tipo de dados de R, **POSIXct**.
- A coluna de texto "ProductSeries" foi identificada como um **fator,** ou seja, uma variável categórica. Valores de cadeia de caracteres são tratados como fatores por padrão. Se você passar uma cadeia de caracteres R, ele será convertida em um inteiro para uso interno e, em seguida, mapeada para cadeia de caracteres na saída.

## <a name="summary"></a>Resumo

Com base até mesmo nesses pequenos exemplos, você pode ver a necessidade de verificar os efeitos da conversão de dados ao passar consultas SQL como entrada. Como alguns tipos de dados SQL não são suportados por R, considere essas maneiras de evitar erros:

- Teste os dados com antecedência e verifique as colunas ou valores em seu esquema que poderiam ser um problema ao ser passados para o código R.
- Especifique as colunas na fonte de dados de entrada individualmente, em vez de usar `SELECT *` e saiba como cada coluna será tratada.
- Execute conversões explícitas conforme necessário durante a preparação dos dados de entrada, a fim de evitar surpresas.
- Evite passar colunas de dados (como GUIDs ou RowGuids) que causam erros e não são úteis para modelagem.

Para obter mais informações sobre os tipos de dados R suportados e não suportados, consulte [bibliotecas R e tipos de dados](/sql/advanced-analytics/r/r-libraries-and-data-types).
