---
title: Controle de Segurança Do Azure - Proteção de Dados
description: Proteção de dados do Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408554"
---
# <a name="security-control-data-protection"></a>Controle de Segurança: Proteção de Dados

As recomendações de proteção de dados se concentram em abordar problemas relacionados à criptografia, listas de controle de acesso, controle de acesso baseado em identidade e registro de auditoria para acesso a dados.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.1 | 13.1 | Cliente |

Use tags para ajudar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas armazenando ou processando informações confidenciais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.2 | 13.2, 2.10 | Cliente |

Implementar isolamento usando assinaturas separadas e grupos de gerenciamento para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes corporativos exigem. Você pode controlar o acesso aos recursos do Azure através do controle de acesso baseado em função do Azure Active Directory. 

- [Como criar assinaturas adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorar e bloquear a transferência não autorizada de informações confidenciais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.3 | 13.3 | Compartilhado |

Aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia essas transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e protege contra a perda e exposição de dados do cliente. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

- [Entenda a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografe todas as informações confidenciais em trânsito

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.4 | 14.4 | Compartilhado |

Criptografe todas as informações confidenciais em trânsito. Certifique-se de que todos os clientes que se conectam aos seus recursos do Azure sejam capazes de negociar TLS 1.2 ou superior.

Siga as recomendações do Azure Security Center para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Entenda a criptografia em trânsito com o Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Use uma ferramenta de descoberta ativa para identificar dados confidenciais

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.5 | 14.5 | Compartilhado |

Quando nenhum recurso estiver disponível para o seu serviço específico no Azure, use uma ferramenta de detecção ativa de terceiros para identificar todas as informações confidenciais armazenadas, processadas ou transmitidas pelos sistemas de tecnologia da organização, incluindo aquelas localizadas no local ou em um provedor de serviços remotos, e atualize o inventário de informações confidenciais da organização.

Use o Azure Information Protection para identificar informações confidenciais dentro dos documentos do Office 365.

Use o Azure SQL Information Protection para auxiliar na classificação e rotulagem das informações armazenadas nos bancos de dados SQL do Azure.

- [Como implementar o Azure SQL Data Discovery](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Como implementar a Proteção de Informações do Azure](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Entenda a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Use o controle de acesso baseado em função para controlar o acesso aos recursos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.6 | 14.6 | Cliente |

Use o Azure AD RBAC para controlar o acesso a dados e recursos, de outra forma use métodos de controle de acesso específicos do serviço.

- [Como configurar o RBAC no Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Use a prevenção de perda de dados baseada em host para reforçar o controle de acesso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.7 | 14.7 | Compartilhado |

Se necessário para a conformidade com recursos computacionais, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perda de dados baseada em host, para impor controles de acesso aos dados mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como sensível e se esforça muito para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles e recursos robustos de proteção de dados.

- [Entenda a proteção de dados do cliente no Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informações confidenciais em repouso

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.8 | 14.8 | Cliente |

Use criptografia em repouso em todos os recursos do Azure. A Microsoft recomenda permitir que o Azure gerencie suas chaves de criptografia, no entanto, há a opção de você gerenciar suas próprias chaves em alguns casos. 

- [Entenda a criptografia em repouso no Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registre e alerte sobre mudanças nos recursos críticos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 4.9 | 14.9 | Cliente |

Use o Azure Monitor com o Azure Activity Log para criar alertas para quando as alterações ocorrerem para recursos críticos do Azure.

- [Como criar alertas para eventos do Azure Activity Log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Próximas etapas

- Veja o próximo Controle de Segurança: [Gerenciamento de Vulnerabilidades](security-control-vulnerability-management.md)