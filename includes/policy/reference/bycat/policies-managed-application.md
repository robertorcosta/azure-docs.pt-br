---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: befe81f1c5d40e3bab8c7f0d02ff993f0217549e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818667"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A definição de aplicativo para o Aplicativo Gerenciado deve usar a conta de armazenamento fornecida pelo cliente](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Use a sua conta de armazenamento para controlar os dados de definição de aplicativo quando ele for um requisito regulamentar ou de conformidade. É possível optar por armazenar a definição de aplicativo gerenciado em uma conta de armazenamento fornecida por você durante a criação, para que a localização dessa definição e o acesso a ela possam ser totalmente gerenciados por você para cumprir os requisitos de conformidade regulamentar. |auditar, negar, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Implantar associações para um aplicativo gerenciado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Implanta um recurso de associação que associa os tipos de recursos selecionados ao aplicativo gerenciado especificado.  Essa implantação de política não dá suporte a tipos de recurso aninhados. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
