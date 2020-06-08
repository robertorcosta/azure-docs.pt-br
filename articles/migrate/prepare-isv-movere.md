---
title: Preparar as Migrações para Azure para trabalhar com uma ferramenta ISV/Movere
description: Este artigo descreve como preparar as Migrações para Azure para trabalhar com uma ferramenta ISV ou um Movere e, em seguida, como começar a usar a ferramenta.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682656"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Preparar-se para trabalhar com uma ferramenta ISV/Movere

Se você adicionou uma [ferramenta ISV](migrate-services-overview.md#isv-integration) ou Movere para um projeto de Migrações para Azure, há algumas etapas a serem seguidas antes de vincular a ferramenta e enviar dados para as Migrações para Azure. 

## <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

Sua conta de usuário do Azure precisa dessas permissões:

- Permissão para registrar um aplicativo do Azure Active Directory (Azure AD) com seu locatário do Azure
- Permissão para alocar uma função ao aplicativo do Azure AD no nível de assinatura


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Definir permissões para registrar um aplicativo do Azure AD

1. Verifique a função da sua conta no Azure AD.
2. Se você tiver a função de usuário, selecione **Configurações de usuário** à esquerda e verifique se os usuários podem registrar aplicativos. Se estiver definido como **Sim**, qualquer usuário no locatário do Azure AD poderá registrar um aplicativo. Se estiver definido como **Não**, somente usuários administradores podem registrar aplicativos.   
3. Se você não tiver permissões, um usuário administrador poderá fornecer a sua conta de usuário com a função de [Administrador de aplicativos](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator), para que você possa registrar o aplicativo.
4. Depois que a ferramenta estiver vinculada às Migrações para Azure, o administrador poderá remover a função da sua conta.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Definir permissões para atribuir uma função a um aplicativo do Azure AD
 
Em sua assinatura do Azure, sua conta precisa do acesso ao **Microsoft.Authorization/*/Write** para atribuir uma função a um aplicativo do Azure AD. 

1. No portal do Azure, abra **Assinaturas**.
2. Selecione a assinatura relevante. Se você não o vir, selecione o **filtro de assinaturas globais**. 
3. Selecionar **Minhas permissões**. Em seguida, selecione **Clique aqui para exibir o acesso completo detalhes para essa assinatura**.
4. Em **Atribuições de função** > **Exibição**, verifique as permissões. Se sua conta não tiver permissões, peça ao administrador da assinatura que o adicione à função [Administrador de acesso do usuário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) ou à função [Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner).
 

## <a name="start-using-the-tool"></a>Comece a usar a ferramenta

1. Se você ainda não tiver uma licença ou uma avaliação gratuita para a ferramenta, na entrada da ferramenta nas Migrações para Azure, em **Registrar**, selecione **Saiba mais**.
2. Na ferramenta, siga as instruções para vincular a partir da ferramenta ao projeto de Migrações para Azure e para enviar dados para as Migrações para Azure.

## <a name="next-steps"></a>Próximas etapas

Siga as instruções do seu ISV ou Movere para enviar dados para as Migrações para Azure.

   
