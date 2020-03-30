---
title: Controles de segurança
description: Uma lista de verificação de controles de segurança para avaliar o Banco de Dados SQL do Azure
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190687"
---
# <a name="security-controls-for-azure-sql-database"></a>Controles de segurança para banco de dados SQL do Azure

Este artigo documenta os controles de segurança incorporados ao Banco de Dados SQL do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

O Banco de Dados SQL inclui [banco de dados único](sql-database-single-index.yml) e instância [gerenciada](sql-database-managed-instance.md). As seguintes entradas se aplicam a ambas as ofertas, exceto quando anotadas de outra forma.

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| Sim | Aplica-se apenas a [um banco de dados](sql-database-single-index.yml) único. |
| Suporte a injeção de rede virtual do Azure| Sim | Aplica-se apenas à [instância gerenciada.](sql-database-managed-instance.md) |
| Isolamento de rede e suporte a firewall| Sim | Firewall no nível do banco de dados e do servidor. O isolamento da rede é apenas para [instância gerenciada.](sql-database-managed-instance.md) |
| Suporte forçado de tunelamento| Sim | [Instância gerenciada](sql-database-managed-instance.md) através de uma [VPN ExpressRoute.](../expressroute/index.yml) |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure, como Análise de Log ou Insights de Aplicativos| Sim | SecureSphere, a solução SIEM da Imperva, também é suportada através da integração [do Azure Event Hubs](../event-hubs/index.yml) através [da auditoria SQL](sql-database-auditing.md). |
| Registro e auditoria de plano de controle e plano de gerenciamento| Sim | Sim, apenas para alguns eventos. |
| Registro e auditoria de data-plane | Sim | Via [auditoria SQL](sql-database-auditing.md) |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Active Directory do Azure (Azure AD) |
| Autorização| Sim | Nenhum |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Chamado de "criptografia em uso", como descrito no artigo [Always Encrypted](sql-database-always-encrypted.md). A criptografia do lado do servidor usa [criptografia de dados transparente.](transparent-data-encryption-azure-sql.md)|
| Criptografia em trânsito:<ul><li>Criptografia Azure ExpressRoute</li><li>Criptografia em uma rede virtual</li><li>Criptografia entre redes virtuais</ul>| Sim | Usar HTTPS. |
| Manuseio de chaves de criptografia, como CMK ou BYOK| Sim | Tanto o gerenciamento de chaves gerenciadas pelo serviço quanto o gerenciamento de chaves gerenciados pelo cliente são oferecidos. Este último é oferecido através [do Azure Key Vault](../key-vault/index.yml). |
| Criptografia em nível de coluna fornecida pelos serviços de dados do Azure| Sim | Através [de Sempre Criptografado](sql-database-always-encrypted.md). |
| Chamadas de API criptografadas| Sim | Usando HTTPS/TLS. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração, como versão da configuração| Não  | Nenhum |

## <a name="additional-security-controls-for-sql-database"></a>Controles adicionais de segurança para banco de dados SQL

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Prevenção: avaliação da vulnerabilidade | Sim | Consulte [o serviço de avaliação de vulnerabilidades SQL ajuda a identificar vulnerabilidades do banco de dados](sql-vulnerability-assessment.md). |
| Prevenção: descoberta e classificação de dados  | Sim | Consulte [o Banco de Dados SQL do Azure e a classificação de & de & de detecção de dados do SQL Data Warehouse](sql-database-data-discovery-and-classification.md). |
| Detecção: detecção de ameaças | Sim | Consulte [O Banco de Dados Avançados de Ameaças para O Banco de Dados SQL do Azure](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)
