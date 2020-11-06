---
title: Dados processados com o pool SQL sem servidor
description: Este documento descreve como os dados processados são calculados ao consultar dados no armazenamento do Azure usando o pool SQL sem servidor.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423879"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Dados processados com o pool SQL sem servidor no Azure Synapse Analytics

Os dados processados são a quantidade de dados armazenados temporariamente no sistema durante a execução de uma consulta e consiste em:

- Quantidade de dados lidos do armazenamento – que inclui:
  - Quantidade de dados lidos durante a leitura de dados
  - Quantidade de dados lidos durante a leitura de metadados (para formatos de arquivo que contêm metadados, como parquet)
- Quantidade de dados em resultados intermediários – dados transferidos entre nós durante a execução da consulta, incluindo a transferência de dados para o ponto de extremidade, em formato descompactado. 
- Quantidade de dados gravados no armazenamento – se você usar CETAS para exportar o conjunto de resultados para armazenamento, você será cobrado por bytes gravados e pela quantidade de dados processados para a parte SELECT de CETAS.

A leitura de arquivos do armazenamento é altamente otimizada e usa:

- Pré-busca-que pode adicionar uma pequena sobrecarga à quantidade de dados lidos. Se uma consulta ler um arquivo inteiro, não haverá sobrecarga. Se um arquivo for lido parcialmente, como nas consultas TOP N, um pouco mais dados serão lidos com a pré-busca.
- Analisador CSV otimizado – se você usar PARSER_VERSION = ' 2.0 ' para ler arquivos CSV, isso resultará em quantidades ligeiramente maiores de dados lidos do armazenamento.  O analisador CSV otimizado lê arquivos em paralelo em partes de tamanho igual. Não há nenhuma garantia de que as partes conterão linhas inteiras. Para garantir que todas as linhas sejam analisadas, pequenos fragmentos de partes adjacentes também serão lidos, adicionando uma pequena quantidade de sobrecarga.

## <a name="statistics"></a>Estatísticas

O otimizador de consulta do pool SQL sem servidor se baseia em estatísticas para gerar planos de execução de consulta ideais. Você pode criar estatísticas manualmente ou elas serão criadas automaticamente pelo pool SQL sem servidor. De qualquer forma, as estatísticas são criadas executando uma consulta separada que retorna uma coluna específica na taxa de amostragem fornecida. Esta consulta tem uma quantidade associada de dados processados.

Se você executar o mesmo ou qualquer outra consulta que possa se beneficiar das estatísticas criadas, as estatísticas serão reutilizadas, se possível, e não haverá dados adicionais processados para a criação de estatísticas.

A criação de estatísticas para uma coluna parquet resultará na leitura somente da coluna relevante dos arquivos. A criação de estatísticas para uma coluna CSV resultará na leitura e análise de arquivos inteiros.

## <a name="rounding"></a>Arredondamento

A quantidade de dados processados será arredondada para os MB mais próximos por consulta, com um mínimo de 10 MB de dados processados por consulta.

## <a name="what-is-not-included-in-data-processed"></a>O que não está incluído nos dados processados

- Metadados de nível de servidor (como logons, funções, credenciais de nível de servidor)
- Os bancos de dados que você cria no ponto de extremidade como esses bancos de dados contêm apenas metadados (como usuários, funções, esquemas, exibições, TVFs embutida, procedimentos armazenados, credenciais no escopo do banco de dados, fontes externas, formatos de arquivo externo, tabelas externas)
  - Se você usar a inferência de esquema, fragmentos de arquivos serão lidos para inferir nomes de coluna e tipos de dados
- Instruções DDL, exceto CREATE STATISTICs, pois processarão dados do armazenamento com base na porcentagem de amostragem especificada
- Consultas somente de metadados

## <a name="reduce-amount-of-data-processed"></a>Reduzir a quantidade de dados processados

Você pode otimizar a quantidade por consulta de dados processados e obter melhor desempenho ao particionar e converter seus dados em um formato de coluna compactado como parquet.

## <a name="examples"></a>Exemplos

Digamos que haja duas tabelas, cada uma com os mesmos dados em cinco colunas igualmente dimensionadas:

- tabela de population_csv com suporte de 5 TB de arquivos CSV
- population_parquet tabela apoiada por 1 TB de arquivos parquet – essa tabela é menor do que a anterior, uma vez que parquet contém dados compactados
- tabela de very_small_csv com suporte de 100 KB de arquivos CSV

**#1 de consulta** : selecione Sum (população) de population_csv

Esta consulta lerá e analisará arquivos inteiros para obter valores para a coluna de população. Os nós processarão fragmentos dessa tabela, a soma da população para cada fragmento será transferida entre os nós e a soma final será transferida para o ponto de extremidade. Essa consulta processará 5 TB de dados, além de uma pequena sobrecarga para a transferência de somas de fragmentos.

**#2 de consulta** : selecione Sum (população) de population_parquet

Consultar formatos compactados e orientados a colunas, como parquet, resulta na leitura de menos dados do que na consulta anterior, pois o pool SQL sem servidor lerá uma única coluna compactada em vez de todo o arquivo. Nesse caso, 0,2 TB serão lidos (cinco colunas de tamanho igual, 0,2 TB cada). Os nós processarão fragmentos dessa tabela, a soma da população para cada fragmento será transferida entre os nós e a soma final será transferida para o ponto de extremidade. Essa consulta processará 0,2 TB, além de uma pequena sobrecarga para a transferência de somas de fragmentos.

**#3 de consulta** : select * from population_parquet

Esta consulta lerá todas as colunas e transferirá todos os dados no formato descompactado. Se o formato de compactação for 5:1, ele processará 6 TB, pois ele lerá 1 TB + transferir 5 TB de dados descompactados.

**#4 de consulta** : selecione Count (*) de very_small_csv

Essa consulta lerá arquivos inteiros. O tamanho total dos arquivos no armazenamento para esta tabela é de 100 KB. Os nós processarão fragmentos dessa tabela, a soma de cada fragmento será transferida entre os nós e a soma final será transferida para o ponto de extremidade. Essa consulta processará um pouco mais de 100 KB de dados. A quantidade de dados processados para essa consulta será arredondada para 10 MB conforme especificado em [arredondamento](#rounding).

## <a name="next-steps"></a>Próximas etapas

Para saber como otimizar suas consultas de desempenho, verifique [as práticas recomendadas para o pool SQL sem servidor](best-practices-sql-on-demand.md).
