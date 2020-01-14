---
title: Exibir relatório de auditoria para funções de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 905acd206ba574e092f41707c9a5625bcaed7f8d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932370"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Exibir a atividade e o histórico de auditoria das funções de recurso do Azure no Privileged Identity Management

Com o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), você pode exibir a atividade, as ativações e o histórico de auditoria para funções de recursos do Azure em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Qualquer recurso dentro do portal do Azure que aproveita a funcionalidade RBAC (controle de acesso baseado em função) do Azure pode aproveitar os recursos de segurança e gerenciamento do ciclo de vida no Privileged Identity Management.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="view-activity-and-activations"></a>Exibir a atividade e as ativações

Para ver as ações que um usuário específico realizou em vários recursos, você pode exibir a atividade do recurso do Azure associada a um período de ativação especificado.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exibir a atividade e as ativações.

1. Clique em **Funções** ou **Membros**.

1. Clique em um usuário.

    Você verá uma exibição gráfica das ações do usuário em recursos do Azure por data. Ele também mostra as ativações de função recentes nesse mesmo período.

    ![Detalhes do usuário com Resumo de atividade de recursos e ativações de função](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Clique em uma ativação de função específica para ver detalhes e a atividade de recurso do Azure correspondente que ocorreu enquanto o usuário estava ativo.

    ![Ativação de função selecionada e detalhes da atividade exibidos por data](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportar as atribuições de função com filhos

Talvez você tenha um requisito de conformidade no qual precisa fornecer uma lista completa de atribuições de função para auditores. Privileged Identity Management permite consultar atribuições de função em um recurso específico, que inclui atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico. Usando Privileged Identity Management, você pode consultar todas as atribuições de função ativas e qualificadas em uma assinatura, incluindo atribuições de função para todos os grupos de recursos e recursos.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exportar as atribuições de função, como uma assinatura.

1. Clique em **Membros**.

1. Clique em **Exportar** para abrir o painel Exportar associação.

    ![Exportar o painel de associação para exportar todos os membros](media/azure-pim-resource-rbac/export-membership.png)

1. Clique em **Exportar todos os membros** para exportar todas as atribuições de função em um arquivo CSV.

    ![Atribuições de função exportadas no arquivo CSV como exibidas no Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades de função para um recurso.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exibir o histórico de auditoria.

1. Clique em **Auditoria de recursos**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Em **Tipo de auditoria**, selecione **Ativar (Atribuído + Ativado)** .

    ![Lista de auditoria de recursos filtrada por ativar tipo de auditoria](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Em **Ação**, clique em **(atividade)** de um usuário para ver os detalhes da atividade desse usuário em recursos do Azure.

    ![Detalhes da atividade do usuário para uma ação específica](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exibir o histórico de auditoria.

1. Clique em **Minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Próximos passos

- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Exibir histórico de auditoria para funções do Azure AD no Privileged Identity Management](pim-how-to-use-audit-log.md)
