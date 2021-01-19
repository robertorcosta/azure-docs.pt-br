---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d276774ab21563f584d03056ce670a6d01398908
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047060"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O ponto de extremidade do serviço de bot deve ser um URI HTTPS válido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Os dados podem ser adulterados durante a transmissão. Existem protocolos que fornecem criptografia para resolver problemas de uso indevido e adulteração. Para garantir que seus bots estejam se comunicando apenas por meio de canais criptografados, defina o ponto de extremidade para um URI HTTPS válido. Isso garante que o protocolo HTTPS seja usado para criptografar seus dados em trânsito e, muitas vezes, é um requisito de conformidade com padrões regulatórios ou do setor. Acesse: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |auditar, negar, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |
