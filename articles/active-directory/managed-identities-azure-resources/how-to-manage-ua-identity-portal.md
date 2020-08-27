---
title: Gerenciar uma identidade gerenciada atribuída pelo usuário no portal do Azure-Azure AD
description: Instruções passo a passo sobre como criar, listar, excluir e atribuir uma função a uma identidade gerenciada atribuída pelo usuário.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348d436d1cb1fa838a34c9ebe86f5e37f0f85803
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930547"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá a criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para criar a identidade gerenciada atribuída ao usuário.
2. Na caixa de pesquisa, digite *identidades gerenciadas*e, em **Serviços**, clique em **identidades gerenciadas**.
3. Clique em **Adicionar** e insira valores nos campos a seguir no painel **Criar identidade gerenciada atribuída ao usuário**:
    - **Assinatura**: escolha a assinatura para a qual criar a identidade gerenciada atribuída pelo usuário.
    - **Grupo de recursos**: escolha um grupo de recursos para criar a identidade gerenciada atribuída pelo usuário ou clique em **criar novo** para criar um novo grupo de recursos.
    - **Região**: escolha uma região para implantar a identidade gerenciada atribuída pelo usuário, por exemplo, **oeste dos EUA**.
    - **Nome**: esse é o nome para a identidade gerenciada atribuída pelo usuário, por exemplo, UAI1.
    ![Criar uma identidade gerenciada atribuída ao usuário](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Clique em **revisar +** enformar para examinar as alterações.
5. Clique em **Criar**.

## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para listar as identidades gerenciadas atribuídas ao usuário.
2. Na caixa de pesquisa, digite *Identidades Gerenciadas*e, em Serviços, clique em **Identidades Gerenciadas**.
3. Uma lista de identidades gerenciadas atribuídas ao usuário para a sua assinatura é retornada.  Para ver os detalhes de uma identidade gerenciada atribuída ao usuário, clique no nome.

![Listar identidade gerenciada atribuída ao usuário](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

A exclusão de uma identidade atribuída pelo usuário não a remove da VM ou recurso ao qual ela foi atribuída.  Para remover uma identidade atribuída ao usuário de uma VM, confira [Remover uma identidade gerenciada atribuída ao usuário de uma VM](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para excluir a identidade gerenciada atribuída ao usuário.
2. Selecione a identidade gerenciada atribuída ao usuário e clique em **Excluir**.
3. Na caixa de confirmação, escolha, **Sim**.

![Excluir identidade gerenciada atribuída ao usuário](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Atribuir uma função a uma identidade gerenciada atribuída pelo usuário 

Para atribuir uma função a uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Administrador de Acesso do Usuário](/azure/role-based-access-control/built-in-roles#user-access-administrator).

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para listar as identidades gerenciadas atribuídas ao usuário.
2. Na caixa de pesquisa, digite *Identidades Gerenciadas*e, em Serviços, clique em **Identidades Gerenciadas**.
3. Uma lista de identidades gerenciadas atribuídas ao usuário para a sua assinatura é retornada.  Selecione a identidade gerenciada atribuída pelo usuário à qual você deseja atribuir uma função.
4. Selecione **Controle de Acesso (IAM)** e, em seguida, selecione **Adicionar atribuição de função**.

   ![Início da identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Na folha adicionar atribuição de função, configure os seguintes valores e, em seguida, clique em **salvar**:
   - **Função** – a função a ser atribuída
   - **Atribuir acesso a** – o recurso ao qual a identidade gerenciada atribuída pelo usuário será atribuída
   - **Selecione** – o membro ao qual o acesso será atribuído
   
   ![IAM de identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
