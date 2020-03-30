---
title: Mapeamento do script de fluxo de dados
description: Visão geral do script de script de fluxo de dados da Fábrica de Dados por trás do idioma
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 92421125ecb5f4336922c6e6b4508fcdaf92be6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246391"
---
# <a name="data-flow-script-dfs"></a>Script de fluxo de dados (DFS)

O script de fluxo de dados (DFS) é o metadados subjacente, semelhante a uma linguagem de codificação, que é usado para executar as transformações que estão incluídas em um fluxo de dados de mapeamento. Toda transformação é representada por uma série de propriedades que fornecem as informações necessárias para executar o trabalho corretamente. O script é visível e editável a partir do ADF clicando no botão "script" na fita superior da interface do navegador.

![Botão de script](media/data-flow/scriptbutton.png "Botão de script")

Por exemplo, `allowSchemaDrift: true,` em uma transformação de origem diz ao serviço para incluir todas as colunas do conjunto de dados de origem no fluxo de dados, mesmo que não estejam incluídas na projeção do esquema.

## <a name="use-cases"></a>Casos de uso
O DFS é produzido automaticamente pela interface do usuário. Você pode clicar no botão Script para visualizar e personalizar o script. Você também pode gerar scripts fora da ADF UI e, em seguida, passá-lo para o cmdlet PowerShell. Ao depurar fluxos de dados complexos, você pode achar mais fácil digitalizar o código de script para trás em vez de digitalizar a representação do gráfico de interface do usuário de seus fluxos.

Aqui estão alguns exemplos de casos de uso:
- Produzir programaticamente muitos fluxos de dados que são bastante semelhantes, ou seja, fluxos de dados "carimbados".
- Expressões complexas que são difíceis de gerenciar na ui ou estão resultando em problemas de validação.
- Depuração e melhor compreensão de vários erros retornados durante a execução.

Quando você constrói um script de fluxo de dados para usar com o PowerShell ou uma API, você deve colapsar o texto formatado em uma única linha. Você pode manter as guias e as novidades como personagens de fuga. Mas o texto deve ser formatado para caber dentro de uma propriedade JSON. Há um botão na ui do editor de script na parte inferior que formatará o script como uma única linha para você.

![botão Copiar](media/data-flow/copybutton.png "botão Copiar")

## <a name="how-to-add-transforms"></a>Como adicionar transformações
Adicionar transformações requer três etapas básicas: adicionar os dados de transformação do núcleo, redirecionar o fluxo de entrada e, em seguida, redirecionar o fluxo de saída. Isso pode ser visto de forma mais fácil em um exemplo.
Digamos que comecemos com uma fonte simples para afundar o fluxo de dados como o seguinte:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Se decidirmos adicionar uma transformação derivada, primeiro precisamos criar o texto de transformação central, que tem uma expressão simples para adicionar uma nova coluna maiúscula chamada `upperCaseTitle`:
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Em seguida, pegamos o DFS existente e adicionamos a transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

E agora redirecionamos o fluxo de entrada identificando qual transformação queremos que `source1`a nova transformação venha depois (neste caso) e copiando o nome do fluxo para a nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Finalmente identificamos a transformação que queremos vir após essa nova transformação, e substituímos seu fluxo de entrada (neste caso, `sink1`) pelo nome de fluxo de saída de nossa nova transformação:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Fundamentos do DFS
O DFS é composto por uma série de transformações conectadas, incluindo fontes, sinks e várias outras que podem adicionar novas colunas, filtrar dados, unir dados e muito mais. Normalmente, o roteiro começa com uma ou mais fontes seguidas de muitas transformações e terminando com uma ou mais pias.

Todas as fontes têm a mesma construção básica:
```
source(
  source properties
) ~> source_name
```

Por exemplo, uma fonte simples com três colunas (movieId, título, gêneros) seria:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Todas as transformações que não as fontes têm a mesma construção básica:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Por exemplo, uma simples transformação derivada que pega uma coluna (título) e a substitui com uma versão maiúscula seria a seguinte:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

E uma pia sem esquema seria simplesmente:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Trechos de script

### <a name="aggregated-summary-stats"></a>Estatísticas de resumo agregadas
Adicione uma transformação agregada ao seu fluxo de dados chamada "SummaryStats" e cole neste código abaixo para a função agregada em seu script, substituindo as SummaryStats existentes. Isso fornecerá um padrão genérico para estatísticas de resumo de perfil de dados.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Você também pode usar a amostra abaixo para contar o número de linhas únicas e o número de linhas distintas em seus dados. O exemplo abaixo pode ser colado em um fluxo de dados com transformação agregada chamada ValueDistAgg. Este exemplo usa uma coluna chamada "título". Certifique-se de substituir "título" pela coluna string em seus dados que você deseja usar para obter contagens de valor.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Inclua todas as colunas em um agregado
Este é um padrão agregado genérico que demonstra como você pode manter as colunas restantes em seus metadados de saída quando você está construindo agregados. Neste caso, usamos ```first()``` a função para escolher o primeiro valor em cada coluna cujo nome não é "filme". Para usar isso, crie uma transformação agregada chamada DistinctRows e cole isso no seu script sobre o script agregado distinctrows existente.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Criar impressão digital hash da linha 
Use este código em seu script de fluxo ```DWhash``` de dados ```sha1``` para criar uma nova coluna derivada chamada que produz um hash de três colunas.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>Próximas etapas

Explore fluxos de dados começando com o artigo de visão geral dos [fluxos de dados](concepts-data-flow-overview.md)
