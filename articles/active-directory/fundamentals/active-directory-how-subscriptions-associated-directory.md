---
title: Adicionar uma assinatura do Azure existente ao seu locatário-Azure AD
description: Instruções sobre como adicionar uma assinatura do Azure existente ao seu locatário do Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd095939009c39c48456d330f975303f06a841a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267523"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory

Uma assinatura do Azure tem uma relação de confiança com Azure Active Directory (Azure AD). Uma assinatura confia no Azure AD para autenticar usuários, serviços e dispositivos.

Várias assinaturas podem confiar no mesmo diretório do AD do Azure. Cada assinatura só pode confiar em um único diretório.

Se a sua assinatura expirar, você perderá o acesso a todos os outros recursos associados à assinatura. No entanto, o diretório do AD do Azure permanece no Azure. Você pode associar e gerenciar o diretório usando uma assinatura do Azure diferente.

Por padrão, quando um usuário se inscreve em um serviço de nuvem da Microsoft, um locatário do Azure AD é criado e o usuário se torna membro da função de administradores globais. Quando você adiciona uma assinatura a um diretório existente, não é atribuída à função de administrador global.

Todos os seus usuários têm um único diretório *base* para autenticação. Os usuários também podem ser convidados em outros diretórios. Você pode ver os diretórios inicial e convidado de cada usuário no Azure AD.

> [!Important]
> Quando você associa uma assinatura a um diretório diferente, os usuários que têm funções atribuídas usando o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md) perdem seu acesso. Os administradores de assinatura clássicos, incluindo o Administrador de Serviços e os Coadministradores, também perdem o acesso.
>
> As Atribuições de Política também são removidas de uma assinatura quando ela é associada a um diretório diferente.
>
> Mover o cluster AKS (serviço kubernetes do Azure) para uma assinatura diferente ou mover a assinatura proprietária do cluster para um novo locatário faz com que o cluster perca a funcionalidade devido a atribuições de função perdidas e direitos da entidade de serviço. Para obter mais informações sobre AKS, consulte [serviço kubernetes do Azure (AKs)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Antes de começar

Para poder associar ou adicionar sua assinatura, execute as seguintes tarefas:

- Examine a seguinte lista de alterações que ocorrerão depois que você associar ou adicionar sua assinatura e como você pode ser afetado:

  - Usuários aos quais foram atribuídas funções usando o RBAC do Azure perderão seu acesso
  - O administrador de serviços e coadministradores perderão o acesso
  - Se você tiver cofres de chaves, eles ficarão inacessíveis e você precisará corrigi-los após a associação
  - Se você tiver identidades gerenciadas para recursos como máquinas virtuais ou aplicativos lógicos, será necessário reabilitá-los ou recriá-los após a associação
  - Se você tiver um Azure Stack registrado, será necessário registrá-lo novamente após a associação
  - Para obter mais informações, confira [Transferir uma assinatura do Azure para um diretório diferente do Azure AD (versão prévia)](../../role-based-access-control/transfer-subscription.md).

- Entre usando uma conta que:

  - Tem uma atribuição de função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) para a assinatura. Para obter informações sobre como atribuir a função de proprietário, consulte [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
  - Existe no diretório atual e no novo diretório. O diretório atual está associado à assinatura. Você associará o novo diretório à assinatura. Para obter mais informações sobre como obter acesso a outro diretório, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../b2b/add-users-administrator.md).

- Verifique se você não está usando uma assinatura de Provedor de Serviços de Nuvem do Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma assinatura Interna da Microsoft (MS-AZR-0015P) ou Assinatura do Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associar uma assinatura a um diretório<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Para associar uma assinatura existente ao seu diretório do Azure AD, siga estas etapas:

1. Entre e selecione a assinatura que você deseja usar na [página assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecione **Alterar diretório**.

   ![Página de assinaturas, com a opção Alterar diretório destacada](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Revise todos os avisos que aparecem e, em seguida, selecione **alterar**.

   ![Altere a página do diretório, mostrando o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   Depois que o diretório for alterado para a assinatura, você receberá uma mensagem de êxito.

1. Selecione **alternar diretórios** na página de assinatura para ir para o novo diretório.

   ![Página de seletor de diretório, com informações de exemplo](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   Pode levar várias horas para que tudo seja exibido corretamente. Se parecer que está demorando muito, verifique o **filtro de assinatura global**. Verifique se a assinatura movida não está oculta. Talvez seja necessário sair do portal do Azure e entrar novamente para ver o novo diretório.

A alteração do diretório de assinatura é uma operação no nível do serviço, portanto, não afeta a propriedade do faturamento da assinatura. O administrador da conta ainda pode alterar o administrador do serviço do [Centro de contas](https://account.azure.com/subscriptions). Para excluir o diretório original, você deve transferir a propriedade de cobrança da assinatura para um novo administrador da conta. Para saber mais sobre como transferir a propriedade de cobrança, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Etapas pós-Associação

Depois de associar uma assinatura a um diretório diferente, talvez seja necessário realizar as seguintes tarefas para retomar as operações:

- Se você tiver qualquer cofre de chaves, deverá alterar a ID de locatário do cofre de chaves. Para obter mais informações, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](../../key-vault/general/move-subscription.md).

- Se você usou identidades gerenciadas atribuídas pelo sistema para recursos, você deve reabilitar essas identidades. Se você usou identidades gerenciadas atribuídas pelo usuário, deverá recriar essas identidades. Depois de habilitar novamente ou recriar as identidades gerenciadas, você deve restabelecer as permissões atribuídas a essas identidades. Para saber mais, confira [O que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

- Se você tiver registrado um Azure Stack usando essa assinatura, deverá registrar novamente. Para obter mais informações, consulte [registrar Azure Stack com o Azure](/azure-stack/operator/azure-stack-registration).

- Para obter mais informações, confira [Transferir uma assinatura do Azure para um diretório diferente do Azure AD (versão prévia)](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Próximas etapas

- Para criar um novo locatário do Azure AD, consulte [início rápido: criar um novo locatário no Azure Active Directory](active-directory-access-create-new-tenant.md).

- Para saber mais sobre como o Microsoft Azure controla o acesso a recursos, consulte funções de [administrador de assinatura clássica, funções do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Para saber mais sobre como atribuir funções no Azure AD, consulte [atribuir funções de administrador e não administrador a usuários com Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
