---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d8d390256c44bc37070594596f627fe52f325c6a
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82941333"
---
|Nome |Descrição |Efeito(s) |Versão |GitHub |
|---|---|---|---|---|
|[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Esta política audita qualquer Banco de Dados do Azure para MariaDB em que a opção de backup com redundância geográfica de longo prazo não está habilitada. |Audit, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[O servidor MariaDB deve usar um ponto de extremidade de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Essa política audita servidores MariaDB que não estão configurados para usar um ponto de extremidade de serviço de rede virtual. Para obter mais detalhes, visite [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork). |AuditIfNotExists, desabilitado |1.0.1 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[O ponto de extremidade privado deve ser habilitado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Essa política audita servidores MariaDB que não estão configurados para usar um ponto de extremidade privado. Para obter mais detalhes, visite [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink). |AuditIfNotExists, desabilitado |1.0.1 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[O acesso à rede pública deve ser desabilitado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Essa política audita os servidores MariaDB em seu ambiente com o acesso à rede pública habilitado. Para obter mais detalhes, visite [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542). |Audit, desabilitado |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
