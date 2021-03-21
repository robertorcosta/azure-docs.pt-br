---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5f89c1236b1c794bb49e4eff667cb77b8d1ea08e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605040"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O ponto de extremidade do serviço de bot deve ser um URI HTTPS válido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Os dados podem ser adulterados durante a transmissão. Existem protocolos que fornecem criptografia para resolver problemas de uso indevido e adulteração. Para garantir que seus bots estejam se comunicando apenas por meio de canais criptografados, defina o ponto de extremidade para um URI HTTPS válido. Isso garante que o protocolo HTTPS seja usado para criptografar seus dados em trânsito e, muitas vezes, é um requisito de conformidade com padrões regulatórios ou do setor. Acesse: [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |auditar, negar, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[O Serviço de Bot deve ser criptografado com uma chave gerenciada pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |O Serviço de Bot do Azure criptografa automaticamente o seu recurso para proteger os seus dados e atender aos compromissos de conformidade e segurança da organização. Por padrão, são usadas as chaves de criptografia gerenciadas pela Microsoft. Para obter mais flexibilidade no gerenciamento de chaves ou no controle de acesso à sua assinatura, selecione as chaves gerenciadas pelo cliente, também conhecidas como BYOK (Bring Your Own Key). Saiba mais sobre a criptografia do Serviço de Bot do Azure: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption). |auditar, negar, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
