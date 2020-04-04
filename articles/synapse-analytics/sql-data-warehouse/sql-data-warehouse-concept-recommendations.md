---
title: Recomendações sinapse SQL
description: Saiba mais sobre as recomendações do Synapse SQL e como elas são geradas
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17877a1ef5d949fbbee080b6157844ac5b516fe7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633680"
---
# <a name="synapse-sql-recommendations"></a>Recomendações sinapse SQL

Este artigo descreve as recomendações sinapse sql atendidas através do Azure Advisor.  

O SQL Analytics fornece recomendações para garantir que a carga de trabalho do data warehouse seja otimizada consistentemente para o desempenho. As recomendações são fortemente integradas com [o Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para fornecer as melhores práticas diretamente dentro do [portal Azure](https://aka.ms/Azureadvisor). O SQL Analytics coleta telemetria e apresenta recomendações para sua carga de trabalho ativa em uma cadência diária. Os cenários de recomendação suportados são descritos abaixo, juntamente com como aplicar as ações recomendadas.

Você pode [conferir suas recomendações](https://aka.ms/Azureadvisor) hoje! Atualmente, esse recurso é aplicável apenas aos armazéns de dados Gen2.

## <a name="data-skew"></a>Distorção de dados

A distorção de dados pode causar movimentação adicional de dados ou afunilamentos de recursos ao executar sua carga de trabalho. A seguinte documentação descreve mostrar para identificar distorção de dados e impedir que isso aconteça selecionando uma chave de distribuição ideal.

- [Identificar e Remover distorção](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Estatísticas sem ou desatualizados

Ter estatísticas subótimas pode afetar severamente o desempenho da consulta, pois pode fazer com que o otimizador de consulta SQL gere planos de consulta subótimos. A documentação a seguir descreve as melhores práticas de criação e atualização de estatísticas:

- [Criando e atualizando estatísticas da tabela](sql-data-warehouse-tables-statistics.md)

Para ver a lista de tabelas afetadas por essas recomendações, execute o seguinte [script T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). O Assistente executa continuamente o mesmo script T-SQL para gerar essas recomendações.

## <a name="replicate-tables"></a>Replicar tabelas

Para obter recomendações de tabela replicada, o Assistente detecta possíveis tabelas com base nas seguintes características físicas:

- Tamanho da tabela replicada
- Número de Colunas
- Tipo de distribuição de tabelas
- Número of partições

O Assistente continuamente utiliza heurística com base em carga de trabalho, como, por exemplo, frequência de acesso de tabela, linhas retornadas em média e limites de tamanho e atividade do data warehouse, para garantir que recomendações de alta qualidade sejam geradas.

O exemplo a seguir descreve a heurística com base em carga de trabalho que pode ser encontrada no portal do Azure para cada recomendação de tabela replicada:

- Examinar o percentual médio avg- de linhas que foram retornadas da tabela para cada acesso de tabela nos últimos sete dias
- Leitura frequente, nenhuma atualização - indica que a tabela não foi atualizada nos últimos sete dias e mostra a atividade de acesso
- Taxa de leitura/atualização - a frequência em que a tabela foi acessada em relação a quando foi atualizada nos últimos sete dias
- Atividade - mede o uso com base na atividade de acesso. Isso se compara à atividade de acesso de tabela em relação à atividade média de acesso de tabela no data warehouse nos últimos sete dias.

Atualmente, o Assistente mostrará apenas no máximo quatro tabelas replicadas possíveis por vez, com índices columnstore clusterizados priorizando a atividade mais alta.

> [!IMPORTANT]
> A recomendação de tabela replicada não é à prova de falhas e não leva em consideração as operações de movimentação de dados da conta. Estamos trabalhando para adicionar isso como uma heurística, mas por enquanto você deverá sempre validar sua carga de trabalho após a aplicação da recomendação. Entre em contato com sqldwadvisor@service.microsoft.com se descobrir recomendações de tabela replicada fazem com que sua carga de trabalho retroceda. Para saber mais sobre as tabelas replicadas, visite a seguinte [documentação](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).
