---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: 57cbd8d208422dfc124bed08109250e7bdd360d5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262134"
---
|Nome |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[Os Registros de Contêiner devem ser criptografados com uma CMK (chave gerenciada pelo cliente)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Audite os Registros de Contêiner que não têm a criptografia habilitada com CMK (chaves gerenciadas pelo cliente). Para obter mais informações sobre a criptografia CMK, visite: https://aka.ms/acr/CMK. |Audit, desabilitado |1.0.0 – versão prévia |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Os Registros de Contêiner não devem permitir acesso irrestrito à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Audite os Registros de Contêiner que não têm nenhuma regra de rede (IP ou VNET) configurada e permitem todo o acesso à rede por padrão. Os Registros de Contêiner com, pelo menos, uma regra de IP/firewall ou uma rede virtual configurada serão considerados em conformidade. Para obter mais informações sobre regras de rede do Registro de Contêiner, visite: https://aka.ms/acr/vnet. |Audit, desabilitado |1.0.0 – versão prévia |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
