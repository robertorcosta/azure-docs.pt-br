---
title: Alertas de configuração e notificações (portal Azure)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209547"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Crie alertas para bancos de dados Azure SQL e Azure Synapse Analytics usando o portal Azure

## <a name="overview"></a>Visão geral

Este artigo mostra como configurar alertas para bancos de dados únicos, agrupados e de data warehouse no Azure SQL Database e no Azure Synapse Analytics (antigo Azure SQL Data Warehouse) usando o portal Azure. Os alertas podem enviar a você um email ou chamar um webhook quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atinge o limite. Este artigo também fornece as práticas recomendadas para definir os períodos de alerta.

> [!IMPORTANT]
> Esse recurso ainda não está disponível na Instância Gerenciada. Como alternativa, você pode usar o SQL Agent para enviar alertas por email para algumas métricas com base em [Exibições de Gerenciamento Dinâmico](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores métricos** - O alerta é acionado quando o valor de uma métrica especificada cruza um limiar atribuído em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.
* **Eventos de registro de atividades** - Um alerta pode ser acionado em *todos os* eventos, ou, somente quando um determinado número de eventos ocorrer.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* Enviar notificações por email para o administrador e coadministradores de serviços
* Enviar um email para outros emails que você especificar.
* Chamar um webhook

Você pode configurar e obter informações sobre o uso de regras de alerta

* [Portal Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Powershell](../azure-monitor/platform/alerts-classic-portal.md)
* [interface de linha de comando (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o Portal do Azure

1. No [Portal](https://portal.azure.com/), localize o recurso no qual você está interessado em monitor e selecione-o.
2. Selecione **Alertas** na seção Monitoramento. O texto e o ícone podem variar um pouco para recursos diferentes.  

   ![Monitoramento](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selecione o botão **Nova regra de alerta** para abrir a página Criar **regra.**
  ![Criar regra](media/sql-database-insights-alerts-portal/create-rule.png)

4. Na seção **Condição,** clique em **Adicionar**.
  ![Definir condição](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na página **Configurar lógica de sinal,** selecione um sinal.
  ![Selecione](media/sql-database-insights-alerts-portal/select-signal.png)o sinal .
6. Depois de selecionar um sinal, como **porcentagem da CPU,** a página **Configurar lógica de sinal** é exibida.
  ![Configurar sinal lógico](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Nesta página, configure esse tipo de limiar, operador, tipo de agregação, valor limite, granularidade de agregação e frequência de avaliação. Em seguida, clique **em Feito**.
8. Na **regra Criar,** selecione um **grupo de ação** existente ou crie um novo grupo. Um grupo de ação permite definir a ação a ser tomada quando ocorre uma condição de alerta.
  ![Definir grupo de ação](media/sql-database-insights-alerts-portal/action-group.png)

9. Defina um nome para a regra, forneça uma descrição opcional, escolha um nível de gravidade para a regra, escolha se deve habilitar a regra na criação da regra e, em seguida, clique em **Criar alerta de regra** para criar o alerta de regra métrica.

Dentro de 10 minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
