---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 44ece75ffeabc11bc047ee7ae3886fd5e263e81c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284258"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |O Banco de Dados do Azure para PostgreSQL permite que você escolha a opção de redundância para seu servidor de banco de dados. Ele pode ser definido como um armazenamento de backup com redundância geográfica no qual os dados não são armazenados apenas na região em que o servidor está hospedado, mas também é replicado para uma região emparelhada para dar a opção de recuperação em caso de falha de região. A configuração do armazenamento com redundância geográfica para backup só é permitida durante a criação do servidor. |Audit, desabilitado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
