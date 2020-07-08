---
title: Controles de segurança
description: Uma lista de verificação de controles de segurança para avaliar o banco de dados SQL do Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97d2cd8e9ba51e4fc6ebab8459b04f4f37e876d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668416"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Controles de segurança para o banco de dados SQL do Azure e o SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo documenta os controles de segurança que são criados no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Aplica-se somente ao [banco de dados SQL](../index.yml) . |
| Suporte à injeção de rede virtual do Azure| Sim | Aplica-se somente ao [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Isolamento de rede e suporte a firewall| Sim | Firewall no nível do banco de dados e do servidor. O isolamento de rede é somente para o [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Suporte a túnel forçado| Sim | O [SQL instância gerenciada](../managed-instance/sql-managed-instance-paas-overview.md) por meio de uma VPN de [ExpressRoute](../expressroute/../index.yml) . |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure, como Log Analytics ou Application Insights| Sim | O SecureSphere, a solução SIEM da Imperva, também tem suporte por meio da integração dos [hubs de eventos do Azure](../event-hubs/../index.yml) por meio da [auditoria do SQL](../../azure-sql/database/auditing-overview.md). |
| Log e auditoria do plano de controle e do plano de gerenciamento| Sim | Sim apenas para alguns eventos |
| Log e auditoria do plano de dados | Sim | Por meio da [auditoria do SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Active Directory do Azure (Azure AD) |
| Autorização| Sim | Nenhum |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Chamado "criptografia em uso", conforme descrito no artigo [Always Encrypted](always-encrypted-certificate-store-configure.md). A criptografia do lado do servidor usa a [Transparent Data Encryption](transparent-data-encryption-tde-overview.md).|
| Criptografia em trânsito:<ul><li>Criptografia do Azure ExpressRoute</li><li>Criptografia em uma rede virtual</li><li>Criptografia entre redes virtuais</ul>| Sim | Usar HTTPS. |
| Criptografia – manipulação de chaves, como CMK ou BYOK| Sim | O tratamento de chaves gerenciado pelo serviço e gerenciado pelo cliente é oferecido. O último é oferecido por meio de [Azure Key Vault](../key-vault/../index.yml). |
| Criptografia em nível de coluna fornecida pelos serviços de dados do Azure| Sim | Por meio de [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Chamadas de API criptografadas| Sim | Usando HTTPS/TLS. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração, como o controle de versão da configuração| Não  | Nenhum |

## <a name="additional-security-controls-for-sql-database"></a>Controles de segurança adicionais para o banco de dados SQL

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Preventivo: avaliação de vulnerabilidade | Sim | Consulte [serviço de avaliação de vulnerabilidade do SQL ajuda a identificar vulnerabilidades de banco de dados](sql-vulnerability-assessment.md). |
| Preventivo: descoberta e classificação de dados  | Sim | Consulte [classificação de & do banco de dados SQL do Azure e SQL data warehouse a descoberta de data](data-discovery-and-classification-overview.md). |
| Detecção: detecção de ameaças | Sim | Consulte [proteção avançada contra ameaças para o banco de dados SQL do Azure](threat-detection-overview.md). |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../../security/fundamentals/security-controls.md).
