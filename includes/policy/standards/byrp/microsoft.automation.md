---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478098"
---
## <a name="azure-security-benchmark"></a>Azure Security Benchmark

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. Para ver como esse serviço é mapeado completamente para o Azure Security Benchmark, confira os [arquivos de mapeamento do Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para examinar como os internos do Azure Policy disponíveis para todos os serviços do Azure são mapeados para esse padrão de conformidade, confira [Conformidade Regulatória do Azure Policy – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de Controle |Título do controle |Política<br /><sub>(Portal do Azure)</sub> |Versão da política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de dados |4.8 |criptografar informações confidenciais em repouso |[As variáveis da conta de automação devem ser criptografadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Ao criar uma variável da conta de Automação, você pode especificar sua criptografia e armazenamento pela Automação do Azure como um ativo protegido. Após criar a variável, você não pode alterar seu status de criptografia sem recriá-la. Se tiver variáveis de conta de Automação que armazenam dados confidenciais que ainda não foram criptografados, você precisará excluí-los e recriá-los como variáveis criptografadas. Uma recomendação da Central de Segurança do Azure é criptografar todas as variáveis de Automação do Azure, conforme descrito em [As variáveis da conta de automação devem ser criptografadas](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Se você tiver variáveis não criptografadas que deseja excluir dessa recomendação de segurança, confira [Isentar um recurso das recomendações e da classificação de segurança](../../../../articles/security-center/exempt-resource.md) para criar uma regra de isenção.