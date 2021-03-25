---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 933577fc80c3d8966d8623d7d9bde2d4f83b2dc7
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035499"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Implantar as Configurações de Diagnóstico do Data Lake Storage Gen1 no Hub de Eventos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8d096bc-85de-4c5f-8cfb-857bd1b9d62d) |Implanta as configurações de diagnóstico do Data Lake Storage Gen1 a serem transmitidas para um Hub de Eventos regional em qualquer Data Lake Storage Gen1 criado ou atualizado que não tenha essas configurações de diagnóstico. |DeployIfNotExists, desabilitado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Implantar as Configurações de Diagnóstico do Data Lake Storage Gen1 no workspace do Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25763a0a-5783-4f14-969e-79d4933eb74b) |Implanta as configurações de diagnóstico do Data Lake Storage Gen1 a serem transmitidas para um workspace do Log Analytics regional em qualquer Data Lake Storage Gen1 criado ou atualizado que não tenha essas configurações de diagnóstico. |DeployIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Exigir a criptografia em contas do Data Lake Storage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Essa política garantirá que a criptografia esteja habilitada em todas as contas do Data Lake Storage |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[Os logs de recurso no Azure Data Lake Storage devem estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Auditar a habilitação de logs de recurso. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
