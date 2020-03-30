---
title: Alterar planos de licença para usuários e grupos - Azure AD | Microsoft Docs
description: Como migrar usuários dentro de um grupo para diferentes planos de serviço usando o licenciamento de grupo no Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025897"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Alterar atribuições de licença para um usuário ou grupo no Azure Active Directory

Este artigo descreve como mover usuários e grupos entre planos de licença de serviço no Azure Active Directory (Azure AD). O objetivo da abordagem do Azure AD é garantir que não haja perda de serviço ou dados durante a alteração da licença. Os usuários devem alternar entre serviços perfeitamente. As etapas de atribuição do plano de licença neste artigo descrevem a alteração de um usuário ou grupo no Office 365 E1 para o Office 365 E3, mas as etapas se aplicam a todos os planos de licença. Quando você atualiza as atribuições de licença para um usuário ou grupo, as remoções de cessão de licença e novas atribuições são feitas simultaneamente para que os usuários não percam o acesso a seus serviços durante as alterações de licença ou vejam conflitos de licença entre planos.

## <a name="before-you-begin"></a>Antes de começar

Antes de atualizar as atribuições da licença, é importante verificar se certas suposições são verdadeiras para que todos os usuários ou grupos sejam atualizados. Se as suposições não forem verdadeiras para todos os usuários de um grupo, a migração pode falhar para alguns. Como resultado, alguns dos usuários poderão perder o acesso a serviços ou dados. Verifique se:

- Os usuários têm o plano de licença atual (neste caso, Office 365 E1) que é atribuído a um grupo e herdado pelo usuário e não atribuído diretamente.

- Você tem licenças disponíveis o suficiente para o plano de licença que você está atribuindo. Se você não tiver licenças suficientes, alguns usuários podem não ser designados para o novo plano de licença. É possível verificar o número de licenças disponíveis.

- Os usuários não têm outras licenças de serviço atribuídas que possam entrar em conflito com a licença desejada ou impedir a remoção da licença atual. Por exemplo, uma licença de um serviço como Workplace Analytics ou Project Online que tenha uma dependência de outros serviços.

- Se você gerenciar grupos no local e sincronizá-los no Azure AD Connect via Azure AD Connect, então você adicionará ou removerá os usuários usando seu sistema local. Pode levar algum tempo para que as alterações sejam sincronizadas com o Azure AD para serem captadas pelo licenciamento de grupo.

- Se você estiver usando membros do grupo dinâmico AD do Azure, adicione ou remova usuários alterando seus atributos, mas o processo de atualização para atribuições de licença permanece o mesmo.

## <a name="change-user-license-assignments"></a>Alterar atribuições de licença de usuário

Na página **Demissões de licença seleção** Update, se você ver que algumas caixas de seleção estão indisponíveis, ela indica serviços que não podem ser alterados porque são herdados de uma licença de grupo.

1. Faça login no [portal Azure](https://portal.azure.com/) usando uma conta de administrador de licenças em sua organização Azure AD.
1. Selecione Usuários do >  **Diretório Ativo do Azure**e abra a página **Perfil** para um usuário.**Users**
1. Selecione **Licenças**.
1. Selecione **Atribuições** para editar a atribuição de licença para o usuário ou grupo. A página **Atribuições** é onde você pode resolver conflitos de atribuição de licença.
1. Selecione a caixa de seleção do Office 366 E3 e certifique-se de que, no mínimo, todos os serviços E1 atribuídos ao usuário sejam selecionados.
1. Limpe a caixa de seleção para office 365 E1.

    ![página de atribuições de licença para um usuário mostrando Office 365 E1 limpo e Office 365 E3 selecionado](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selecione **Salvar**.

O Azure AD aplica as novas licenças e remove as licenças antigas simultaneamente para fornecer continuidade de serviços.

## <a name="change-group-license-assignments"></a>Alterar atribuições de licença de grupo

1. Faça login no [portal Azure](https://portal.azure.com/) usando uma conta de administrador de licenças em sua organização Azure AD.
1. Selecione **Grupos de diretórios ativos do** > Azure e**abra**a página **Visão geral** de um grupo.
1. Selecione **Licenças**.
1. Selecione o comando **Atribuições** para editar a atribuição de licença para o usuário ou grupo.
1. Selecione a caixa de seleção do Office 366 E3. Para manter a continuidade do serviço, certifique-se de selecionar todos os serviços E1 que já estão atribuídos ao usuário.
1. Limpe a caixa de seleção para office 365 E1.

    ![Selecione o comando Atribuições em uma página de Licenças de usuário ou grupo](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selecione **Salvar**.

Para fornecer continuidade de serviços, o Azure AD aplica as novas licenças e remove as licenças antigas simultaneamente para todos os usuários do grupo.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários de gerenciamento de licenças por meio de grupos nos seguintes artigos:

- [Atribuição de licenças a um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
- [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Como migrar usuários licenciados individuais para o licenciamento de grupo no Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
- [Grupo azure Active Directory licenciando cenários adicionais](../users-groups-roles/licensing-group-advanced.md)
- [Exemplos do PowerShell para licenciamento em grupo no Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
