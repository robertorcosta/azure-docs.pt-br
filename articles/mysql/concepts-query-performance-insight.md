---
title: Insight de desempenho de consulta - Banco de dados Azure para MySQL
description: Este artigo descreve o recurso Query Performance Insight no Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a924f51fac6d43ae4a4530ac4f61f2e8b1f4e8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537050"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Análise de Desempenho de Consultas no Banco de Dados do Azure para MySQL

**Aplica-se a:** Banco de dados Azure para MySQL 5.7

A Análise de Desempenho de Consultas ajuda você a identificar rapidamente quais são suas consultas de execução mais longa, como elas mudam ao longo do tempo e quais esperas as estão afetando.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de execução prolongada

- Identificar consultas de execução mais longas nas últimas X horas
- Identificar as principais N consultas que estão aguardando recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Entender a natureza de espera para uma consulta
- Entendendo tendências de espera de recursos e onde existe contenção de recursos

## <a name="permissions"></a>Permissões

**Permissões do Proprietário** ou **Colaborador** necessárias para exibir o texto das consultas na Análise de Desempenho de Consultas **Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para a Análise de Desempenho de Consultas funcionar, os dados precisam existir no [Repositório de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibição de análises de desempenho

A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas.

Na página do portal do seu banco de dados Azure para o servidor MySQL, selecione **O Insight de Desempenho de Consulta** na seção Desempenho **Inteligente** da barra de menus.

### <a name="long-running-queries"></a>Consultas de execução prolongada

A **guia De consultas em execução** longa mostra as 5 principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Você pode exibir mais consultas selecionando a **retirada do Número de Consultas.** As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico. Alternativamente, use os ícones de zoom in e out para visualizar um período de tempo menor ou maior, respectivamente.

![Consultas de execução longa da Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Estatísticas de espera

> [!NOTE]
> As estatísticas de espera destinam-se a solucionar problemas de desempenho de consultas. Recomenda-se que seja ligado apenas para fins de solução de problemas. <br>Se você receber a mensagem de erro no portal Azure "*O problema encontrado para 'Microsoft.DBforMySQL'; não pode atender ao pedido. Se esse problema continuar ou for inesperado, entre em contato com essas informações.*" enquanto visualiza as estatísticas de espera, use um período de tempo menor.

As estatísticas de wait fornecem uma visão dos eventos de espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de eventos de espera na documentação do [mecanismo MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecione a guia das **Estatísticas de Espera** guia para exibir as visualizações correspondentes em espera no servidor.

As consultas exibidas na exibição de estatísticas de espera são agrupadas pelas consultas que exibem as maiores esperas durante o intervalo de tempo especificado.

![Query Performance Insight aguarda estatísticas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados Azure para MySQL.