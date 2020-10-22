---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 40695d07ebece67011b299863dbd6da9a03f1ebe
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315506"
---
## <a name="azure-security-benchmark"></a>Azure Security Benchmark

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. Para ver como esse serviço é mapeado completamente para o Azure Security Benchmark, confira os [arquivos de mapeamento do Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para examinar como os internos do Azure Policy disponíveis para todos os serviços do Azure são mapeados para esse padrão de conformidade, confira [Conformidade Regulatória do Azure Policy – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de Controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Recuperação de dados |9.1 |garantir backups automatizados regulares |[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Recuperação de dados |9.2 |realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente |[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Para examinar como as iniciativas internas disponíveis do Azure Policy de todos os serviços do Azure são mapeadas para esse padrão de conformidade, confira [Conformidade Regulatória do Azure Policy – HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Para obter mais informações sobre esse padrão de conformidade, confira [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domínio |ID do controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Backup |1620.09l1Organizational.8 – 09.l |Quando o serviço de backup é fornecido por terceiros, o SLA inclui as proteções detalhadas para controlar a confidencialidade, a integridade e a disponibilidade das informações de backup. |[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Backup |1625.09l3Organizational.34 – 09.l |Três (3) gerações de backups (completo, além de todos os backups incrementais ou diferenciais relacionados) são armazenados fora do site, e os backups no site e fora dele são registrados com nome, data, hora e ação. |[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|Backup |1699.09l1Organizational.10 – 09.l |As funções e as responsabilidades dos membros da força de trabalho no processo de backup de dados são identificadas e comunicadas à força de trabalho; em particular, os usuários BYOD (Traga seu próprio dispositivo) devem executar backups de dados organizacionais e/ou do cliente nos próprios dispositivos. |[O Backup do Azure deve ser habilitado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

