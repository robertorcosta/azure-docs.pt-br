---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 949979afb3c2cc0d04dc1e9b189570e5bf1f2c07
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99213700"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço do Azure SignalR deve usar o link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |O Link Privado do Azure permite que você conecte sua rede virtual aos serviços do Azure sem um endereço IP público na origem ou no destino. A plataforma de link privado manipula a conectividade entre o consumidor e os serviços na rede de backbone do Azure. Por meio do mapeamento de pontos de extremidade privados para seus recursos do SignalR, em vez de todo o serviço, você também estará protegido contra riscos de vazamento de dados. Saiba mais em: [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink). |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
