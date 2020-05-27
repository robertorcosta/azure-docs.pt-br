---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c60273cced46ba2afe536c004bf4cfc6f1eab646
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654809"
---
|Nome |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[Implantar as Configurações de Diagnóstico dos Aplicativos Lógicos no Hub de Eventos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1dae6c7-13f3-48ea-a149-ff8442661f60) |Implanta as configurações de diagnóstico dos Aplicativos Lógicos a serem transmitidas para um Hub de Eventos regional em quaisquer Aplicativos Lógicos criados ou atualizados que não tenham essas configurações de diagnóstico. |DeployIfNotExists, desabilitado |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogicApps_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Implantar as Configurações de Diagnóstico dos Aplicativos Lógicos ao workspace do Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb889a06c-ec72-4b03-910a-cb169ee18721) |Implanta as configurações de diagnóstico dos Aplicativos Lógicos a serem transmitidas para um workspace do Log Analytics regional em quaisquer Aplicativos Lógicos criados ou atualizados que não tenham essas configurações de diagnóstico. |DeployIfNotExists, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogicApps_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Habilitação da auditoria de logs de diagnóstico. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida |AuditIfNotExists, desabilitado |2.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
