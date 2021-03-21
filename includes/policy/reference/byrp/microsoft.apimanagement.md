---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 603a06128d6ac54c9a336b4f58503efad13434fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605844"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O serviço de gerenciamento de API deve usar um SKU que dê suporte a redes virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Com as SKUs com suporte do gerenciamento de API, a implantação de serviço em uma rede virtual desbloqueia os recursos de segurança e rede de gerenciamento de API avançados, o que proporciona maior controle sobre a configuração de segurança de rede. Saiba mais em: [https://aka.ms/apimvnet](https://aka.ms/apimvnet). |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[Os serviços do Gerenciamento de API devem usar uma rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |A implantação da Rede Virtual do Azure fornece isolamento e segurança aprimorada e permite que você coloque o serviço de Gerenciamento de API em uma rede roteável não ligada à Internet para a qual você controla o acesso. Essas redes podem ser conectadas às suas redes locais usando várias tecnologias de VPN, o que permite o acesso aos seus serviços de back-end na rede e/ou locais. O portal do desenvolvedor e o gateway de API podem ser configurados para serem acessados pela Internet ou somente na rede virtual. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
