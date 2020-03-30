---
title: Configurar fontes de dados do agente no Azure Monitor | Microsoft Docs
description: As fontes de dados definem os dados coletados pelo Azure Monitor de agentes e outras fontes conectadas.  Este artigo descreve o conceito de como o Azure Monitor usa fontes de dados, explica os detalhes de como configurá-las e fornece um resumo das diferentes fontes de dados disponíveis.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249094"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Fontes de dados do agente no Azure Monitor
Os dados coletados pelo Azure Monitor dos agentes são definidos pelas fontes de dados configuradas.  Os dados dos agentes são armazenados como [dados de log](data-platform-logs.md) com um conjunto de registros.  Cada fonte de dados cria registros de um determinado tipo com cada tipo de tendo seu próprio conjunto de propriedades.

![Coleta de dados de log](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Resumo das fontes de dados
A tabela a seguir lista as fontes de dados de agente atualmente disponíveis no Azure Monitor.  Cada uma tem um link para um artigo à parte que fornece detalhes para aquela fonte de dados.   Fornece também informações sobre o método e a frequência de coleta. 


| Fonte de dados | Plataforma | Agente de análise de log | Agente do Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Logs personalizados](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | na chegada |
| [Logs personalizados](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | na chegada |
| [Registros IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |depende da configuração de rolagem de arquivo de log |
| [Contadores de desempenho](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Contadores de desempenho](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento do Azure: 10 minutos; do agente: na chegada |
| [Registros de eventos do Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |


## <a name="configuring-data-sources"></a>Configurando fontes de dados
Configure fontes de dados no menu **Dados** nas **Configurações Avançadas** do workspace.  Todas as configurações são entregues a todas as fontes conectadas em seu workspace.  No momento, você não pode excluir nenhum agente dessa configuração.

![Configurar eventos do Windows](media/agent-data-sources/configure-events.png)

1. No portal do Azure, escolha **workspaces do Log Analytics** > seu workspace > **Configurações Avançadas**.
2. Selecione **Dados**.
3. Clique na fonte de dados que você deseja configurar.
4. Siga o link para a documentação para cada fonte de dados na tabela acima para obter detalhes sobre sua configuração.


## <a name="data-collection"></a>Coleta de dados
As configurações de fonte de dados são entregues aos agentes que estão diretamente conectados ao Azure Monitor em alguns minutos.  Os dados especificados são coletados do agente e entregues diretamente para o Azure Monitor em intervalos específicos a cada fonte de dados.  Consulte a documentação para cada fonte de dados para encontrar essas especificações.

Para agentes do System Center Operations Manager em um grupo de gerenciamento conectado, as configurações de fonte de dados são convertidas em pacotes de gerenciamento e entregues ao grupo de gerenciamento a cada cinco minutos por padrão.  O agente baixa o pacote de gerenciamento como qualquer outro e coleta os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gerenciamento que os encaminhará para o Azure Monitor ou o agente enviará os dados para o Azure Monitor sem passar pelo servidor de gerenciamento. Confira [Detalhes da coleta de dados para soluções de monitoramento no Azure](../insights/solutions-inventory.md) para obter detalhes.  Leia mais sobre os detalhes da conexão do Operations Manager e do Azure Monitor e a modificação da frequência de entrega da configuração em [Configurar a integração com o System Center Operations Manager](om-agents.md).

Se o agente não puder se conectar ao Azure Monitor nem ao Operations Manager, ele continuará coletando dados que serão fornecidos quando ele estabelecer uma conexão.  Dados podem ser perdidos se a quantidade de dados atingir o tamanho máximo do cache do cliente, ou se o agente não for capaz de estabelecer uma conexão dentro de 24 horas.

## <a name="log-records"></a>Registros de log
Todos os dados de log coletados pelo Azure Monitor são armazenados no workspace como registros.  Registros coletados por diferentes fontes de dados terão seu próprio conjunto de propriedades e serão identificados por sua propriedade **Type** .  Consulte a documentação para cada fonte de dados e a solução para obter detalhes sobre cada tipo de registro.

## <a name="next-steps"></a>Próximas etapas
* Conheça as [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidades ao Azure Monitor e também coletam dados no workspace.
* Saiba mais sobre as [consultas de log](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções de monitoramento.  
* Configure [alertas](alerts-overview.md) para notificá-lo de forma proativa sobre dados críticos coletados de fontes de dados e soluções de monitoramento.
