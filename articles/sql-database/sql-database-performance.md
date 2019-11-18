---
title: Monitorar e melhorar o desempenho
description: O Banco de Dados SQL do Azure fornece ferramentas de desempenho para ajudá-lo a identificar áreas em que é possível melhorar o desempenho de consulta atual.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 474c2f4f00374ce785b81fe048e11cb353b3078a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151230"
---
# <a name="monitor-and-improve-performance"></a>Monitorar e melhorar o desempenho

O Banco de Dados SQL do Azure identifica possíveis problemas em seu banco de dados e recomenda ações que podem melhorar o desempenho de sua carga de trabalho, fornecendo ações de ajuste inteligente e recomendações.

## <a name="performance-tuning-options"></a>Opções de ajuste de desempenho

As opções de ajuste de desempenho disponíveis no banco de dados SQL do Azure estão disponíveis no menu de navegação do banco de dados em "desempenho inteligente":

| Opção de ajuste de desempenho | Suporte a banco de dados individual e banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Visão geral de desempenho** -monitorar todas as atividades de desempenho do banco de dados. | sim | Não | 
| **Recomendações de desempenho** -mostra as recomendações de desempenho que podem melhorar o desempenho da carga de trabalho. | sim | Não | 
| **Análise de desempenho de consultas** -mostra o desempenho de principais consultas de consumo no banco de dados. | sim | Não | 
| **Ajuste automático** -use o banco de dados SQL do Azure para otimizar automaticamente o desempenho do banco de dados. | sim | Não | 

## <a name="performance-overview"></a>Visão geral do desempenho

Essa exibição fornece um resumo do desempenho de seu banco de dados e lhe ajudará com ajustes e solução de problemas de desempenho. 

![Visão geral do desempenho do banco de dados SQL do Azure](./media/sql-database-performance/performance-overview-annotated.png)

* O bloco **Recomendações** fornece uma divisão das recomendações de ajuste para seu banco de dados (as três principais recomendações serão exibidas, se existirem mais). Ao clicar nesse bloco, será direcionado para **[Recomendações de desempenho](#performance-recommendations)** . 
* O bloco **Atividade de ajuste** fornece um resumo das atividades de ajuste em andamento e concluídas para o banco de dados, oferecendo uma visão rápida do histórico de atividades de ajuste. Clicar nesse bloco leva você para a exibição do histórico completo de ajustes do banco de dados.
* O bloco **Ajuste automático** mostra a[configuração de ajuste automático](sql-database-automatic-tuning-enable.md) para o banco de dados (opções de ajuste que são aplicadas automaticamente ao banco de dados). Clicar nesse bloco abre a caixa de diálogo de configuração de automação.
* O bloco **Consultas de banco de dados** mostra o resumo do desempenho de consulta do banco de dados (uso geral de DTU e consultas com maior consumo de recursos). Ao clicar nesse bloco, será direcionado para **[Análise de Desempenho de Consultas](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Recomendações do desempenho

Esta página fornece [recomendações de ajuste](sql-database-advisor.md) inteligente que podem melhorar o desempenho do banco de dados. Os seguintes tipos de recomendações são mostrados nesta página:

* Recomendações sobre quais índices criar ou soltar.
* Recomendações quando problemas de esquema são identificados no banco de dados.
* Recomendações quando consultas podem se beneficiar de consultas parametrizadas.

![Recomendações de desempenho para o banco de dados SQL do Azure](./media/sql-database-performance/performance-recommendations-annotated.png)

Você também pode encontrar o histórico completo de ajuste de ações que foram aplicadas no passado.

Saiba como localizar recomendações de desempenho de aplicativo no artigo [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).

## <a name="query-performance-insight"></a>Análise de desempenho de consultas

[Análise de Desempenho de Consultas](sql-database-query-performance.md) permite que você gaste menos tempo solucionando problemas de desempenho de banco de dados, fornecendo o seguinte:

* Mais informações sobre o consumo de recursos de bancos de dados (DTU). 
* As consultas que mais consomem CPU, que potencialmente podem ser ajustadas para melhorar o desempenho. 
* A capacidade de fazer drill down nos detalhes de uma consulta. 

  ![Informações de desempenho de consulta do banco de dados SQL do Azure](./media/sql-database-performance/query-performance-insights-annotated.png)

Localize mais informações sobre essa página no artigo  **[Como usar a Análise de Desempenho de Consultas](sql-database-query-performance.md)** .

## <a name="automatic-tuning"></a>Ajuste automático

Os bancos de dados SQL do Azure podem ajustar o desempenho do banco de dados automaticamente, aplicando as [recomendações de desempenho](sql-database-advisor.md). Para habilitá-lo, leia [como habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).

  ![Ajuste automático para o banco de dados SQL do Azure](./media/sql-database-performance/automatic-tuning-annotated.png)

Para saber mais, leia o artigo de [Ajuste automático](sql-database-automatic-tuning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos](sql-database-performance-guidance.md)
* [Quando um pool elástico deve ser usado?](sql-database-elastic-pool-guidance.md)
