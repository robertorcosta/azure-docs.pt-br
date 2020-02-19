---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170124"
---
|Nome |Descrição |Efeito(s) |Versão |
|---|---|---|---|
|[Aplicar as Configurações de Diagnóstico do Key Vault no Hub de Eventos](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Implanta as configurações de diagnóstico do Key Vault a serem transmitidas para um Hub de Eventos regional em qualquer Key Vault criado ou atualizado que não tenha essas configurações de diagnóstico. |deployIfNotExists |1.0.0 |
|[Os logs de diagnóstico no Key Vault devem estar habilitados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Habilitação da auditoria de logs de diagnóstico. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |1.0.0 |
|[Os objetos do Key Vault devem ser recuperáveis](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Essa política audita se os objetos do cofre de chaves não são recuperáveis. O recurso de exclusão reversível ajuda a efetivamente manter os recursos por um período de retenção especificado (90 dias) mesmo após a operação EXCLUIR, ao mesmo tempo aparentando ter excluído o objeto. Quando a proteção de limpeza está ativada, um cofre ou um objeto no estado excluído não pode ser limpo até que tenha passado o período de retenção de 90 dias. Esses cofres e objetos ainda podem ser recuperados, garantindo aos clientes que a política de retenção será seguida. |Audit, desabilitado |1.0.0 |
