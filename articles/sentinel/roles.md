---
title: Permissões no Azure Sentinel | Microsoft Docs
description: Este artigo explica como o Azure Sentinel usa o controle de acesso baseado em função para atribuir permissões aos usuários e identifica as ações permitidas para cada função.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587168"
---
# <a name="permissions-in-azure-sentinel"></a>Permissões no Azure Sentinel

O Azure Sentinel usa [o RBAC (Role-Based Access Control, controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md)para fornecer [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

Usando o RBAC, você pode usar e criar funções dentro de sua equipe de operações de segurança para conceder acesso apropriado ao Azure Sentinel. Com base nas funções, você tem um controle fino sobre o que os usuários com acesso ao Azure Sentinel podem ver. Você pode atribuir funções RBAC diretamente no espaço de trabalho do Azure Sentinel ou a um grupo de assinatura ou recurso ao qual o espaço de trabalho pertence.

Existem três funções específicas do Azure Sentinel.  
**Todas as funções incorporadas do Azure Sentinel concedem acesso à leitura dos dados no espaço de trabalho do Azure Sentinel.**
- [Leitor do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Colaborador do Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Além das funções RBAC dedicadas ao Azure Sentinel, existem funções RBAC do Azure e do Log Analytics que podem conceder um conjunto mais amplo de permissões que incluem acesso ao seu espaço de trabalho do Azure Sentinel e outros recursos:

- **Funções azure:** [Proprietário,](../role-based-access-control/built-in-roles.md#owner) [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)e [Leitor](../role-based-access-control/built-in-roles.md#reader). As funções do Azure concedem acesso a todos os seus recursos do Azure, incluindo espaços de trabalho do Log Analytics e recursos do Azure Sentinel.

-   **Funções do Log Analytics:** [colaborador do Log Analytics,](../role-based-access-control/built-in-roles.md#log-analytics-contributor)leitor de Log [Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). As funções do Log Analytics concedem acesso em todos os espaços de trabalho do Log Analytics. 

> [!NOTE]
> As funções do Log Analytics também concedem acesso à leitura em todos os recursos do Azure, mas apenas atribuirá permissões de gravação aos recursos do Log Analytics.


Por exemplo, um usuário que é atribuído com **o leitor do Azure Sentinel** e **funções de contribuinte do Azure** Sentinel (não **colaborador do Azure Sentinel),** poderá editar dados no Azure Sentinel, embora eles só tenham permissões **de leitor do Sentinel.** Portanto, se você quiser conceder permissões a um único no Azure Sentinel, você deve remover cuidadosamente as permissões anteriores deste usuário, certificando-se de que você não quebre nenhuma função de permissão necessária para outro recurso.

> [!NOTE]
>- O Azure Sentinel usa cartilhas para resposta automatizada a ameaças. As playbooks aproveitam os aplicativos azure logic e são um recurso separado do Azure. Você pode querer atribuir a membros específicos de sua equipe de operações de segurança a opção de usar apps lógicos para operações de orquestração de segurança, automação e resposta (SOAR). Você pode usar a função de contribuinte do [App Lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor) ou a função de operador do App [Lógico](../role-based-access-control/built-in-roles.md#logic-app-operator) para atribuir permissão explícita para usar livros de jogadas.
>- Para adicionar conectores de dados, as funções necessárias para cada conector são por tipo de conector e estão listadas na página de conector relevante. Além disso, para conectar qualquer fonte de dados, você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir exibe funções e ações permitidas no Azure Sentinel. Um X indica que a ação é permitida para essa função.

| Função | Crie e execute cartilhas| Crie e edite dashboards, regras analíticas e outros recursos do Azure Sentinel | Gerenciar incidentes (demitir, atribuir, etc.) | Exibir dados, incidentes, dashboards e outros recursos do Azure Sentinel |
|--- |---|---|---|---|
| Leitor do Azure Sentinel | -- | -- | -- | X |
| Azure Sentinel responder|--|--| X | X |
| Colaborador do Azure Sentinel | -- | X | X | X |
| Colaborador do Azure Sentinel + Colaborador do App Logic | X | X | X | X |


> [!NOTE]
> - Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, atribua a função de contribuinte do Azure Sentinel apenas aos usuários que precisam criar regras ou dashboards.
> - Recomendamos que você defina permissões para o Azure Sentinel no escopo do grupo de recursos, para que o usuário possa ter acesso a todos os espaços de trabalho do Azure Sentinel no mesmo grupo de recursos.
>
## <a name="building-custom-rbac-roles"></a>Criação de funções RBAC

Além de, ou em vez de, usar funções RBAC incorporadas, você pode criar funções RBAC personalizadas para o Azure Sentinel. As funções RBAC personalizadas para o Azure Sentinel são criadas da mesma forma que você cria outras funções [personalizadas do Azure RBAC,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) com base em [permissões específicas para o Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e para [os recursos do Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>RBAC avançado sobre os dados que você armazena no Azure Sentinel
  
Você pode usar o controle avançado de acesso baseado em função do Log Analytics através dos dados no espaço de trabalho do Azure Sentinel. Isso inclui tanto o controle de acesso baseado em função por tipo de dados quanto o controle de acesso baseado em função centrado em recursos. Para obter mais informações sobre as funções do Log Analytics, consulte [Gerenciar dados de log e espaços de trabalho no Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a trabalhar com funções para usuários do Azure Sentinel e o que cada função permite que os usuários façam.

* [Blog do Azure Sentinel](https://aka.ms/azuresentinelblog). Encontre postagens no blog sobre a conformidade e segurança do Azure.
