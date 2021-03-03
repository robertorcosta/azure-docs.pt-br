---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90f4131a616d0cac93ac2a08999178ac7ea8a0eb
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "101728432"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O provisionamento automático do agente do Log Analytics deve ser habilitado na sua assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorar as vulnerabilidades e ameaças à segurança, a Central de Segurança do Azure coleta dados de suas máquinas virtuais do Azure. Os dados são coletados pelo agente do Log Analytics, anteriormente conhecido como MMA (Microsoft Monitoring Agent), que lê várias configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace do Log Analytics para análise. É recomendável habilitar o provisionamento automático para implantar automaticamente o agente em todas as VMs do Azure com suporte e nas VMs que forem criadas. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[O perfil de log do Azure Monitor deve coletar logs para as categorias "gravar", "excluir" e "ação"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Essa política garante que um perfil de log colete logs para as categorias "gravar", "excluir" e "ação" |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[O Azure Monitor deve coletar os logs de atividades de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Essa política audita o perfil de log do Azure Monitor que não exporta atividades de todas as regiões com suporte do Azure, incluindo global. |AuditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
