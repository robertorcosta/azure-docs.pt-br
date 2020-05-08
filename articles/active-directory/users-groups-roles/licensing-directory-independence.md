---
title: Características de uma interação de locatário múltiplo – Azure AD | Microsoft Docs
description: Compreendendo a independência de dados de suas organizações Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe7b0211775e59504443d30fa253cfa14af13b1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582749"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Entenda como várias organizações Azure Active Directory interagem

No Azure Active Directory (AD do Azure), cada organização do Azure AD é totalmente independente: um ponto que é logicamente independente das outras organizações do Azure AD que você gerencia. Essa independência entre organizações inclui independência de recursos, independência administrativa e independência de sincronização. Não há nenhuma relação pai-filho entre organizações.

## <a name="resource-independence"></a>Independência de recursos

* Se você criar ou excluir um recurso do Azure AD em uma organização, ele não afetará nenhum recurso em outra organização, com a exceção parcial de usuários externos.
* Se você registrar um de seus nomes de domínio com uma organização, ele não poderá ser usado por nenhuma outra organização.

## <a name="administrative-independence"></a>Independência administrativa

Se um usuário não administrativo da organização ' contoso ' criar uma organização de teste ' test ', então:

* Por padrão, o usuário que cria uma organização é adicionado como um usuário externo na nova organização e atribuiu a função de administrador global nessa organização.
* Os administradores da organização ' contoso ' não têm privilégios administrativos diretos para a organização ' teste ', a menos que um administrador de ' teste ' especificamente conceda a eles esses privilégios. No entanto, os administradores do "contoso" podem controlar o acesso à organização "teste" se controlarem a conta de usuário que criou "teste".
* Se você adicionar ou remover uma função do Azure AD para um usuário em uma organização, a alteração não afetará as funções que o usuário está atribuído em qualquer outra organização do Azure AD.

## <a name="synchronization-independence"></a>Independência de sincronização

Você pode configurar cada organização do Azure AD independentemente para obter dados sincronizados de uma única instância de:

* Ferramenta do Azure AD Connect, para sincronizar dados com uma única floresta do AD.
* Azure Active Directory Connector para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não Azure AD.

## <a name="add-an-azure-ad-organization"></a>Adicionar uma organização do Azure AD

Para adicionar uma organização do Azure AD no portal do Azure, entre no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do Azure AD e selecione **novo**.

> [!NOTE]
> Ao contrário de outros recursos do Azure, suas organizações do Azure AD não são recursos filho de uma assinatura do Azure. Se sua assinatura do Azure for cancelada ou expirada, você ainda poderá acessar os dados da sua organização do Azure AD usando Azure PowerShell, a API do Microsoft Graph ou o centro de administração do Microsoft 365. Você também pode [associar outra assinatura à organização](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Próximas etapas

Para considerações de licenciamento e práticas recomendadas do Azure AD, consulte [o que é Azure Active Directory licenciamento?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
