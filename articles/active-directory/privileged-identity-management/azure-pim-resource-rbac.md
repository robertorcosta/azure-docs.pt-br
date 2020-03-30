---
title: Ver relatório de auditoria para funções de recursos do Azure no Privd Identity Management (PIM) - Azure AD | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329555"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Exibir histórico de atividades e auditoria susta is

Com o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), você pode exibir a atividade, as ativações e o histórico de auditoria para funções de recursos do Azure em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Qualquer recurso dentro do portal Azure que aproveite a funcionalidade de controle de acesso baseada em função do Azure pode tirar proveito dos recursos de segurança e gerenciamento do ciclo de vida no Gerenciamento de Identidade Privilegiada.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa [o gerenciamento de recursos delegado do Azure,](../../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="view-activity-and-activations"></a>Exibir a atividade e as ativações

Para ver as ações que um usuário específico realizou em vários recursos, você pode exibir a atividade do recurso do Azure associada a um período de ativação especificado.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o seu objetivo de visualizar atividades e ativações.

1. Selecionar **Funções** ou **Membros**.

1. Selecione um usuário.

    Você vê um resumo das ações do usuário nos recursos do Azure por data. Ele também mostra as ativações de função recentes nesse mesmo período.

    ![Detalhes do usuário com resumo de atividades de recursos e ativações de função](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selecione uma ativação de função específica para ver detalhes e atividades correspondentes de recursos do Azure que ocorreram enquanto esse usuário estava ativo.

    [![Ativação da função selecionada e detalhes da atividade](media/azure-pim-resource-rbac/export-membership.png "Ativação da função selecionada e detalhes da atividade")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Exportar as atribuições de função com filhos

Talvez você tenha um requisito de conformidade no qual precisa fornecer uma lista completa de atribuições de função para auditores. O Gerenciamento de Identidade Privilegiado permite que você consulta as atribuições de função em um recurso específico, que inclui atribuições de função para todos os recursos dos filhos. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico. Usando o Gerenciamento de Identidade Privilegiada, você pode consultar todas as atribuições de função ativas e elegíveis em uma assinatura, incluindo atribuições de função para todos os grupos de recursos e recursos.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o qual deseja exportar atribuições de função, como uma assinatura.

1. Selecione **Membros**.

1. Selecione **Exportar** para abrir o painel de adesão exportação.

    [![Painel de adesão de exportação para exportar todos os membros](media/azure-pim-resource-rbac/export-membership.png "Exportar página de adesão para exportar todos os membros")](media/azure-pim-resource-rbac/export-membership.png)

1. Selecione **Exportar todos os membros** para exportar todas as atribuições de função em um arquivo CSV.

    ![Atribuições de função exportadas no arquivo CSV como exibição no Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades de função para um recurso.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o seu objetivo de visualizar o histórico de auditoria.

1. Selecione **auditoria de recursos**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    [![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lista de auditoria de recursos com filtros")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Em **Tipo de auditoria**, selecione **Ativar (Atribuído + Ativado)**.

    [![Lista de auditoria de recursos filtrada por Ativar lista](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lista de auditoria de recursos filtrada pelo Activate")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![de auditoria tipo de auditoria Que é filtrada pelo tipo de auditoria Ativar](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Em **Ação**, clique em **(atividade)** de um usuário para ver os detalhes da atividade desse usuário em recursos do Azure.

    ![Detalhes da atividade do usuário para uma ação específica](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o seu objetivo de visualizar o histórico de auditoria.

1. Selecione **Minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    [![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png "Lista de auditoria para o usuário atual")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> O acesso ao histórico de auditoria requer uma função de administrador global ou de um administrador de funções privilegiadas.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obter razão, aprovador e número de bilhete para eventos de aprovação

1. Faça login no [portal Azure](https://aad.portal.azure.com) com permissões de função de administrador de funções privilegiadas e abra o Azure AD.
1. Selecione **registros de auditoria**.
1. Use o filtro **Serviço** para exibir apenas eventos de auditoria para o serviço de gerenciamento de identidade privilegiado. Na página de registros de **auditoria,** você pode:

    - Veja o motivo de um evento de auditoria na coluna **Razão status.**
    - Consulte o aprovador na coluna **Iniciado por (ator)** para o evento "adicionar membro à solicitação de papel aprovado".

    [![Filtrar o registro de auditoria para o serviço PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrar o registro de auditoria para o serviço PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selecione um evento de registro de auditoria para ver o número do bilhete na guia **Atividade** do painel **Detalhes.**
  
    [![Verifique o número do ingresso para o evento de auditoria](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Confira o número do ingresso para o evento de auditoria")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Você pode visualizar o solicitante (pessoa ativando a função) na guia **'''Objetivos'** do painel **Detalhes** para um evento de auditoria. Existem três tipos de destino para funções de recursos do Azure:

    - O papel (**Tipo** = Papel)
    - O solicitante **(Tipo** = Outro)
    - O aprovador (**Tipo** = Usuário)

    [![Verifique o tipo de destino](media/azure-pim-resource-rbac/audit-event-target-type.png "Verifique o tipo de destino")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Normalmente, o evento de log imediatamente acima do evento de aprovação é um evento para "Adicionar membro ao papel concluído" onde o **Iniciado por (ator)** é o solicitante. Na maioria dos casos, você não precisará encontrar o solicitante no pedido de aprovação de uma perspectiva de auditoria.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar pedidos de funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
- [Veja o histórico de auditoria das funções do Azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-use-audit-log.md)
