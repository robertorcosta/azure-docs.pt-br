---
title: Preparar migrações para Azure para trabalhar com ferramentas de ISV/mover
description: Preparar migrações para Azure para trabalhar com ferramentas de ISV/mover
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906976"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Prepare-se para trabalhar com ferramentas de ISV/mover

Se você adicionou uma [ferramenta ISV](migrate-services-overview.md#isv-integration), ou mover, a um projeto de migrações para Azure, há algumas etapas a serem preparadas antes de vincular a ferramenta e enviar dados para as migrações para o Azure. 

## <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

Sua conta de usuário do Azure precisa de algumas permissões:

- Permissão para registrar um aplicativo do Azure AD com seu locatário do Azure.
- Permissão para alocar uma função para o aplicativo do Azure AD no nível de assinatura.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Definir permissões para registrar um aplicativo do Azure AD

1. Em Azure Active Directory, verifique a função da sua conta. Se você tiver a função de usuário, clique em **configurações de usuário** à esquerda e verifique se os usuários podem registrar aplicativos.
2. Se estiver definido como **Sim**, todos os usuários no locatário do Azure ad poderão registrar um aplicativo. Se não puderem, somente os usuários administradores poderão registrar aplicativos.
3. Se você não tiver permissões, um usuário administrador poderá fornecer sua conta de usuário com a função de [administrador de aplicativos](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) , para que você possa registrar o aplicativo.
4. Depois que a ferramenta estiver vinculada à migração do Azure, o administrador poderá remover a função de sua conta.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Definir permissões para atribuir uma função a um aplicativo do Azure AD
 
Em sua assinatura do Azure, sua conta precisa de **acesso Microsoft. Authorization/*/Write**para atribuir uma função a um aplicativo do Azure AD. 

1. Para verificar as permissões de assinatura, no portal do Azure, abra **assinaturas**.
2. Selecione a assinatura relevante. Se você não o vir, selecione o **filtro assinaturas globais**. 
3. Selecione **minhas permissões**. Em seguida, selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.
4. Na**exibição** **atribuições** > de função e verifique as permissões.
5. Se sua conta não tiver permissões, peça ao administrador da assinatura para adicioná-lo à função [administrador de acesso do usuário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) ou à função [proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) .
 

## <a name="start-using-the-tool"></a>Começar a usar a ferramenta

1. Se você ainda não tiver uma licença ou uma avaliação gratuita para a ferramenta, na entrada de ferramenta do Azure migrar, em **registrar**, clique em **saiba mais**.
2. Na ferramenta, siga as instruções para vincular a ferramenta ao projeto de migrações para Azure e para enviar dados para as migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Siga as instruções de ISV ou mover para enviar dados para as migrações para Azure.

   
