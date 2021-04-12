---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4a86ac3ddd639bac7a04d9f2d3bddd40a0774ca2
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096131"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os modelos do Construtor de Imagens de VM devem usar o link privado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |O Link Privado do Azure permite que você conecte sua rede virtual aos serviços do Azure sem um endereço IP público na origem ou no destino. A plataforma de Link Privado manipula a conectividade entre o consumidor e os serviços na rede de backbone do Azure. Ao mapear pontos de extremidade privados para seus recursos de criação do Construtor de Imagens de VM, os riscos de vazamento de dados são reduzidos. Saiba mais sobre links privados em: [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet). |Auditoria, desabilitado, negação |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
