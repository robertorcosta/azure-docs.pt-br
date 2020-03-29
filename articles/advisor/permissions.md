---
title: Permissões no Azure Advisor
description: Permissões de conselheiro e como elas podem bloquear sua capacidade de configurar assinaturas ou adiar ou rejeitar recomendações.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422311"
---
# <a name="permissions-in-azure-advisor"></a>Permissões no Azure Advisor

O Azure Advisor fornece recomendações baseadas no uso e configuração de seus recursos e assinaturas do Azure. O Advisor usa as [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) fornecidas pelo RBAC [(Role-Based Access Control, controle de acesso baseado em](https://docs.microsoft.com/azure/role-based-access-control/overview) função) para gerenciar seu acesso a recomendações e recursos do Advisor. 

## <a name="roles-and-their-access"></a>Papéis e seu acesso

A tabela a seguir define as funções e o acesso que eles têm dentro do Advisor:

| **Role** | **Ver recomendações** | **Editar regras** | **Editar configuração de assinatura** | **Editar configuração de grupo de recursos**| **Destituir e adiar recomendações**|
|---|:---:|:---:|:---:|:---:|:---:|
|Proprietário da assinatura|**X**|**X**|**X**|**X**|**X**|
|Colaborador da assinatura|**X**|**X**|**X**|**X**|**X**|
|Leitor de assinaturas|**X**|--|--|--|--|
|Proprietário do grupo de recursos|**X**|--|--|**X**|**X**|
|Contribuinte do grupo de recursos|**X**|--|--|**X**|**X**|
|Leitor do grupo de recursos|**X**|--|--|--|--|
|Proprietário de recursos|**X**|--|--|--|**X**|
|Contribuinte de Recursos|**X**|--|--|--|**X**|
|Leitor de recursos|**X**|--|--|--|--|

> [!NOTE]
> O acesso às recomendações de visualização depende do seu acesso ao recurso impactado da recomendação.

## <a name="permissions-and-unavailable-actions"></a>Permissões e ações indisponíveis

A falta de permissões adequadas pode bloquear sua capacidade de executar ações no Advisor. A seguir estão alguns problemas comuns.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Não é possível configurar assinaturas ou grupos de recursos

Quando você tenta configurar assinaturas ou grupos de recursos no Advisor, você pode ver que a opção de incluir ou excluir está desativada. Esse status indica que você não tem um nível suficiente de permissão para esse grupo de recursos ou assinatura. Para resolver esse problema, saiba como conceder acesso ao [usuário.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Incapaz de adiar ou rejeitar uma recomendação

Se você receber um erro ao tentar adiar ou rejeitar uma recomendação, você pode não ter permissões suficientes. Certifique-se de que você tenha, pelo menos, acesso contribuinte ao recurso impactado da recomendação que você está adiando ou descartando. Para resolver esse problema, saiba como conceder acesso ao [usuário.](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

## <a name="next-steps"></a>Próximas etapas

Este artigo deu uma visão geral de como o Advisor usa o RBAC para controlar as permissões do usuário e como resolver problemas comuns. Para saber mais sobre o Assistente, consulte:

- [O que é o Azure Advisor?](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Comece com o Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
