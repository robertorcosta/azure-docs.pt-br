---
title: Renovar atribuições de função de recurso do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como estender ou renovar atribuições de função de recurso do Azure no PIM (Azure AD Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022910"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Estender ou renovar as atribuições de função de recurso do Azure no Gerenciamento de Identidade Privilegiada

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) fornece controles para gerenciar o ciclo de vida de acesso e atribuição dos recursos do Azure. Os administradores podem atribuir funções usando propriedades de início e término de data-hora. Quando o fim da atribuição se aproxima, o Gerenciamento de Identidade Privilegiada envia notificações de e-mail para os usuários ou grupos afetados. Ele também envia notificações por e-mail aos administradores do recurso para garantir que o acesso apropriado seja mantido. As atribuições podem ser renovadas e permanecer visíveis em um estado expirado por até 30 dias, mesmo que o acesso não seja estendido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Somente os administradores do recurso podem estender ou renovar atribuições de função. O usuário ou grupo afetado pode solicitar a prorrogação de funções que estão prestes a expirar e solicitar a renovação de funções que já estão expiradas.

## <a name="when-are-notifications-sent"></a>Quando as notificações são enviadas?

O Gerenciamento de Identidade Privilegiada envia notificações de e-mail aos administradores e usuários ou grupos de funções afetadas que estão expirando dentro de 14 dias e um dia antes do vencimento. Ele envia um e-mail adicional quando uma atribuição expira oficialmente.

Os administradores recebem notificações quando um usuário ou grupo atribuiu uma função expirando ou expirada solicita ções de função para estender ou renovar. Quando um administrador específico resolve a solicitação, todos os outros administradores são notificados da decisão de resolução (aprovada ou recusada). Em seguida, o usuário ou grupo solicitante é notificado da decisão.

## <a name="extend-role-assignments"></a>Estender atribuições de função

As etapas a seguir descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de função.

### <a name="self-extend-expiring-assignments"></a>Atribuições de expiração de auto-extensão

Os usuários ou grupos atribuídos a uma função podem estender as atribuições de expiração diretamente da guia **Elegível** ou **Ativo** na página **Minhas funções** de um recurso e a partir do nível superior Minha página de **funções** do portal Desviável Gerenciamento de Identidade. Os usuários ou grupos podem solicitar a prorrogação de funções elegíveis e ativas (atribuídas) que expiram nos próximos 14 dias.

![Recursos do Azure - Minha página de funções listando funções elegíveis com uma coluna Ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a data e a hora de término da atribuição estiverem dentro de 14 dias, o botão para ** Estender ** se tornará um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual seja 27 de março.

![Coluna de ação com links para Ativar ou Estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar uma extensão dessa atribuição de função, selecione ** Estender ** para abrir o formulário de solicitação.

![Estender painel de atribuição de função com uma caixa Razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para visualizar informações sobre a atribuição original, expanda ** Detalhes da atribuição **. Digite uma razão para a solicitação de extensão e, em seguida, selecione ** Estender **.

>[!NOTE]
>Recomendamos incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser concedida (se você tiver essa informação).

![Estender painel de atribuição de função com detalhes de atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Em questão de momentos, os administradores de recursos recebem uma notificação por e-mail solicitando que revisem a solicitação de extensão. Se uma solicitação de prorrogação já tiver sido enviada, uma notificação do Azure será exibida no portal.

![Notificação explicando que já existe uma extensão de atribuição de função pendente existente](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá para a página **solicitações pendentes** para visualizar o status de sua solicitação ou cancelá-la.

![Recursos do Azure - Página de solicitações pendentes listando qualquer pendente solicitado e um link para Cancelar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Prorrogação aprovada pelo admin

Quando um usuário ou grupo envia uma solicitação para estender uma atribuição de função, os administradores de recursos recebem uma notificação por e-mail que contém os detalhes da atribuição original e o motivo da solicitação. A notificação inclui um link direto com a solicitação para o administrador aprovar ou negar.

Além de usar seguindo o link do e-mail, os administradores podem aprovar ou negar solicitações indo ao portal de administração de gerenciamento de identidade privilegiada e selecionando **solicitações de aprovação** no painel esquerdo.

![Recursos do Azure - Aprovar solicitações de solicitações de listagem de pedidos e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **Aprovar** ou **Negar,** os detalhes da solicitação são mostrados, juntamente com um campo para fornecer uma justificativa comercial para os registros de auditoria.

![Aprovar solicitação de atribuição de função com motivo solicitante, tipo de atribuição, tempo de início, tempo de término e razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para estender a atribuição de função, os administradores de recursos podem escolher uma nova data de início, uma data de término e um tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de ** Qualificado ** para ** Ativo **. Isso significa que eles podem fornecer acesso ao solicitante sem precisar que eles sejam ativados.

### <a name="admin-initiated-extension"></a>Admin iniciou prorrogação

Se um usuário atribuído a uma função não solicitar uma extensão para a atribuição da função, um administrador pode estender uma atribuição em nome do usuário. Prorrogações administrativas da atribuição de função não exigem aprovação, mas as notificações são enviadas a todos os outros administradores após a prorrogação da função.

Para estender uma atribuição de função, navegue até a função de recurso ou exibição de atribuição no Gerenciamento de Identidade Privilegiada. Encontre a tarefa que requer uma prorrogação. Em seguida, selecione **estender** na coluna ação.

![Recursos do Azure - página de atribuições listando funções elegíveis com links para estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de função

Embora seja conceitualmente semelhante ao processo para solicitar uma extensão, o processo para renovar uma atribuição de função expirada é diferente. Usando as seguintes etapas, as atribuições e os administradores podem renovar o acesso a funções expiradas quando necessário.

### <a name="self-renew"></a>Auto-renovação

Os usuários que não podem mais acessar recursos podem acessar até 30 dias do histórico de atribuição expirado. Para fazer isso, navegue até **funções Meus** no painel esquerdo e, em seguida, selecione o **expirado funções** guia na seção de funções de recurso do Azure.

![Minha página de funções - Guia de funções expiradas](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de papéis mostrada é padronizada para ** Funções elegíveis **. Use o menu suspenso para alternar entre as funções atribuídas Elegíveis e Ativas.

Para solicitar a renovação de qualquer uma das atribuições de função na lista, selecione a ação ** Renovar **. Em seguida, forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional ou uma justificativa de negócios que possa ajudar o administrador de recursos a decidir aprovar ou negar.

![Renovar painel de atribuição de papéis mostrando caixa razão](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Depois que a solicitação é enviada, os administradores de recursos são notificados sobre uma solicitação pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovação pelo administrador

Os administradores de recursos podem acessar a solicitação de renovação a partir do link na notificação de e-mail ou acessando o Gerenciamento de Identidade Privilegiada do portal Azure e selecionando solicitações de **Aprovação** do painel esquerdo.

![Recursos do Azure - Aprovar solicitações de solicitações de listagem de pedidos e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador **seleciona Aprovar** ou **Negar,** os detalhes da solicitação são mostrados juntamente com um campo para fornecer uma justificativa comercial para os registros de auditoria.

![Aprovar solicitação de atribuição de função com motivo solicitante, tipo de atribuição, tempo de início, tempo de término e razão](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para renovar a atribuição de função, os administradores de recursos devem inserir uma nova data de início, uma data de término e um tipo de atribuição.

### <a name="admin-renew"></a>Renovação pelo administrador

Os administradores de recursos podem renovar atribuições de função expiradas da guia ** Members ** no menu de navegação à esquerda de um recurso. Eles também podem renovar atribuições de função expiradas de dentro do **expirado** guia funções de uma função de recurso.

Para visualizar uma lista de todas as atribuições de funções expiradas, na tela ** Members **, selecione ** Expired roles **.

![Recursos do Azure - Página de membros listando funções expiradas com links para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar pedidos de funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-approval-workflow.md)
- [Configure as configurações de função de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
