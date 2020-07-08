---
title: Análise de Desempenho de Consultas – Banco de Dados do Azure para MySQL
description: Este artigo descreve o recurso Análise de Desempenho de Consultas no Banco de Dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 51b478e5184c79e11d95ff004a652b2e5298558f
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402606"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Análise de Desempenho de Consultas no Banco de Dados do Azure para MySQL

**Aplica-se a:** Banco de Dados do Azure para MySQL 5.7, 8.0

A Análise de Desempenho de Consultas ajuda você a identificar rapidamente quais são suas consultas de execução mais longa, como elas mudam ao longo do tempo e quais esperas as estão afetando.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de execução prolongada

- Identificar consultas de execução mais longas nas últimas X horas
- Identificar as principais N consultas que estão aguardando recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Entender a natureza de espera de uma consulta
- Entender tendências sobre esperas de recurso e onde existe a contenção de recurso

## <a name="permissions"></a>Permissões

**Permissões do Proprietário** ou **Colaborador** necessárias para exibir o texto das consultas na Análise de Desempenho de Consultas **Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para a Análise de Desempenho de Consultas funcionar, os dados precisam existir no [Repositório de Consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibição de análises de desempenho

A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas.

Na página do portal do Banco de Dados do Azure para MySQL, selecione **Análise de Desempenho de Consultas** na seção **Desempenho Inteligente** da barra de menus.

### <a name="long-running-queries"></a>Consultas de execução prolongada

A guia **Consultas de execução prolongada** mostra as cinco principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Você pode ver mais consultas, selecionando no menu suspenso **Número de Consultas**. As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico. Como alternativa, use os ícones de ampliar e afastar para ver um período de tempo maior ou menor, respectivamente.

![Consultas de execução longa da Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Estatísticas de espera

> [!NOTE]
> As estatísticas de espera servem para solucionar problemas de desempenho da consulta. É recomendável que elas sejam ativadas apenas para fins de solução de problemas. <br>Se você receber a mensagem de erro no portal do Azure "*O problema encontrado para 'Microsoft.DBforMySQL'; não pode atender à solicitação. Se o problema continuar ou for inesperado, entre em contato com o suporte com essas informações.* " ao exibir as estatísticas de espera, use um período de tempo menor.

As estatísticas de espera fornecem uma exibição dos eventos de espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de evento de espera na [documentação do mecanismo MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecione a guia das **Estatísticas de Espera** guia para exibir as visualizações correspondentes em espera no servidor.

As consultas exibidas na visualização de estatísticas de espera são agrupadas pelas consultas que exibem as maiores esperas durante o intervalo de tempo especificado.

![Estatísticas de esperas de Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para MySQL.