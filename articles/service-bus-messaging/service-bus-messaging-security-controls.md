---
title: Controles de segurança para mensagens de ônibus de serviço do Azure
description: Uma lista de verificação de controles de segurança para avaliar mensagens de ônibus do Azure Service
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903259"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controles de segurança para mensagens de ônibus de serviço do Azure

Este artigo documenta os controles de segurança incorporados no Azure Service Bus Messaging.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Suporte a ponto final de serviço| Sim (somente nível Premium) | Os pontos finais de serviço da VNet são suportados apenas para [o nível Service Bus Premium.](service-bus-premium-messaging.md) |  |
| Suporte à injeção VNet| Não | |  |
| Suporte de isolamento de rede e firewall| Sim (somente nível Premium) |  |  |
| Suporte forçado de tunelamento| Não |  |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Suportado via [Monitor e Alertas Do Azure](service-bus-metrics-azure-monitor.md). |  |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Os registros de operações estão disponíveis.  | [Logs de diagnóstico do Barramento de Serviço](service-bus-diagnostic-logs.md) |
| Registro e auditoria de data plan| Não |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Autenticação| Sim | Gerenciado através [do Azure Active Directory Managed Service Identity](service-bus-managed-service-identity.md).| [Autenticação e autorização do Ônibus de Serviço](service-bus-authentication-and-authorization.md). |
| Autorização| Sim | Suporta autorização via [token RBAC](authenticate-application.md) e SAS. | [Autenticação e autorização do Ônibus de Serviço](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft |  Sim para criptografia do lado do servidor em repouso por padrão. |  |  |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim. | Uma chave gerenciada pelo cliente no Azure KeyVault pode ser usada para criptografar os dados no Service Bus Namespace em repouso. | [Configure chaves gerenciadas pelo cliente para criptografar os dados do Ônibus de Serviço do Azure em repouso usando o portal Azure](configure-customer-managed-key.md)  |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |   |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | Suporta mecanismo PADRÃO HTTPS/TLS. |   |
| Chamadas criptografadas à API| Sim | As chamadas de API são feitas através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |   |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Suporta a versão do provedor de recursos através da [API do Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)
