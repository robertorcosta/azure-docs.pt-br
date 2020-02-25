---
title: Características de uma interação de locatário múltiplo – Azure AD | Microsoft Docs
description: Gerenciar os locatários do Azure Active Directory considerando os locatários como recursos totalmente independentes
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562251"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Entender como interagem vários locatários do Azure Active Directory

No Azure AD (Azure Active Directory), cada locatário é um recurso totalmente independente: um par logicamente independente dos outros locatários gerenciados. Não há nenhuma relação pai-filho entre locatários. Essa independência entre locatários inclui independência de recursos, independência administrativa e independência de sincronização.

## <a name="resource-independence"></a>Independência de recursos
* Se você criar ou excluir um recurso de um locatário, ele não afetará nenhum recurso em outro locatário, com a exceção parcial de usuários externos. 
* Se você usar um de seus nomes de domínio com um locatário, ele não poderá ser usado com nenhum outro locatário.

## <a name="administrative-independence"></a>Independência administrativa
Se um usuário não administrativo do locatário “Contoso” criar um locatário de teste “Teste”:

* Por padrão, o usuário que cria um locatário é adicionado como um usuário externo nesse novo locatário e é atribuído à função de administrador global nesse locatário.
* Os administradores do locatário “Contoso” não têm privilégios administrativos diretos no diretório “Teste”, a menos que um administrador de “Teste” conceda especificamente esses privilégios a eles. No entanto, os administradores do “Contoso” poderão controlar o acesso ao locatário “Teste” se controlarem a conta de usuário que criou “Teste”.
* Se você adicionar ou remover uma função de administrador de um usuário em um locatário, a alteração não afetará as funções de administrador que o usuário tem em outro locatário.

## <a name="synchronization-independence"></a>Independência de sincronização
Configure cada locatário do Azure AD de maneira independente para sincronizar os dados de uma única instância de:

* Ferramenta do Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* Conector de locatário do Azure Active Directory para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Adicionar um locatário do Azure AD
Para adicionar um locatário do Azure AD no Portal do Azure, entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e, à esquerda, selecione **Novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, os locatários não são recursos filho de uma assinatura do Azure. Se sua assinatura do Azure for cancelada ou expirada, você ainda poderá acessar os dados do locatário usando Azure PowerShell, a API do Microsoft Graph ou o centro de administração do Microsoft 365. Você também pode [associar outra assinatura ao locatário](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral ampla dos problemas de licenciamento e as melhores práticas do Azure AD, consulte [O que é o licenciamento de locatário do Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
