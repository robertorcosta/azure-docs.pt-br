---
title: Recomendações de SQL do Synapse
description: Saiba mais sobre as recomendações de SQL do Synapse e como elas são geradas
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 546945d70554adbb28f19a3153faa67495e55f04
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607735"
---
# <a name="synapse-sql-recommendations"></a>Recomendações de SQL do Synapse

Este artigo descreve as recomendações de SQL do Synapse servidas por meio do Azure Advisor.  

A análise de SQL fornece recomendações para garantir que sua carga de trabalho data warehouse seja consistentemente otimizada para o desempenho. As recomendações são totalmente integradas ao [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para fornecer as práticas recomendadas diretamente dentro do [portal do Azure](https://aka.ms/Azureadvisor). A análise de SQL coleta recomendações de telemetria e de superfícies para sua carga de trabalho ativa em uma cadência diária. Os cenários de recomendação com suporte são descritos abaixo, juntamente com como aplicar as ações recomendadas.

Você pode [verificar suas recomendações](https://aka.ms/Azureadvisor) hoje mesmo! 

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar movimentação adicional de dados ou afunilamentos de recursos ao executar sua carga de trabalho. A seguinte documentação descreve mostrar para identificar distorção de dados e impedir que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e Remover distorção](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Estatísticas não ou desatualizadas

Ter estatísticas de qualidade inferior pode afetar seriamente o desempenho da consulta, pois isso pode fazer com que o otimizador de consulta do SQL gere planos de consulta de qualidade inferior. A documentação a seguir descreve as melhores práticas de criação e atualização de estatísticas:

- [Criando e atualizando estatísticas da tabela](sql-data-warehouse-tables-statistics.md)

Para ver a lista de tabelas afetadas por essas recomendações, execute o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O Assistente executa continuamente o mesmo script T-SQL para gerar essas recomendações.

## <a name="replicate-tables"></a>Replicar tabelas

Para obter recomendações de tabela replicada, o Assistente detecta possíveis tabelas com base nas seguintes características físicas:

- Tamanho da tabela replicada
- Número de Colunas
- Tipo de distribuição de tabelas
- Número of partições

O Assistente continuamente utiliza heurística com base em carga de trabalho, como, por exemplo, frequência de acesso de tabela, linhas retornadas em média e limites de tamanho e atividade do data warehouse, para garantir que recomendações de alta qualidade sejam geradas.

A seção a seguir descreve a heurística baseada em carga de trabalho que você pode encontrar na portal do Azure para cada recomendação de tabela replicada:

- Examinar o percentual médio avg- de linhas que foram retornadas da tabela para cada acesso de tabela nos últimos sete dias
- Leitura frequente, nenhuma atualização - indica que a tabela não foi atualizada nos últimos sete dias e mostra a atividade de acesso
- Taxa de leitura/atualização - a frequência em que a tabela foi acessada em relação a quando foi atualizada nos últimos sete dias
- Atividade - mede o uso com base na atividade de acesso. Essa atividade compara a atividade de acesso à tabela relativa à atividade média de acesso à tabela no data warehouse nos últimos sete dias.

Atualmente, o Assistente mostrará apenas no máximo quatro tabelas replicadas possíveis por vez, com índices columnstore clusterizados priorizando a atividade mais alta.

> [!IMPORTANT]
> A recomendação de tabela replicada não é à prova de falhas e não leva em consideração as operações de movimentação de dados da conta. Estamos trabalhando para adicionar isso como uma heurística, mas por enquanto você deverá sempre validar sua carga de trabalho após a aplicação da recomendação. Para saber mais sobre as tabelas replicadas, visite a seguinte [documentação](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Utilização de cache adaptável (Gen2)
Quando você tem um grande conjunto de trabalho, pode experimentar um percentual de falha de cache baixo e alta utilização de cache. Para esse cenário, você deve escalar verticalmente para aumentar a capacidade do cache e executar novamente a carga de trabalho. Para obter mais informações, visite a [documentação](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache)a seguir. 

## <a name="tempdb-contention"></a>Contenção de tempdb

O desempenho da consulta pode diminuir quando há uma contenção de tempdb alta.  A contenção de tempdb pode ocorrer por meio de tabelas temporárias definidas pelo usuário ou quando há uma grande quantidade de movimentação de dados. Para esse cenário, você pode dimensionar para mais alocação de tempdb e [configurar classes de recursos e gerenciamento de carga de trabalho](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) para fornecer mais memória às suas consultas. 
