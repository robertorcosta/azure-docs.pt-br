---
title: Controle de segurança do Azure-proteção de dados
description: Proteção de dados do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d89320807c6322120490db85100453edf593aded
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045528"
---
# <a name="security-control-data-protection"></a>Controle de segurança: proteção de dados

As recomendações de proteção de dados concentram-se em resolver problemas relacionados à criptografia, listas de controle de acesso, controle de acesso baseado em identidade e log de auditoria para acesso a dados.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.1 | 13.1 | Cliente |

Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.2 | 13,2, 2,10 | Cliente |

Implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure por meio do Azure RBAC (controle de acesso baseado em função). 

- [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar marcas](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.3 | 13,3 | Compartilhado |

Aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia essas transferências ao alertar os profissionais de segurança de informações.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.4 | 14,4 | Compartilhado |

Criptografe todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam aos recursos do Azure são capazes de negociar o TLS 1,2 ou superior.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Entender a criptografia em trânsito com o Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.5 | 14,5 | Compartilhado |

Quando nenhum recurso está disponível para seu serviço específico no Azure, use uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas, processadas ou transmitidas pelos sistemas de tecnologia da organização, incluindo aquelas localizadas no local, ou em um provedor de serviços remoto, e atualize o inventário de informações confidenciais da organização.

Use a proteção de informações do Azure para identificar informações confidenciais nos documentos do Office 365.

Use a proteção de informações do SQL do Azure para auxiliar na classificação e no rotulamento de informações armazenadas no banco de dados SQL do Azure.

- [Como implementar a descoberta de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Como implementar a proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso baseado em função para controlar o acesso aos recursos

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.6 | 14,6 | Cliente |

Use o RBAC do Azure AD para controlar o acesso a dados e recursos, caso contrário, use métodos de controle de acesso específicos de serviço.

- [Como configurar o RBAC no Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.7 | 14,7 | Compartilhado |

Se necessário para a conformidade em recursos de computação, implemente uma ferramenta de terceiros, como uma solução de prevenção contra perda de dados baseada em host automatizada, para impor controles de acesso aos dados, mesmo quando os dados são copiados fora de um sistema.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4.8 | 14,8 | Cliente |

Use a criptografia em repouso em todos os recursos do Azure. A Microsoft recomenda permitir que o Azure gerencie suas chaves de criptografia, no entanto, há a opção de gerenciar suas próprias chaves em algumas instâncias. 

- [Entender a criptografia em repouso no Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 4,9 | 14,9 | Cliente |

Use Azure Monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para os recursos críticos do Azure.

- [Como criar alertas para eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança: [Gerenciamento de vulnerabilidades](security-control-vulnerability-management.md)