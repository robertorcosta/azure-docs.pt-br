---
title: Adicione uma assinatura azure existente ao seu inquilino - Azure AD
description: Instruções sobre como adicionar uma assinatura do Azure existente ao seu locatário do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457919"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory

Uma assinatura do Azure tem uma relação de confiança com o Azure Active Directory (Azure AD). Uma assinatura confia no Azure AD para autenticar usuários, serviços e dispositivos.

Várias assinaturas podem confiar no mesmo diretório Azure AD. Cada assinatura só pode confiar em um único diretório.

Se a sua assinatura expirar, você perderá o acesso a todos os outros recursos associados à assinatura. No entanto, o diretório Azure AD permanece no Azure. Você pode associar e gerenciar o diretório usando uma assinatura diferente do Azure.

Todos os seus usuários têm um único *diretório doméstico* para autenticação. Seus usuários também podem ser convidados em outros diretórios. Você pode ver os diretórios inicial e convidado de cada usuário no Azure AD.

> [!Important]
> Quando você associa uma assinatura a um diretório diferente, os usuários que têm funções atribuídas usando [o RBAC (Role-Based Access Control, controle de acesso baseado em funções)](../../role-based-access-control/role-assignments-portal.md) perdem seu acesso. Os administradores de assinatura clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
>
> As Atribuições de Política também são removidas de uma assinatura quando ela é associada a um diretório diferente.
>
> Mover seu cluster Azure Kubernetes Service (AKS) para uma assinatura diferente ou mover a assinatura de dono de cluster para um novo inquilino, faz com que o cluster perca a funcionalidade devido às atribuições de função perdidas e aos direitos do diretor de serviço. Para obter mais informações sobre a AKS, consulte [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Antes de começar

Antes de associar ou adicionar sua assinatura, faça as seguintes tarefas:

- Revise a seguinte lista de alterações e como você pode ser afetado:

  - Os usuários que foram designados funções usando o RBAC perderão seu acesso
  - Administrador de serviços e co-administradores perderão o acesso
  - Se você tiver algum cofre chave, eles estarão inacessíveis e você terá que corrigi-los após a associação
  - Se você tiver alguma identidade gerenciada para recursos como Máquinas Virtuais ou Aplicativos lógicos, você deve reatiminá-las ou recriá-las após a associação
  - Se você tiver um Azure Stack registrado, você terá que reregistrá-lo após a associação

- Entre usando uma conta que:

  - Tem uma atribuição de função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) para a assinatura. Para obter informações sobre como atribuir a função Proprietário, consulte Gerenciar o [acesso aos recursos do Azure usando o RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md).
  - Existe tanto no diretório atual quanto no novo diretório. O diretório atual está associado à assinatura. Você associará o novo diretório à assinatura. Para obter mais informações sobre como obter acesso a outro diretório, consulte Adicionar usuários de [colaboração B2B do Active Directory no portal Azure](../b2b/add-users-administrator.md).

- Verifique se você não está usando uma assinatura de Provedor de Serviços de Nuvem do Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma assinatura Interna da Microsoft (MS-AZR-0015P) ou Assinatura do Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associar uma assinatura a um diretório<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para associar uma assinatura existente ao seu diretório Azure AD, siga estas etapas:

1. Faça login e selecione a assinatura que você deseja usar na página [Inscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecione **altere o diretório**.

    ![Página de assinaturas, com a opção Alterar diretório destacada](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Revise todos os avisos exibidos e, em seguida, selecione **Alterar**.

    ![Altere a página do diretório, mostrando o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a assinatura e você recebe uma mensagem de sucesso.

    ![Mensagem de sucesso sobre mudança de diretório](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Use **o diretório do Switch** para ir ao seu novo diretório. Pode levar várias horas para tudo aparecer corretamente. Se parecer que está demorando muito, verifique o **filtro de assinatura Global**. Certifique-se de que a assinatura não esteja oculta. Você pode precisar sair do portal Azure e entrar de volta para ver o novo diretório.

![Página do switcher do diretório, com informações de exemplo](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

A alteração do diretório de assinatura é uma operação no nível do serviço, portanto, não afeta a propriedade do faturamento da assinatura. O administrador da conta ainda pode alterar o administrador do serviço do [Centro de contas](https://account.azure.com/subscriptions). Para excluir o diretório original, você deve transferir a propriedade de cobrança de assinatura para um novo Admin de conta. Para saber mais sobre a transferência de propriedade de faturamento, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Etapas pós-associação

Depois de associar uma assinatura a um diretório diferente, talvez seja necessário fazer as seguintes tarefas para retomar as operações:

- Se você tem algum cofre chave, você deve mudar o id do inquilino do cofre chave. Para obter mais informações, consulte [Alterar um ID de inquilino do cofre de chave após um movimento de assinatura](../../key-vault/general/subscription-move-fix.md).

- Se você usou identidades gerenciadas atribuídas ao sistema para recursos, você deve reativar essas identidades. Se você usou identidades gerenciadas atribuídas pelo usuário, você deve recriar essas identidades. Depois de reativar ou recriar as Identidades Gerenciadas, você deve restabelecer as permissões atribuídas a essas identidades. Para saber mais, confira [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

- Se você registrou um Azure Stack usando esta assinatura, você deve se recadastrar. Para obter mais informações, consulte [Register Azure Stack com o Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Próximas etapas

- Para criar um novo inquilino Azure AD, consulte [Quickstart: Crie um novo inquilino no Azure Active Directory](active-directory-access-create-new-tenant.md).

- Para saber mais sobre como o Microsoft Azure controla o acesso a recursos, consulte [funções clássicas de administrador de assinaturas, funções de Administrador Do Azure RBAC e adviamento do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Para saber mais sobre como atribuir funções no Azure AD, consulte [Atribuir funções de administrador e não-administrador aos usuários com o Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
