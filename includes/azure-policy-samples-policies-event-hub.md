---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: aa9320ab4a2ff28c185ecef0f525376ceab4d875
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170194"
---
|Nome |Descrição |Efeito(s) |Versão |
|---|---|---|---|
|[Todas as regras de autorização, exceto a RootManageSharedAccessKey, devem ser removidas do namespace do Hub de Eventos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |Os clientes do Hub de Eventos não devem usar uma política de acesso no nível do namespace que forneça acesso a todas as filas e tópicos em um namespace. Para alinhar-se ao modelo de segurança com menos privilégios, você deve criar políticas de acesso no nível da entidade para que as filas e os tópicos forneçam acesso somente à entidade específica |Audit, Deny, desabilitado |1.0.1 |
|[As regras de autorização na instância do Hub de Eventos devem ser definidas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |Auditar a existência de regras de autorização em entidades do Hub de Eventos para conceder acesso com privilégios mínimos |AuditIfNotExists, desabilitado |1.0.0 |
|[Os logs de diagnóstico no Hub de Eventos devem ser habilitados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |Habilitação da auditoria de logs de diagnóstico. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |2.0.0 |
