---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299406"
---
|Nome |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Essa política ajuda a auditar se o serviço de Backup do Azure está habilitado para todas as máquinas virtuais. O Backup do Azure é uma solução de backup econômica e com um clique que simplifica a recuperação de dados e é mais fácil de habilitar do que outros serviços de backup em nuvem. |AuditIfNotExists, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Configurar o backup em VMs de um local para um cofre central existente no mesmo local](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Essa política configura a proteção do Backup do Azure em VMs em um determinado local para um cofre central existente no mesmo local. Ele se aplica apenas às VMs que ainda não estão configuradas para backup. É recomendável que essa política seja atribuída a no máximo 200 VMs. Se a política for atribuída a mais de 200 VMs, isso poderá fazer com que o backup seja disparado algumas horas além do agendamento definido. Essa política será aprimorada para dar suporte a mais imagens de VM. |deployIfNotExists, auditIfNotExists, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
