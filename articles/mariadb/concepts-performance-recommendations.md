---
title: Recomendações de desempenho - Banco de Dados Azure para MariaDB
description: Este artigo descreve o recurso de recomendação de desempenho no Banco de Dados Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528091"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Recomendações de desempenho no Banco de Dados do Azure para MariaDB

**Aplica-se a:** Banco de dados Azure para MariaDB 10.2

O recurso Recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise analisa várias características do banco de dados, incluindo esquema. Habilite [a Loja de Consulta](concepts-query-store.md) em seu servidor para utilizar totalmente o recurso Recomendações de desempenho. Se o esquema de desempenho estiver DESLIGADO, ligar a Query Store habilita performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para o recurso. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações.

## <a name="permissions"></a>Permissões

**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho

O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **as recomendações** de desempenho da seção Desempenho **Inteligente** da barra de menus na página do portal Azure para o servidor MariaDB.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **Analisar** e escolher um banco de dados, que iniciará a análise. Dependendo da sua carga de trabalho, a análise pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. A análise realiza um exame profundo do seu banco de dados. Recomendamos que você realize análises durante os períodos fora do pico.

A janela **Recomendações** mostrará uma lista de recomendações se alguma foi encontrada e o ID de consulta relacionado que gerou essa recomendação. Com o ID de consulta, você pode usar a visualização [mysql.query_store](concepts-query-store.md#mysqlquery_store) para saber mais sobre a consulta.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o a partir de seu cliente de escolha. Lembre-se de testar e monitorar para avaliar a recomendação.

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, apenas as recomendações *de Create Index* são suportadas.

### <a name="create-index-recommendations"></a>Criar recomendações de índice

*As* recomendações do Create Index sugerem novos índices para acelerar as consultas mais freqüentes ou demoradas na carga de trabalho. Este tipo de recomendação requer que a [Loja de Consulta](concepts-query-store.md) seja ativada. O Query Store coleta informações de consulta e fornece as estatísticas detalhadas de tempo de execução e frequência da consulta que a análise usa para fazer a recomendação.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [monitoramento e sintonia](concepts-monitoring.md) no Banco de Dados Azure para MariaDB.