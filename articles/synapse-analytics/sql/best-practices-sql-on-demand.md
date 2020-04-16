---
title: Melhores práticas para SQL sob demanda (pré-visualização) no Azure Synapse Analytics
description: Recomendações e práticas recomendadas que você deve saber como você trabalha com SQL sob demanda (visualização).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429064"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Melhores práticas para SQL sob demanda (pré-visualização) no Azure Synapse Analytics

Neste artigo, você encontrará uma coleção de práticas recomendadas para usar SQL sob demanda (visualização). O SQL sob demanda é um recurso adicional dentro do Azure Synapse Analytics.

## <a name="general-considerations"></a>Considerações gerais

O SQL sob demanda permite que você consulta arquivos em suas contas de armazenamento do Azure. Ele não tem recursos locais de armazenamento ou ingestão. Como tal, todos os arquivos que os alvos de consulta são externos ao SQL sob demanda. Tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Conta de armazenamento Azure e SQL sob demanda

Para minimizar a latência, colocalize sua conta de armazenamento Azure e seu ponto final SQL sob demanda. Contas de armazenamento e pontos finais provisionados durante a criação do espaço de trabalho estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com SQL sob demanda, certifique-se de que elas estão na mesma região. Se eles não estiverem na mesma região, haverá aumento da latência para a transferência de rede dos dados entre as regiões remota e final.

## <a name="azure-storage-throttling"></a>Estrangulamento do armazenamento azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. O estrangulamento do armazenamento ocorre quando o IOPS combinado ou o throughput gerado por aplicativos, serviços e carga de trabalho sob demanda excedem os limites da conta de armazenamento. Como resultado, você experimentará um efeito negativo significativo no desempenho da consulta.

Uma vez detectado o estrangulamento, o SQL sob demanda tem um tratamento incorporado deste cenário. O SQL sob demanda fará solicitações de armazenamento em um ritmo mais lento até que o estrangulamento seja resolvido.

> [!TIP]
> Para uma execução ideal de consulta, você não deve enfatizar a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

## <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para um melhor desempenho:

- Converter CSV para Parquet - Parquet é formato colunar. Uma vez que é compactado, seus tamanhos de arquivo são menores do que arquivos CSV com os mesmos dados. O SQL sob demanda precisará de menos tempo e solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como alvo um único arquivo grande, você se beneficiará de dividi-lo em vários arquivos menores.
- Tente manter o tamanho do arquivo CSV abaixo de 10 GB.
- É melhor ter arquivos de tamanho igual para um único caminho OPENROWSET ou uma tabela externa LOCATION.
- Particione seus dados armazenando partições para diferentes pastas ou nomes de arquivos - verifique [o uso de funções de nome de arquivo e filepath para segmentar partições específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Use funções fileinfo e filepath para segmentar partições específicas

Os dados são frequentemente organizados em partições. Você pode instruir o SQL sob demanda a consultar pastas e arquivos específicos. Esta função reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. Um bônus adicional é que você alcançará um melhor desempenho.

Para obter mais informações, verifique o [nome do arquivo](develop-storage-files-overview.md#filename-function) e as funções e exemplos do [filepath](develop-storage-files-overview.md#filepath-function) sobre como [consultar arquivos específicos](query-specific-files.md).

Se os dados armazenados não forem particionados, considere particioná-los para que você possa usar essas funções para otimizar consultas direcionadas a esses arquivos. Ao [consultar tabelas Spark particionadas](develop-storage-files-spark-tables.md) do SQL sob demanda, a consulta terá como alvo automaticamente apenas os arquivos necessários.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use cetas para melhorar o desempenho da consulta e se junte

[O CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no SQL sob demanda. O CETAS é uma operação paralela que cria metadados de tabela externa e exporta os resultados da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar o CETAS para armazenar partes usadas com freqüência de consultas, como tabelas de referência unidas, para um novo conjunto de arquivos. Em seguida, você pode se juntar a esta única tabela externa em vez de repetir as junções comuns em várias consultas.

À medida que o CETAS gera arquivos Parquet, as estatísticas serão criadas automaticamente quando a primeira consulta atingir essa tabela externa, resultando em um melhor desempenho.

## <a name="next-steps"></a>Próximas etapas

Revise o artigo [Solução de Problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para problemas e soluções comuns. Se você estiver trabalhando com pool SQL em vez de SQL sob demanda, consulte o artigo [de práticas recomendadas para o pool SQL](best-practices-sql-pool.md) para obter orientação específica.
