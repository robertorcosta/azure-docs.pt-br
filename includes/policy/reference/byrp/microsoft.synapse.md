---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296073"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os espaços de trabalho do Azure Synapse devem usar chaves gerenciadas pelo cliente para criptografar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Use chaves gerenciadas pelo cliente para controlar a criptografia em repouso dos dados armazenados nos espaços de trabalho do Azure Synapse. Chaves gerenciadas pelo cliente fornecem criptografia dupla adicionando uma segunda camada de criptografia sobre a criptografia padrão com chaves gerenciadas pelo serviço. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[As regras de firewall IP nos espaços de trabalho Synapse do Azure devem ser removidas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |Remover todas as regras de firewall de IP melhora a segurança garantindo que o espaço de trabalho Synapse do Azure só possa ser acessado de um ponto de extremidade privado. Essa configuração audita a criação de regras de firewall que permitem acesso à rede pública no espaço de trabalho. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[A rede virtual do espaço de trabalho gerenciado em espaços de trabalho Synapse do Azure deve ser habilitada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |Habilitar uma rede virtual de espaço de trabalho gerenciado garante que seu espaço de trabalho seja isolado da rede de outros espaços de trabalho. A integração de dados e os recursos do Spark implantados nessa rede virtual também fornecem isolamento no nível do usuário para atividades do Spark. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[As conexões de ponto de extremidade privado nos espaços de trabalho do Azure Synapse devem ser habilitadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Pontos de extremidade privados podem ser configurados para se conectarem de forma privada a um espaço de trabalho do Azure Synapse. Isso é usado para impor um canal de comunicação seguro ao espaço de trabalho Synapse do Azure. |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Pontos de extremidade privados gerenciados pelo Synapse só devem se conectar a recursos em locatários aprovados Azure Active Directory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Proteja seu espaço de trabalho do Synapse permitindo apenas conexões com recursos em locatários aprovados do Azure Active Directory (Azure AD). Os locatários aprovados do Azure AD podem ser definidos durante a atribuição da política. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
