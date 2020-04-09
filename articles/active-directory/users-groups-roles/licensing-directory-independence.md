---
title: Características da interação múltipla do inquilino - Azure AD | Microsoft Docs
description: Entendendo seus inquilinos do Azure Active Directory como organizações totalmente independentes
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878112"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Entenda como várias organizações de diretórios ativos do Azure interagem

No Azure Active Directory (Azure AD), cada inquilino é uma organização totalmente independente: um peer que é logicamente independente das outras organizações Azure AD que você gerencia. Essa independência entre as organizações inclui independência de recursos, independência administrativa e independência de sincronização. Não há relação pai-filho entre as organizações.

## <a name="resource-independence"></a>Independência de recursos

* Se você criar ou excluir um recurso Azure AD em uma organização, ele não tem impacto em nenhum recurso em outra organização, com a exceção parcial de usuários externos.
* Se você registrar um de seus nomes de domínio com uma organização, ele não poderá ser usado por nenhuma outra organização.

## <a name="administrative-independence"></a>Independência administrativa

Se um usuário não administrativo da organização 'Contoso' criar uma organização de teste 'Teste', então:

* Por padrão, o usuário que cria uma organização é adicionado como um usuário externo nessa nova organização e atribui a função de administrador global nessa organização.
* Os administradores da organização 'Contoso' não têm privilégios administrativos diretos para organizar o 'Teste', a menos que um administrador de 'Teste' conceda especificamente esses privilégios. No entanto, os administradores do 'Contoso' podem controlar o acesso à organização 'Teste' se controlarem a conta de usuário que criou o 'Teste'.
* Se você adicionar ou remover uma função Ad do Azure para um usuário em uma organização, a alteração não afetará as funções que o usuário é atribuído em qualquer outra organização Azure AD.

## <a name="synchronization-independence"></a>Independência de sincronização

Você pode configurar cada organização Azure AD independentemente para obter dados sincronizados a partir de uma única instância de qualquer um:

* Ferramenta do Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* Azure Active Directory Connector para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-organization"></a>Adicionar uma organização Azure AD

Para adicionar uma organização Azure AD no portal Azure, entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e selecione **Novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, suas organizações Azure AD não são recursos infantis de uma assinatura do Azure. Se sua assinatura do Azure for cancelada ou expirada, você ainda poderá acessar os dados da organização Azure AD usando o Azure PowerShell, a API do Microsoft Graph ou o centro de administradores microsoft 365. Você também pode [associar outra assinatura à organização.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Próximas etapas

Para considerações e práticas recomendadas de licenciamento do Azure AD, consulte [O que é o licenciamento do Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
