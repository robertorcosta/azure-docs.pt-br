---
title: Adicionar uma assinatura do Azure existente ao seu locatário – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar uma assinatura do Azure existente ao seu locatário do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473384"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory

Uma assinatura do Azure tem uma relação de confiança com Azure Active Directory (Azure AD), o que significa que a assinatura confia no Azure AD para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório do Azure AD, mas cada assinatura só pode confiar em um único diretório.

Se a sua assinatura expirar, você perderá o acesso a todos os outros recursos associados à assinatura. No entanto, o diretório do Azure AD permanece no Azure, permitindo associar e gerenciar o diretório usando uma assinatura diferente do Azure.

Todos os seus usuários têm um único diretório *base* para autenticação. No entanto, seus usuários também podem ser convidados em outros diretórios. Você pode ver os diretórios inicial e convidado de cada usuário no Azure AD.

> [!Important]
> Quando você associa uma assinatura a um diretório diferente, os usuários que têm funções atribuídas usando o [controle de acesso baseado em função (RBAC)](../../role-based-access-control/role-assignments-portal.md) perderão seu acesso. Os administradores de assinatura clássicos (administrador de serviços e coadministradores) também perderão o acesso. 
>
> As atribuições de política também são removidas de uma assinatura quando a assinatura é associada a um diretório diferente.
> 
> Além disso, mover o cluster AKS (serviço kubernetes do Azure) para uma assinatura diferente ou mover a assinatura proprietária do cluster para um novo locatário faz com que o cluster perca funcionalidade devido a atribuições de função perdidas e direitos de entidades de serviço. Para obter mais informações sobre AKS, consulte [serviço kubernetes do Azure (AKs)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Antes de começar

Antes de associar ou adicionar sua assinatura, você deve executar as seguintes tarefas:

1. Examine a lista de alterações a seguir e como você pode ser afetado:

    - Os usuários aos quais foram atribuídas funções usando o RBAC perderão seu acesso
    - O administrador de serviços e coadministradores perderão o acesso
    - Se você tiver cofres de chaves, eles ficarão inacessíveis e você precisará corrigi-los após a associação
    - Se você tiver identidades gerenciadas para recursos como máquinas virtuais ou aplicativos lógicos, precisará reabilitar ou recriá-los após a associação
    - Se você tiver um Azure Stack registrado, será necessário registrá-lo novamente após a associação

1. Entre usando uma conta que:
    - Tem uma atribuição de função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) para a assinatura. Para obter informações sobre como atribuir a função de proprietário, consulte [gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
    - Existe no diretório atual associado à assinatura e no novo diretório no qual você deseja associar a assinatura daqui para frente. Para obter mais informações sobre como obter acesso a outro diretório, consulte [Como os administradores do Active Directory do Azure adicionam usuários de colaboração B2B?](../b2b/add-users-administrator.md).

1. Verifique se você não está usando uma assinatura de Provedor de Serviços de Nuvem do Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma assinatura Interna da Microsoft (MS-AZR-0015P) ou Assinatura do Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Para associar uma assinatura existente ao seu diretório do Azure AD

1. Faça login e selecione a assinatura que você deseja usar na página [Inscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione **altere o diretório**.

    ![Página de assinaturas, com a opção Alterar diretório destacada](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Revise todos os avisos exibidos e, em seguida, selecione **Alterar**.

    ![Altere a página do diretório, mostrando o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a assinatura e você recebe uma mensagem de sucesso.

    ![Mensagem de êxito sobre alteração de diretório](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Use o **seletor de diretório** para ir para o novo diretório. Pode levar várias horas para que tudo seja exibido corretamente. Se parecer que está demorando muito, verifique o **filtro de assinatura global** para a assinatura movida para verificar se ele não está oculto. Talvez seja necessário sair do portal do Azure e entrar novamente para poder ver o novo diretório. 

    ![Página de seletor de diretório, com informações de exemplo](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

A alteração do diretório de assinatura é uma operação no nível do serviço, portanto, não afeta a propriedade do faturamento da assinatura. O administrador da conta ainda pode alterar o administrador do serviço do [Centro de contas](https://account.azure.com/subscriptions). Para excluir o diretório original, você deve transferir a propriedade de cobrança da assinatura para um novo administrador da conta. Para saber mais sobre como transferir a propriedade de cobrança, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Etapas de associação post
Depois de associar uma assinatura a um diretório diferente, pode haver etapas adicionais que você deve executar para retomar as operações.

1. Se você tiver qualquer cofre de chaves, deverá alterar a ID de locatário do cofre de chaves. Para obter mais informações, consulte [alterar uma ID de locatário do cofre de chaves após a movimentação de uma assinatura](../../key-vault/key-vault-subscription-move-fix.md).

2. Se você estivesse usando identidades gerenciadas atribuídas pelo sistema para recursos, você deve reabilitá-las. Se você estiver usando identidades gerenciadas atribuídas pelo usuário, deverá recriá-las. Depois de habilitar novamente ou recriar as identidades gerenciadas, você deve restabelecer as permissões atribuídas a essas identidades. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

3. Se você registrou um Azure Stack usando essa assinatura, deverá registrar novamente. Para obter mais informações, consulte [registrar Azure Stack com o Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Próximas etapas

- Para criar um novo locatário do Azure AD, consulte [Aceder ao Active Directory do Azure para criar um novo inquilino](active-directory-access-create-new-tenant.md)

- Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Para saber mais sobre como atribuir funções no Azure AD, consulte [Como atribuir funções de diretório a usuários com o Active Directory do Azure](active-directory-users-assign-role-azure-portal.md)
