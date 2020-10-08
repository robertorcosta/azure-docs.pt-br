---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 52997495b5c46c89d3abfbb836f90d2363ee6d4f
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91820659"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registros de contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Faça auditoria dos registros de contêiner que não têm a criptografia habilitada com CMK (chaves gerenciadas pelo cliente). O Azure criptografa automaticamente o conteúdo do Registro em repouso com chaves gerenciadas pelo serviço. Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando uma chave criada e gerenciada por você no Azure Key Vault. Para obter mais informações sobre a criptografia CMK, visite: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Os registros de contêiner não devem permitir acesso irrestrito à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Faça auditoria dos registros de contêiner que não têm nenhuma regra de rede (IP) configurada e, assim, permitem todo o acesso à rede por padrão. Restringir o acesso à rede protege registros de contêiner contra possíveis ameaças. Os registros de contêiner com pelo menos uma regra de IP/firewall ou uma rede virtual configurada são considerados em conformidade. Para obter mais informações sobre regras de rede do Registro de Contêiner, acesse [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) e [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Os registros de contêiner devem usar links privados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Faça a auditoria de registros de contêiner que não têm pelo menos uma conexão de ponto de extremidade privado aprovada. Os clientes em uma rede virtual podem acessar com segurança recursos que têm conexões de ponto de extremidade privado por meio de links privados. O acesso público pode ser desabilitado para garantir que apenas links privados possam ser usados para se conectar ao Registro. Para obter mais informações, visite: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
