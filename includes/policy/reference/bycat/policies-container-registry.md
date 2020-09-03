---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8920752a6adfe5f3259c4bf701173d2d817aa7da
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291000"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os Registros de Contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Audite os Registros de Contêiner que não têm a criptografia habilitada com CMK (chaves gerenciadas pelo cliente). Para obter mais informações sobre a criptografia CMK, visite: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os Registros de Contêiner não devem permitir acesso irrestrito à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Audite os Registros de Contêiner que não têm nenhuma regra de rede (IP ou VNET) configurada e permitem todo o acesso à rede por padrão. Os Registros de Contêiner com, pelo menos, uma regra de IP/firewall ou uma rede virtual configurada serão considerados em conformidade. Para obter mais informações sobre regras de Rede do Registro de Contêiner, visite: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os Registros de Contêiner devem usar links privados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Faça a auditoria de Registros de Contêiner que não têm pelo menos uma conexão de ponto de extremidade privado aprovada. Os clientes em uma rede virtual podem acessar com segurança recursos que têm conexões de ponto de extremidade privado por meio de links privados. Para obter mais informações, visite: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
