---
title: Exibir as notificações de integridade do serviço usando o Portal do Azure
description: As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748643"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Exibir as notificações de integridade do serviço usando o Portal do Azure

As notificações de saúde dos serviços são publicadas pela infra-estrutura Do Zure no registro de atividades do [Azure](../azure-monitor/platform/platform-logs-overview.md).  As notificações contêm informações sobre os recursos sua assinatura. Dado o volume possivelmente grande de informações armazenadas no registro de atividades, há uma interface de usuário separada para facilitar a visualização e a configuração de alertas sobre notificações de saúde do serviço. 

As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Para obter mais informações sobre as diversas classes de notificações de saúde do serviço, consulte [os imóveis de notificações de saúde do Serviço](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Exibindo as notificações de integridade do serviço no Portal do Azure

1. No [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Captura de tela do menu do Portal do Azure, com a opção Monitor selecionada](./media/service-notifications/home-monitor.png)

    Este Azure Monitor reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades** .

1. Selecione **Alertas**.

    ![Captura do log de atividades do Monitor, com a opção Alertas selecionada](./media/service-notifications/service-health-summary.png)

1. Selecione **+Adicionar alerta do log de atividades** e configure um alerta para garantir que você receba notificações de serviço futuras. Para obter mais informações, consulte [Criar alertas do log de atividades em notificações de serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md).
