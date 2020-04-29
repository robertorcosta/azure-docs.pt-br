---
title: Controles de segurança para retransmissão do barramento de serviço do Azure
description: Este artigo fornece uma lista de verificação de controles de segurança internos para avaliar a retransmissão do barramento de serviço do Azure.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514010"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Controles de segurança para retransmissão do barramento de serviço do Azure

Este artigo documenta os controles de segurança criados na retransmissão do barramento de serviço do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Anotações | Documentação |
|---|---|--|--|
| Suporte ao ponto de extremidade de serviço| Não |  |   |
| Isolamento de rede e suporte de firewall| Não |  |   |
| Suporte a túnel forçado| N/D | Retransmissão é o túnel TLS  |   |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Anotações| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |   |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Log e auditoria do plano de dados| Sim | Êxito na conexão/falha e erros e registrados.  |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Anotações| Documentação |
|---|---|--|--|
| Autenticação| Sim | Via SAS. | [Autenticação e autorização da Retransmissão do Azure](relay-authentication-and-authorization.md) |
| Autorização|  Sim | Via SAS. | [Autenticação e autorização da Retransmissão do Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Anotações | Documentação |
|---|---|--|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/D | A retransmissão é um soquete da Web e não mantém os dados. |   |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | Usa somente certificados TLS da Microsoft.  |   |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |   |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O serviço requer TLS. |   |
| Chamadas criptografadas à API| Sim | HTTPS. |


## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Anotações| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).