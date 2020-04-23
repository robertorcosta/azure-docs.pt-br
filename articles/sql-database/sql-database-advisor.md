---
title: Recomendações de desempenho do Database Advisor para bancos de dados individuais e em pool
description: O banco de dados SQL do Azure fornece recomendações para bancos de dados individuais e em pool que podem melhorar o desempenho de consulta no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5b0aeec851c8f514492e32792f48e955597ced5
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096564"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Assistente do Banco de Dados recomendações de desempenho para bancos de dados individuais e em pool

O Banco de Dados SQL do Azure aprende e se adapta ao seu aplicativo. Para bancos de dados individuais e em pool, o banco de dados SQL tem uma série de consultores de banco de dados que fornecem recomendações personalizadas que permitem que você maximize o desempenho. Esses consultores de banco de dados avaliam e analisam continuamente o histórico de uso e fornecem recomendações baseadas em padrões de carga de trabalho que ajudam a melhorar o desempenho.

## <a name="performance-overview"></a>Visão geral do desempenho

A visão geral do desempenho fornece um resumo do desempenho do banco de dados e ajuda você com ajuste de desempenho e solução de problemas.

![Visão geral do desempenho do banco de dados SQL do Azure](./media/sql-database-performance/performance-overview-annotated.png)

- O bloco **Recomendações** fornece uma divisão das recomendações de ajuste para seu banco de dados (as três principais recomendações serão exibidas, se existirem mais). Clicar nesse bloco leva você às **[Opções de recomendação de desempenho](sql-database-advisor-portal.md#viewing-recommendations)**.
- O bloco **Atividade de ajuste** fornece um resumo das atividades de ajuste em andamento e concluídas para o banco de dados, oferecendo uma visão rápida do histórico de atividades de ajuste. Clicar nesse bloco leva você para a exibição do histórico completo de ajustes do banco de dados.
- O bloco de **ajuste automático** mostra a **[configuração de ajuste automático](sql-database-automatic-tuning-enable.md)** do banco de dados (opções de ajuste aplicadas automaticamente ao banco de dados). Clicar nesse bloco abre a caixa de diálogo de configuração de automação.
- O bloco **Consultas de banco de dados** mostra o resumo do desempenho de consulta do banco de dados (uso geral de DTU e consultas com maior consumo de recursos). Ao clicar nesse bloco, será direcionado para **[Análise de Desempenho de Consultas](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Opções de recomendação de desempenho

As opções de recomendação de desempenho disponíveis para bancos de dados individuais e em pool no banco de dados SQL do Azure são:

| Recomendação de desempenho | Suporte a banco de dados individual e banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Criar recomendações de índice** – recomenda a criação de índices que podem melhorar o desempenho da carga de trabalho. | Sim | Não |
| **Recomendações de drop index** – recomenda a remoção de índices redundantes e duplicados diariamente, exceto para índices exclusivos e índices que não foram usados por muito tempo (>90 dias). Observe que essa opção não é compatível com aplicativos que usam alternância de partição e dicas de índice. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico. | Sim | Não |
| **Parametrizar consultas recomendações (visualização)** – recomenda a parametrização forçada em casos em que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. | Sim | Não |
| **Corrigir recomendações de problemas de esquema (versão prévia)** – as recomendações para correção de esquema aparecem quando o serviço do banco de dados SQL observa uma anomalia no número de erros SQL relacionados ao esquema que estão ocorrendo no banco de dados SQL. A Microsoft está atualmente substituindo recomendações do tipo "Corrigir problema de esquema". | Sim | Não |

![Recomendações de desempenho para o banco de dados SQL do Azure](./media/sql-database-performance/performance-recommendations-annotated.png)

Para aplicar recomendações de desempenho, consulte [aplicando recomendações](sql-database-advisor-portal.md#applying-recommendations). Para exibir o status das recomendações, consulte [operações de monitoramento](sql-database-advisor-portal.md#monitoring-operations).

Você também pode encontrar o histórico completo de ajuste de ações que foram aplicadas no passado.

## <a name="create-index-recommendations"></a>Criar recomendações de índice

O banco de dados SQL do Azure monitora continuamente as consultas que estão em execução e identifica os índices que podem melhorar o desempenho. Após haver certeza suficiente de que um determinado índice está ausente, uma nova recomendação **Criar índice** é criada.

O Banco de Dados SQL do Azure aumenta a confiança estimando o ganho de desempenho que o índice trará ao longo do tempo. Dependendo do ganho de desempenho estimado, as recomendações são categorizadas como alta, média ou baixa.

Índices criados usando recomendações sempre são sinalizados como índices criados automaticamente. Você pode ver quais índices são criados automaticamente examinando a [exibição sys. Indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Os índices criados automaticamente não bloqueiam os comandos ALTER/RENAME.

Se você tentar descartar a coluna que tem um índice criado automaticamente por ela, o comando é passado. O índice criado automaticamente é descartado com o comando também. Índices regulares bloqueiam o comando ALTER/RENAME em colunas que são indexadas.

Depois que a recomendação para criar índice for aplicada, o Banco de Dados SQL do Azure comparará o desempenho das consultas com o desempenho de linha de base. Se o novo índice melhorou o desempenho, a recomendação é sinalizada como bem-sucedida e o relatório de impacto fica disponível. Se o índice não melhorar o desempenho, ele será revertido automaticamente. O banco de dados SQL do Azure usa esse processo para garantir que as recomendações melhorem o desempenho do banco de dados

Qualquer recomendação **criar índice** tem uma política de retirada que não permite aplicar a recomendação se o uso de recursos de um banco de dados ou pool estiver alto. A política de retirada leva em conta CPU, E/S de Dados, E/S de Log e armazenamento disponível.

Se a CPU, a e/s de dados ou a e/s de log for maior que 80% nos 30 minutos anteriores, a recomendação criar índice será adiada. Se o armazenamento disponível for inferior a 10% após o índice ser criado, a recomendação entrará em estado de erro. Se depois de alguns dias o ajuste automático ainda acreditar que o índice pode ser benéfico, o processo será iniciado novamente.

Esse processo se repetirá até que não haja armazenamento suficiente disponível para criar um índice ou até que o índice não seja visto mais como benéfico.

## <a name="drop-index-recommendations"></a>Recomendações para Remover Índice

Além de detectar índices ausentes, o banco de dados SQL do Azure analisa continuamente o desempenho de índices existentes. Se um índice não for usado, o banco de dados SQL do Azure recomendará cancelá-lo. Descartar um índice é recomendado em dois casos:

- O índice é uma duplicata de outro índice (mesma coluna indexada e incluída, esquema de partição e filtros).
- O índice não foi usado por um período prolongado (93 dias).

Recomendações para Remover Índice também passam por verificação após a implementação. Se o desempenho melhorar, o relatório de impacto ficará disponível. Se o desempenho diminuir, a recomendação será revertida.

## <a name="parameterize-queries-recommendations-preview"></a>Recomendações de parametrização de consultas (versão prévia)

Recomendações para *Parametrizar consultas* aparecem quando o serviço de Banco de Dados SQL detecta que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Essa condição cria uma oportunidade para aplicar a parametrização forçada. A parametrização forçada, por sua vez, permite que os planos de consulta sejam armazenadas em cache e reutilizados no futuro, o que melhora o desempenho e reduz o uso de recursos.

Cada consulta que é emitida para o SQL Server inicialmente precisa ser compilada para gerar um plano de execução. Cada plano gerado é adicionado ao cache de planos. As execuções subsequentes da mesma consulta poderão reutilizar esse plano pelo cache, o que elimina a necessidade de compilações adicionais.

Consultas com valores não parametrizados podem gerar sobrecarga de desempenho porque o plano de execução é recompilado toda vez que os valores não parametrizados são diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução. No entanto, esses planos ainda são adicionados separadamente ao cache de planos.

O processo de recompilação de planos de execução usa recursos do banco de dados, aumenta o tempo de duração da consulta e sobrecarrega o cache de planos. Esses eventos, por sua vez, fazem com que os planos sejam removido do cache. Esse comportamento do SQL Server pode ser alterado ao definir a opção de parametrização forçada no banco de dados.

Para ajudá-lo a estimar o impacto dessa recomendação, você receberá uma comparação entre o uso real da CPU e o uso projetado da CPU (como se a recomendação tivesse sido aplicada). Essa recomendação pode ajudá-lo a obter uma economia de CPU. Ela também pode ajudar a reduzir a duração da consulta e a sobrecarga para o cache de planos, o que significa que mais dos planos podem permanecer no cache e ser reutilizados. Você pode aplicar essa recomendação com rapidez selecionando o comando **Aplicar**.

Depois de aplicar essa recomendação, ele permite a parametrização forçada em minutos no banco de dados. Ele inicia o processo de monitoramento, que dura aproximadamente 24 horas. Após esse período, você pode ver o relatório de validação. Este relatório mostra o uso da CPU de seu banco de dados 24 horas antes e depois que a recomendação foi aplicada. O Assistente do Banco de Dados SQL tem um mecanismo de segurança que reverte a recomendação aplicada automaticamente se uma redução de desempenho seja detectada.

## <a name="fix-schema-issues-recommendations-preview"></a>Recomendações para corrigir problemas do esquema (versão prévia)

> [!IMPORTANT]
> A Microsoft está atualmente substituindo recomendações do tipo "Corrigir problema de esquema". Recomendamos que você use o [Intelligent Insights](sql-database-intelligent-insights.md) para monitorar seus problemas de desempenho do banco de dados, incluindo problemas de esquema que as recomendações de "Corrigir problemas do esquema" abordaram anteriormente.

As recomendações de **correção de problemas de esquema** aparecem quando o serviço do banco de dados SQL do Azure observa uma anomalia no número de erros SQL relacionados ao esquema que estão ocorrendo no banco de dados SQL. Essa recomendação normalmente aparece quando seu banco de dados encontra vários erros relacionados ao esquema (nome da coluna inválido, nome do objeto inválido e assim por diante) no período de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no SQL Server. Eles ocorrem quando a definição da consulta SQL e a definição do esquema do banco de dados não estão alinhadas. Por exemplo, uma das colunas esperadas pela consulta pode estar ausente na tabela de destino ou vice-versa.

A recomendação "corrigir problema do esquema" aparece quando o serviço do banco de dados SQL do Azure observa uma anomalia no número de erros SQL relacionados ao esquema que estão ocorrendo no banco de dados SQL. A tabela a seguir mostra os erros relacionados a problemas de esquema:

| Código do erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função '*' espera o parâmetro '*', que não foi fornecido. |
| 207 |Nome de coluna inválido '*'. |
| 208 |Nome de objeto inválido '*'. |
| 213 |O nome da coluna ou o número de valores fornecidos não corresponde à definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado '*'. |
| 8144 |Função ou procedimento * tem muitos argumentos especificados. |

## <a name="custom-applications"></a>Aplicativos personalizados

Os desenvolvedores podem considerar o desenvolvimento de aplicativos personalizados usando recomendações de desempenho para o banco de dados SQL do Azure. Todas as recomendações listadas no portal para um banco de dados podem ser acessadas por meio da API [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) .

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre ajuste automático de índices do banco de dados e planos de execução de consulta, consulte [Ajuste automático do banco de dados SQL do Azure](sql-database-automatic-tuning.md).
- Para mais informações sobre monitoramento automático do desempenho do banco de dados com diagnóstico automatizado e análise de causa raiz de problemas de desempenho, consulte [Intelligent Insights do SQL Azure](sql-database-intelligent-insights.md).
- Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais e visualizar o impacto no desempenho de suas principais consultas.
