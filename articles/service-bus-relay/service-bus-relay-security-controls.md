---
title: Controles de segurança para relé de ônibus de serviço azure
description: Esteartigo fornece uma lista de verificação de controles de segurança incorporados para avaliar o Azure Service Bus Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514010"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Controles de segurança para relé de ônibus de serviço azure

Este artigo documenta os controles de segurança incorporados no Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Suporte a ponto final de serviço| Não |  |   |
| Suporte de isolamento de rede e firewall| Não |  |   |
| Suporte forçado de tunelamento| N/D | Relé é o túnel TLS  |   |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | |   |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Através [do Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Registro e auditoria de data plan| Sim | Sucesso de conexão / falha e erros e logado.  |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Autenticação| Sim | Via SAS. | [Autenticação e autorização da Retransmissão do Azure](relay-authentication-and-authorization.md) |
| Autorização|  Sim | Via SAS. | [Autenticação e autorização da Retransmissão do Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/D | O relé é um soquete web e não persiste dados. |   |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | Usa apenas certs Microsoft TLS.  |   |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |   |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | O serviço requer TLS. |   |
| Chamadas criptografadas à API| Sim | HTTPS. |


## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Através [do Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)