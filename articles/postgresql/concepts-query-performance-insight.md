---
title: Análise de Desempenho de Consultas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso Análise de Desempenho de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768377"
---
# <a name="query-performance-insight"></a>Análise de Desempenho de Consultas 

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

A Análise de Desempenho de Consultas ajuda você a identificar rapidamente quais são suas consultas de execução mais longa, como elas mudam ao longo do tempo e quais esperas as estão afetando.

## <a name="permissions"></a>Permissões
**Permissões do Proprietário** ou **Colaborador** necessárias para exibir o texto das consultas na Análise de Desempenho de Consultas **Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para a Análise de Desempenho de Consultas funcionar, os dados precisam existir no [Repositório de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibição de análises de desempenho
A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas. 

Na página do portal do servidor do banco de dados do Azure para PostgreSQL, selecione análise de **desempenho de consultas** na seção **desempenho inteligente** da barra de menus.

![Consultas de execução longa da Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

A guia **consultas de longa execução** mostra as cinco principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Você pode exibir mais consultas selecionando na lista suspensa **número de consultas** . As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico. Como alternativa, use os ícones de ampliar e afastar para exibir um período maior ou menor, respectivamente.

A tabela abaixo do gráfico contém mais detalhes sobre as consultas de execução longa na janela de tempo.

Selecione a guia das **Estatísticas de Espera** guia para exibir as visualizações correspondentes em espera no servidor.

![Estatísticas de esperas de Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Considerações
* Análise de Desempenho de Consultas não está disponível para [réplicas de leitura](concepts-read-replicas.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.


