---
title: Recomendações de desempenho - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve o recurso de recomendação de desempenho no Banco de Dados Azure para PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768462"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Recomendações de desempenho no banco de dados Azure para PostgreSQL - Servidor Único

**Aplica-se a:** Banco de dados Azure para PostgreSQL - Single Server versões 9.6, 10, 11

O recurso Recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizadas para um melhor desempenho. Para produzir as recomendações, a análise analisa várias características do banco de dados, incluindo esquema. Habilite [a Loja de Consulta](concepts-query-store.md) em seu servidor para utilizar totalmente o recurso Recomendações de desempenho. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="performance-recommendations"></a>Recomendações do desempenho
O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

Abra **as recomendações** de desempenho da seção Desempenho **Inteligente** da barra de menus na página do portal Azure para o servidor PostgreSQL.

![Página das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **Analisar** e escolher um banco de dados, que iniciará a análise. Dependendo da sua carga de trabalho, a análise pode levar vários minutos para ser concluída. Quando a análise for concluída, haverá uma notificação no portal. A análise realiza um exame profundo do seu banco de dados. Recomendamos que você realize análises durante os períodos fora do pico. 

A janela **Recomendações** mostrará uma lista de recomendações se alguma for encontrada.

![Nova página de recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o a partir de seu cliente de escolha. Lembre-se de testar e monitorar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, dois tipos de recomendações são suportadas: *Create Index* e *Drop Index*.

### <a name="create-index-recommendations"></a>Criar recomendações de índice
*As* recomendações do Create Index sugerem novos índices para acelerar as consultas mais freqüentes ou demoradas na carga de trabalho. Este tipo de recomendação requer que a [Loja de Consulta](concepts-query-store.md) seja ativada. O Query Store coleta informações de consulta e fornece as estatísticas detalhadas de tempo de execução e frequência da consulta que a análise usa para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
Além de detectar índices ausentes, o Banco de Dados Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice é raramente usado ou redundante, o analisador recomenda deixá-lo cair.

## <a name="considerations"></a>Considerações
* Recomendações de desempenho não estão disponíveis para [réplicas de leitura](concepts-read-replicas.md).
## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.

