---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ba20a64c6eca4f98cd871f50078512d79952b75f
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819833"
---
## <a name="azure-security-benchmark"></a>Azure Security Benchmark

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. Para ver como esse serviço é mapeado completamente para o Azure Security Benchmark, confira os [arquivos de mapeamento do Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para examinar como os internos do Azure Policy disponíveis para todos os serviços do Azure são mapeados para esse padrão de conformidade, confira [Conformidade Regulatória do Azure Policy – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de Controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de dados |4.6 |usar o RBAC do Azure para controlar o acesso a recursos |[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Para examinar como os itens internos do Azure Policy disponíveis para todos os serviços do Azure são mapeados para esse padrão de conformidade, confira [Conformidade Regulatória do Azure Policy – CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Para saber mais sobre esse padrão de conformidade, confira [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domínio |ID de Controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gerenciamento de Identidades e Acesso |1.23 |Garantir que nenhuma função personalizada de proprietário de assinatura seja criada |[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Para examinar como as iniciativas internas disponíveis do Azure Policy de todos os serviços do Azure são mapeadas para esse padrão de conformidade, confira [Conformidade regulatória do Azure Policy – HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Para obter mais informações sobre esse padrão de conformidade, confira [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domínio |ID de Controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gerenciamento de privilégios |1148.01c2System.78 – 01.c |A organização restringe o acesso a funções privilegiadas e a todas as informações relevantes de segurança. |[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Diferenciação de tarefas |1230.09c2Organizational.1 – 09.c |Nenhuma pessoa pode acessar, modificar ou usar sistemas de informações sem autorização ou detecção. |[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Diferenciação de tarefas |1276.09c2Organizational.2 – 09.c |As atividades de auditoria de segurança são independentes. |[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|Diferenciação de tarefas |1278.09c2Organizational.56 – 09.c |A organização identifica os direitos que exigem separação e define as autorizações de acesso do sistema de informações para dar suporte à separação de tarefas; as tarefas incompatíveis são separadas por vários usuários para minimizar a oportunidade de uso indevido ou fraude. |[Não devem existir funções personalizadas de proprietário de assinatura](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Para examinar como as iniciativas internas disponíveis do Azure Policy de todos os serviços do Azure são mapeadas para esse padrão de conformidade, confira [Conformidade regulatória do Azure Policy – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Para obter mais informações sobre esse padrão de conformidade, confira [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domínio |ID do controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controle de acesso |AC-2 (7) |Gerenciamento de conta \| Esquemas baseados em função |[Auditar o uso de regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

