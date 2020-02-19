---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170284"
---
|Nome |Descrição |Efeito(s) |Versão |
|---|---|---|---|
|[SKUs de conta de armazenamento permitidas](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Essa política permite especificar um conjunto de SKUs de conta de armazenamento que sua organização pode implantar. |Negar |1.0.0 |
|[Auditar o acesso irrestrito à rede para contas de armazenamento](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Audite o acesso irrestrito à rede nas configurações de firewall da conta de armazenamento. Em vez disso, configure as regras de rede de forma que somente aplicativos das redes permitidas podem acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da internet, o acesso pode ser concedido para o tráfego de redes virtuais do Azure específicas ou para intervalos de endereços IP públicos da internet |Audit, desabilitado |1.0.0 |
|[Implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Essa política habilita a Proteção Avançada contra Ameaças nas Contas de Armazenamento. |DeployIfNotExists, desabilitado |1.0.0 |
|[O armazenamento com redundância geográfica deve ser habilitado para Contas de Armazenamento](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Essa política audita qualquer Conta de Armazenamento em que a opção de armazenamento com redundância geográfica não está habilitada. |Audit, desabilitado |1.0.0 |
|[A transferência segura para contas de armazenamento deve ser habilitada](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Exigência de auditoria de transferência segura em sua conta de armazenamento. A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão |Audit, Deny, desabilitado |1.0.0 |
|[As contas de armazenamento devem permitir o acesso de serviços confiáveis da Microsoft](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Alguns serviços da Microsoft que interagem com contas de armazenamento operam a partir de redes que não podem ter o acesso concedido por meio de regras de rede. Para ajudar esse tipo de serviço a funcionar como esperado, você pode permitir que o conjunto de serviços Microsoft confiáveis ignore as regras de rede. Esses serviços usarão então a autenticação forte para acessar a conta de armazenamento. |Audit, Deny, desabilitado |1.0.0 |
|[As contas de armazenamento devem ser migradas para os novos recursos do Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Use o novo Azure Resource Manager para suas contas de armazenamento para fornecer aprimoramentos de segurança, como: controle de acesso (RBAC) mais forte, melhor auditoria, implantação e controle com base no Azure Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves por segredos, autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento de segurança |Audit, Deny, desabilitado |1.0.0 |
