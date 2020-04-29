---
title: Alertas e notificações de instalação (portal do Azure)
description: Use o portal do Azure para criar alertas do Banco de Dados SQL, que podem disparar notificações ou automação quando as condições especificadas são atendidas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79209547"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Criar alertas para bancos de dados SQL do Azure e Azure Synapse Analytics usando portal do Azure

## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas para bancos de dados únicos, em pool e data warehouse no banco de dados SQL do Azure e no Azure Synapse Analytics (anteriormente, o Azure SQL Data Warehouse) usando o portal do Azure. Os alertas podem enviar a você um email ou chamar um webhook quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atinge o limite. Este artigo também fornece as práticas recomendadas para definir os períodos de alerta.

> [!IMPORTANT]
> Esse recurso ainda não está disponível na Instância Gerenciada. Como alternativa, você pode usar o SQL Agent para enviar alertas por email para algumas métricas com base em [Exibições de Gerenciamento Dinâmico](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores de métrica** -o alerta é disparado quando o valor de uma métrica especificada cruza um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.
* **Eventos do log de atividades** – um alerta pode disparar em *cada* evento ou somente quando ocorre um determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* Enviar notificações por email para o administrador e coadministradores de serviços
* Enviar um email para outros emails que você especificar.
* Chamar um webhook

Você pode configurar e obter informações sobre o uso de regras de alerta

* [Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [CLI (interface de linha de comando)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure

1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o.
2. Selecione **alertas** na seção monitoramento. O texto e o ícone podem variar um pouco para recursos diferentes.  

   ![Monitoramento](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selecione o botão **nova regra de alerta** para abrir a página **criar regra** .
  ![Criar regra](media/sql-database-insights-alerts-portal/create-rule.png)

4. Na seção **condição** , clique em **Adicionar**.
  ![Definir condição](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na página **Configurar lógica de sinal** , selecione um sinal.
  ![Selecione o](media/sql-database-insights-alerts-portal/select-signal.png)sinal.
6. Depois de selecionar um sinal, como **percentual de CPU**, a página **Configurar lógica de sinal** será exibida.
  ![Configurar sinal lógico](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Nessa página, configure esse tipo de limite, operador, tipo de agregação, valor de limite, granularidade de agregação e frequência de avaliação. Em seguida, clique em **concluído**.
8. Na **regra criar**, selecione um grupo de **ações** existente ou crie um novo grupo. Um grupo de ações permite que você defina a ação a ser executada quando ocorrer uma condição de alerta.
  ![Definir grupo de ação](media/sql-database-insights-alerts-portal/action-group.png)

9. Defina um nome para a regra, forneça uma descrição opcional, escolha um nível de severidade para a regra, escolha se deseja habilitar a regra na criação de regra e clique em **criar alerta de regra** para criar o alerta de regra de métrica.

Em 10 minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
