---
title: Controles de segurança para Hubs de Eventos Azure
description: Este artigo fornece uma lista de verificação de controles de segurança para avaliação de Hubs de Eventos Azure (rede, identidade, proteção de dados, etc.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309499"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controles de segurança para Hubs de Eventos Azure

Este artigo documenta os controles de segurança incorporados no Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Suporte a ponto final de serviço| Sim |  |  |
| Suporte à injeção VNet| Não | |  |
| Suporte de isolamento de rede e firewall| Sim |  |  |
| Suporte forçado de tunelamento| Não |  |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | |  |
| Registro e auditoria de plano de controle e gerenciamento| Sim |  |  |
| Registro e auditoria de data plan| Sim |   |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Autenticação| Sim | | [Autorize o acesso aos Hubs de Eventos do Azure,](authorize-access-event-hubs.md) [autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory,](authorize-access-azure-active-directory.md) [autorizando o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md) |
| Autorização|  Sim | | [Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs,](authenticate-managed-identity.md) [autenticar um aplicativo com o Azure Active Directory para acessar os recursos do Event Hubs,](authenticate-application.md) [autenticar o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft |  Sim | |  |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim. Disponível para clusters dedicados. | Uma chave gerenciada pelo cliente no Azure KeyVault pode ser usada para criptografar os dados em um Event Hub em repouso. | [Configure chaves gerenciadas pelo cliente para criptografar dados do Azure Event Hubs em repouso usando o portal Azure](configure-customer-managed-key.md) |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | |  |
| Chamadas criptografadas à API| Sim |  |  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | |  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)
