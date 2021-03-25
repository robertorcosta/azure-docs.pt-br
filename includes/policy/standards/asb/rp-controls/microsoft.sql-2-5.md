---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e6aaab50b549f0bf5c971c0e05c83b84a8b379b
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104487"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os SQL Servers com auditoria para destino da conta de armazenamento devem ser configurados com 90 dias de retenção ou mais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Para fins de investigação de incidentes, é recomendável definir a retenção de dados para a auditoria do SQL Server para o destino da conta de armazenamento para pelo menos 90 dias. Confirme que você está atendendo às regras de retenção necessárias para as regiões em que você está operando. Às vezes, isso é necessário para que você tenha conformidade com os padrões regulatórios. |AuditIfNotExists, desabilitado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
