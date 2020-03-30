---
title: Exibir atividade do provedor de serviços
description: Os clientes podem visualizar atividades registradas para ver as ações realizadas pelos provedores de serviços através do gerenciamento de recursos delegado do Azure.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649629"
---
# <a name="view-service-provider-activity"></a>Exibir atividade do provedor de serviços

Os clientes que delegaram assinaturas para o gerenciamento de recursos delegados do Azure podem visualizar os dados [do registro de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) para ver todas as ações tomadas. Isso dá aos clientes total visibilidade sobre as operações que os provedores de serviços estão realizando através do gerenciamento de recursos delegados do Azure, juntamente com as operações feitas pelos usuários dentro do próprio inquilino do Azure Active Directory (Azure AD).

> [!TIP]
> Também fornecemos uma definição de política incorporada da Política Azure para auditar a delegação de escopos a um inquilino gerenciador. Para obter mais informações, consulte [as delegações de Auditoria em seu ambiente](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Exibir dados do registro de atividades

Você pode visualizar o registro de [atividades](../../azure-monitor/platform/activity-log-view.md) do menu **Monitor** no portal Azure. Para limitar os resultados a uma assinatura específica, use os filtros para selecionar uma assinatura específica. Você também pode [visualizar e recuperar eventos de registro de atividades](../../azure-monitor/platform/activity-log-view.md) de forma programática.

> [!NOTE]
> Os usuários do inquilino de um provedor de serviços podem visualizar os resultados do registro de atividades para uma assinatura delegada em um inquilino do cliente se eles forem concedidos a função [Reader](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclui acesso ao Leitor) quando essa assinatura foi a bordo para o gerenciamento de recursos delegado do Azure.

No registro de atividades, você verá o nome da operação e seu status, juntamente com a data e a hora em que foi realizada. O **Evento iniciado pela** coluna mostra qual usuário realizou a operação, seja um usuário do inquilino de um provedor de serviços atuando através do gerenciamento de recursos delegados do Azure, ou um usuário no próprio inquilino do cliente. Observe que o nome do usuário é mostrado, em vez do inquilino ou da função que o usuário foi atribuído para essa assinatura.

A atividade registrada está disponível no portal Azure nos últimos 90 dias. Para saber como armazenar esses dados por mais de 90 dias, consulte [Coletar e analisar os registros de atividades do Azure no espaço de trabalho do Log Analytics](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Os usuários do provedor de serviços aparecem no registro de atividades, mas esses usuários e suas atribuições de função não são mostrados no **Access Control (IAM)** ou ao recuperar informações de atribuição de função via APIs.

## <a name="set-alerts-for-critical-operations"></a>Defina alertas para operações críticas

Para ficar atento às operações críticas que os provedores de serviços (ou usuários do seu próprio inquilino) estão realizando, recomendamos a criação de [alertas de registro de atividades](../../azure-monitor/platform/activity-log-alerts.md). Por exemplo, você pode querer acompanhar todas as ações administrativas de uma assinatura ou ser notificado quando qualquer máquina virtual em um determinado grupo de recursos for excluída. Quando você cria alertas, eles incluirão ações realizadas pelos usuários no próprio inquilino do cliente, bem como em quaisquer inquilinos gerenciadores.

Para obter mais informações, consulte [Criar e gerenciar alertas de registro de atividades](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Criar consultas de log

Você pode criar consultas para analisar sua atividade registrada ou se concentrar em itens específicos. Por exemplo, talvez uma auditoria exija que você informe todas as ações de nível administrativo realizadas em uma assinatura. Você pode criar uma consulta para filtrar apenas essas ações e classificar os resultados por usuário, data ou outro valor.

Para obter mais informações, consulte [Visão geral das consultas de log no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Azure Monitor](../../azure-monitor/index.yml).
- Saiba como [visualizar e gerenciar ofertas de prestadores](view-manage-service-providers.md) de serviços no portal Azure.