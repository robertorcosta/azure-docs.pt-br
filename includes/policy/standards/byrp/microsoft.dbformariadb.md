---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e7fcbba4eb5f9a44e5819d88e5bbb5e77a258470
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488466"
---
## <a name="azure-security-benchmark"></a>Azure Security Benchmark

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. Para ver como esse serviço é mapeado completamente para o Azure Security Benchmark, confira os [arquivos de mapeamento do Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para examinar como as iniciativas internas disponíveis do Azure Policy de todos os serviços do Azure são mapeadas para esse padrão de conformidade, confira [Conformidade regulatória do Azure Policy – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID do controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Segurança de rede |1,1 |proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual |[O ponto de extremidade privado deve ser habilitado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|Recuperação de dados |9.1 |garantir backups automatizados regulares |[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Recuperação de dados |9.2 |realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente |[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Para examinar como as iniciativas internas disponíveis do Azure Policy de todos os serviços do Azure são mapeadas para esse padrão de conformidade, confira [Conformidade Regulatória do Azure Policy – HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Para obter mais informações sobre esse padrão de conformidade, confira [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domínio |ID do controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Backup |1619.09l1Organizational.7 – 09.l |Os registros de inventário das cópias de backup, incluindo o conteúdo e a localização atual, são mantidos. |[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Backup |1624.09l3Organizational.12 – 09.l |A organização executa backups incrementais ou diferenciais diários e completos semanalmente para separar a mídia. |[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Backup |1627.09l3Organizational.6 – 09.l |A organização testa as informações de backup seguindo cada backup para verificar a confiabilidade da mídia e a integridade das informações, pelo menos, anualmente. |[O backup com redundância geográfica deve ser habilitado para o Banco de Dados do Azure para MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

