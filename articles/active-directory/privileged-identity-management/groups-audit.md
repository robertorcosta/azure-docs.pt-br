---
title: Exibir relatório de auditoria para atribuições de grupo de acesso privilegiado no Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria para atribuições de grupo de acesso privilegiado no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9bbc90776ca007b84d5f67c50f8550ee9c881c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506037"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Histórico de atividades de auditoria para atribuições de grupo de acesso privilegiado (versão prévia) no Privileged Identity Management

Com o PIM (Privileged Identity Management), você pode exibir a atividade, as ativações e o histórico de auditoria para membros e proprietários do grupo de acesso privilegiado do Azure em sua organização do Azure Active Directory (Azure AD).

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

Siga estas etapas para exibir o histórico de auditoria para grupos de acesso privilegiado.

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A **auditoria de recursos** fornece uma exibição de todas as atividades associadas aos seus grupos de acesso privilegiado.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **acesso privilegiado (versão prévia)**.

1. Em **atividade**, selecione **recurso auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **acesso privilegiado (versão prévia)**.

1. Selecione o membro ou grupo para o qual você deseja exibir o histórico de auditoria.

1. Selecione **minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir Associação de grupo e propriedade no Privileged Identity Management](groups-assign-member-owner.md)
- [Aprovar ou negar solicitações de grupos de acesso privilegiado no PIM](groups-approval-workflow.md)
- [Exibir histórico de auditoria para funções do Azure AD no PIM](groups-audit.md)
