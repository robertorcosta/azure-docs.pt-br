---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170094"
---
|Nome |Descrição |Efeito(s) |Versão |
|---|---|---|---|
|[SKUs de máquinas virtuais permitidas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Esta política permite especificar um conjunto de SKUs de máquina virtual que sua organização pode implantar. |Negar |1.0.0 |
|[Auditar máquinas virtuais sem a recuperação de desastre configurada](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Audite máquinas virtuais sem a recuperação de desastre configurada. Para saber mais sobre recuperação de desastre, acesse https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Auditar VMs que não usam discos gerenciados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Essa política audita VMs que não usam discos gerenciados |auditoria |1.0.0 |
|[Implantar a extensão padrão antimalware de IaaS da Microsoft para Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Essa política implanta uma extensão IaaSAntimalware da Microsoft com uma configuração padrão quando uma VM não está configurada com a extensão antimalware. |deployIfNotExists |1.0.0 |
|[Os logs de diagnóstico nos Conjuntos de Dimensionamento de Máquinas Virtuais devem ser habilitados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Recomenda-se habilitar os Logs para que a trilha de atividades possa ser recriada quando investigações forem necessárias no caso de um incidente ou um comprometimento. |AuditIfNotExists, desabilitado |1.0.0 |
|[O Microsoft Antimalware para o Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Essa política audita qualquer máquina virtual do Windows não configurada com a atualização automática das assinaturas de proteção Microsoft Antimalware. |AuditIfNotExists, desabilitado |1.0.0 |
|[A extensão IaaSAntimalware da Microsoft deve ser implantada em servidores do Windows](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Essa política audita qualquer VM de servidor do Windows sem a extensão IaaSAntimalware da Microsoft implantada. |AuditIfNotExists, desabilitado |1.0.0 |
|[Somente as extensões aprovadas da VM devem ser instaladas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Essa política rege as extensões da máquina virtual que não foram aprovadas. |Audit, Deny, desabilitado |1.0.0 |
|[Exigir a aplicação automática de patch da imagem do sistema operacional em Conjuntos de Dimensionamento de Máquinas Virtuais](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Essa política impõe a habilitação do patch automático da imagem do sistema operacional nos Conjuntos de Dimensionamento de Máquinas Virtuais para que as máquinas virtuais estejam sempre protegidas por meio da aplicação segura dos patches de segurança mais recentes mensalmente. |deny |1.0.0 |
|[Os discos desconectados devem ser criptografados](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Essa política audita qualquer disco desconectado sem a criptografia habilitada. |Audit, desabilitado |1.0.0 |
|[As máquinas virtuais devem ser migradas para os novos recursos do Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Use o novo Azure Resource Manager para que suas máquinas virtuais forneçam aprimoramentos de segurança, como: controle de acesso (RBCA) mais forte, melhor auditoria, governança e implantação baseadas no ARM, acesso a identidades gerenciadas, acesso ao cofre de chaves com segredos, autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento de segurança |Audit, Deny, desabilitado |1.0.0 |
