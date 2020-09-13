---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ab9ca05db0e6def896ec242df74ae531d33293bf
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022107"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garanta a proteção de suas máquinas virtuais do Azure habilitando o backup do Azure. O backup do Azure é uma solução de proteção de dados segura e econômica para o Azure. |AuditIfNotExists, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Configurar o backup em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Essa política configura a proteção do Backup do Azure em VMs em um determinado local para um cofre central existente no mesmo local. Ele se aplica apenas às VMs que ainda não estão configuradas para backup. É recomendável que essa política seja atribuída a no máximo 200 VMs. Se a política for atribuída a mais de 200 VMs, isso poderá fazer com que o backup seja disparado algumas horas além do agendamento definido. Essa política será aprimorada para dar suporte a mais imagens de VM. |deployIfNotExists, auditIfNotExists, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Implantar as configurações de diagnóstico do cofre dos Serviços de Recuperação no workspace do Log Analytics para categorias específicas do recurso.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Implante as configurações de diagnóstico do cofre dos Serviços de Recuperação para transmitir para o workspace do Log Analytics de categorias específicas do recurso. Se uma das categorias específicas do recurso não estiver habilitada, uma configuração de diagnóstico será criada. |deployIfNotExists |[1.0.1 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
