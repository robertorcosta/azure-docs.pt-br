---
title: Configurar alertas de métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Este artigo descreve como configurar e acessar os alertas de métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5f45df16ac294f50e99cc7e05ab6eba43c0ae85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516013"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Usar o portal do Azure para configurar alertas em métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Este artigo mostra como configurar alertas do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure. Você pode receber um alerta com base em métricas de monitoramento para seus serviços do Azure.

Vamos configurar um alerta para disparar quando o valor de uma métrica especificada ultrapassar um limite. O alerta é disparado quando a condição é atendida pela primeira vez e continua a disparar posteriormente.

Você pode configurar um alerta para fazer as seguintes ações quando ele disparar:
* Envie notificações por email para o administrador de serviços e coadministradores.
* Enviar um email para outros emails que você especificar.
* Chamar um webhook.

Você pode configurar e obter informações sobre as regras de alerta usando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Monitor do Azure](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica no Portal do Azure
1. No [Portal do Azure](https://portal.azure.com/), selecione o servidor do Banco de Dados do Azure para PostgreSQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Alertas** como mostrado abaixo:

   ![Selecionar Regras de Alerta](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selecione **nova regra de alerta** (+ ícone).

4. A página **Criar regra** é aberta, conforme mostrado abaixo. Preencha as informações obrigatórias:

   ![Formulário Adicionar alerta de métrica](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Na seção **condição** , selecione **Adicionar**.

6. Selecione uma métrica da lista de sinais sobre a qual deseja ser alertado. Neste exemplo, selecione "Porcentagem de armazenamento".
   
   ![Selecionar métrica](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta:

    * **Operador** (ex. "Maior que")
    * **Valor de limite** (ex. 85 por cento)
    * Quantidade de **granularidade de agregação** de tempo que a regra de métrica deve ser satisfeita antes que o alerta seja disparado (por exemplo, "Nos últimos 30 minutos")
    * e **frequência de avaliação** (ex. "1 minuto")
   
   Selecione **Concluído** ao concluir.

   ![Selecionar métrica](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Dentro da seção **Grupos de Ações**, selecione **Criar Novo** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário "Adicionar grupo de ações" com um nome, o nome curto, a assinatura e o grupo de recursos.

    ![Grupo de ações](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Configure o tipo de ação **Email/SMS/Push/Voz**.
    
    Escolha "função de Azure Resource Manager de email" para enviar notificações para proprietários, colaboradores e leitores de assinatura.
   
    Selecione **OK** ao concluir.

    ![Grupo de ações](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Especifique um Nome da regra de alerta, uma Descrição e uma Gravidade.

    ![Grupo de ações](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selecione **Criar regra de alerta** para criar o alerta.

    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, você pode selecioná-lo e executar as seguintes ações:

* Exibir um grafo mostrando o limite de métrica e os valores reais do dia anterior relevante para este alerta.
* **Editar** ou **Excluir** a regra de alerta.
* **Desabilitar** ou **Habilitar** o alerta, se desejar interromper temporariamente ou retomar o recebimento de notificações.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Tenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
