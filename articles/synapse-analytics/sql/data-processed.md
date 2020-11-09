---
title: Dados processados usando o pool SQL sem servidor
description: Este documento descreve como o valor processado por dados é calculado quando você consulta dados no data Lake.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381194"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Dados processados usando o pool SQL sem servidor no Azure Synapse Analytics

Os *dados processados* são a quantidade de dados que o sistema armazena temporariamente enquanto uma consulta é executada. Os dados processados consistem nas seguintes quantidades:

- Quantidade de dados lidos do armazenamento. Essa quantidade inclui:
  - Dados lidos durante a leitura de dados.
  - Dados lidos durante a leitura de metadados (para formatos de arquivo que contêm metadados, como parquet).
- Quantidade de dados em resultados intermediários. Esses dados são transferidos entre nós enquanto a consulta é executada. Ele inclui a transferência de dados para seu ponto de extremidade, em um formato descompactado. 
- Quantidade de dados gravados no armazenamento. Se você usar CETAS para exportar o conjunto de resultados para armazenamento, a quantidade de dados gravados será adicionada à quantidade de dados processados para a parte SELECT de CETAS.

A leitura de arquivos do armazenamento é altamente otimizada. O processo usa:

- Pré-busca, que pode adicionar alguma sobrecarga à quantidade de dados lidos. Se uma consulta ler um arquivo inteiro, não haverá sobrecarga. Se um arquivo for lido parcialmente, como nas consultas TOP N, um pouco mais dados serão lidos usando a pré-busca.
- Um analisador de CSV (valor separado por vírgula) otimizado. Se você usar PARSER_VERSION = ' 2.0 ' para ler arquivos CSV, as quantidades de dados lidos do armazenamento aumentarão um pouco. Um analisador CSV otimizado lê arquivos em paralelo, em partes de tamanho igual. As partes não contêm necessariamente linhas inteiras. Para garantir que todas as linhas sejam analisadas, o analisador CSV otimizado também lê pequenos fragmentos de partes adjacentes. Esse processo adiciona uma pequena quantidade de sobrecarga.

## <a name="statistics"></a>Estatísticas

O otimizador de consulta do pool SQL sem servidor se baseia em estatísticas para gerar planos de execução de consulta ideais. Você pode criar estatísticas manualmente. Caso contrário, o pool SQL sem servidor os criará automaticamente. De qualquer forma, as estatísticas são criadas com a execução de uma consulta separada que retorna uma coluna específica em uma taxa de amostra fornecida. Esta consulta tem uma quantidade associada de dados processados.

Se você executar o mesmo ou qualquer outra consulta que possa se beneficiar das estatísticas criadas, as estatísticas serão reutilizadas, se possível. Não há dados adicionais processados para a criação de estatísticas.

Quando as estatísticas são criadas para uma coluna parquet, somente a coluna relevante é lida a partir dos arquivos. Quando as estatísticas são criadas para uma coluna CSV, arquivos inteiros são lidos e analisados.

## <a name="rounding"></a>Arredondamento

A quantidade de dados processados é arredondada para o MB mais próximo por consulta. Cada consulta tem um mínimo de 10 MB de dados processados.

## <a name="what-data-processed-doesnt-include"></a>Quais dados processados não incluem

- Metadados de nível de servidor (como logons, funções e credenciais de nível de servidor).
- Bancos de dados que você cria em seu ponto de extremidade. Esses bancos de dados contêm apenas metadados (como usuários, funções, esquemas, modos de exibição, funções com valor de tabela embutida [TVFs], procedimentos armazenados, credenciais no escopo do banco de dados, fontes externas, formatos de arquivos externos e tabelas externas).
  - Se você usar a inferência de esquema, os fragmentos de arquivo serão lidos para inferir nomes de coluna e tipos de dados, e a quantidade de dados lidos será adicionada à quantidade de dados processados.
- Instruções DDL (linguagem de definição de dados), exceto para a instrução CREATE STATISTICs porque ele processa dados do armazenamento com base na porcentagem de amostra especificada.
- Consultas somente de metadados.

## <a name="reducing-the-amount-of-data-processed"></a>Reduzindo a quantidade de dados processados

Você pode otimizar a quantidade de dados por consulta processada e melhorar o desempenho ao particionar e converter seus dados em um formato baseado em coluna compactado, como parquet.

## <a name="examples"></a>Exemplos

Imagine três tabelas.

- A tabela de population_csv é apoiada por 5 TB de arquivos CSV. Os arquivos são organizados em cinco colunas igualmente dimensionadas.
- A tabela population_parquet tem os mesmos dados que a tabela population_csv. Ele tem o suporte de 1 TB de arquivos parquet. Esta tabela é menor do que a anterior porque os dados são compactados no formato parquet.
- A tabela de very_small_csv tem o suporte de 100 KB de arquivos CSV.

**Consulta 1** : selecione Sum (população) de population_csv

Esta consulta lê e analisa arquivos inteiros para obter valores para a coluna de população. Nós processam fragmentos dessa tabela e a soma da população para cada fragmento é transferida entre nós. A soma final é transferida para o ponto de extremidade. 

Essa consulta processa 5 TB de dados mais uma pequena sobrecarga de valor para transferir somas de fragmentos.

**Consulta 2** : selecione Sum (população) de population_parquet

Quando você consulta formatos compactados e baseados em colunas como parquet, menos dados são lidos do que na consulta 1. Você verá esse resultado porque o pool SQL sem servidor lê uma única coluna compactada em vez de todo o arquivo. Nesse caso, 0,2 TB é lido. (Cinco colunas igualmente dimensionadas são 0,2 TB cada.) Nós processam fragmentos dessa tabela e a soma da população para cada fragmento é transferida entre nós. A soma final é transferida para o ponto de extremidade. 

Essa consulta processa 0,2 TB mais uma pequena quantidade de sobrecarga para a transferência de somas de fragmentos.

**Consulta 3** : selecionar * de population_parquet

Essa consulta lê todas as colunas e transfere todos os dados em um formato descompactado. Se o formato de compactação for 5:1, a consulta processará 6 TB porque ela lê 1 TB e transfere 5 TB de dados descompactados.

**Consulta 4** : selecionar contagem (*) de very_small_csv

Essa consulta lê arquivos inteiros. O tamanho total dos arquivos no armazenamento para esta tabela é de 100 KB. Nós processam fragmentos dessa tabela e a soma de cada fragmento é transferida entre nós. A soma final é transferida para o ponto de extremidade. 

Essa consulta processa um pouco mais de 100 KB de dados. A quantidade de dados processados para essa consulta é arredondada para 10 MB, conforme especificado na seção [arredondamento](#rounding) deste artigo.

## <a name="next-steps"></a>Próximas etapas

Para saber como otimizar suas consultas de desempenho, consulte [práticas recomendadas para o pool SQL sem servidor](best-practices-sql-on-demand.md).
