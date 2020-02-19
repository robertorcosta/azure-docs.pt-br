---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169880"
---
|Nome |Descrição |Efeito(s) |Versão |
|---|---|---|---|
|[Locais permitidos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Esta política permite que você restrinja os locais que sua organização pode especificar ao implantar recursos. Use para impor seus requisitos de conformidade geográfica. Exclui grupos de recursos, Microsoft.AzureActiveDirectory/b2cDirectories e recursos que usam a região "global". |deny |1.0.0 |
|[Localizações permitidas para grupos de recursos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Esta política permite que você restrinja os locais em que sua organização pode criar grupos de recursos. Use para impor seus requisitos de conformidade geográfica. |deny |1.0.0 |
|[Tipos de recursos permitidos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Essa política permite especificar os tipos de recursos que sua organização pode implantar. Somente os tipos de recursos que dão suporte a "marcas" e "local" serão afetados por essa política. Para restringir todos os recursos, duplique essa política e altere o "modo" para "Todos". |deny |1.0.0 |
|[O local do recurso de auditoria corresponde ao local do grupo de recursos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Auditar se o local do recurso corresponde ao local do seu grupo de recursos |auditoria |1.0.0 |
|[Auditar o uso de regras personalizadas do RBAC](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Auditar funções internas, como 'Proprietário, Contribuidor, Leitor', em vez de funções RBAC personalizadas, que são propensas a erros. O uso de funções personalizadas é tratado como uma exceção e requer uma revisão rigorosa e uma modelagem de ameaças |Audit, desabilitado |1.0.0 |
|[Não devem existir funções personalizadas de proprietário de assinatura](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Essa política garante que não exista nenhuma função personalizada de proprietário de assinatura. |Audit, desabilitado |1.0.0 |
|[Tipos de recursos não permitidos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Essa política permite especificar os tipos de recursos que sua organização não pode implantar. |Negar |1.0.0 |
