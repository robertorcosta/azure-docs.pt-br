---
title: Controles de segurança para gerenciamento de API do Azure
description: Uma lista de verificação de controles de segurança para avaliação do gerenciamento de API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751129"
---
# <a name="security-controls-for-api-management"></a>Controles de segurança para gerenciamento de API

Este artigo documenta os controles de segurança incorporados ao Gerenciamento de API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Suporte a ponto final de serviço| Não | |  |
| Suporte à injeção VNet| Sim | |  |
| Suporte de isolamento de rede e firewall| Sim | Usando grupos de segurança de rede (NSG) e Azure Application Gateway (ou outro aparelho de software) respectivamente. |  |
| Suporte forçado de tunelamento| Sim | A rede do Azure fornece túnel forçado. |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | | |
| Registro e auditoria de plano de controle e gerenciamento| Sim | [Registros de atividades do Monitor do Azure](../azure-monitor/platform/platform-logs-overview.md) | |
| Registro e auditoria de data plan| Sim | [Registros de diagnóstico do Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) e (opcionalmente) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Autenticação| Sim | |  |
| Autorização| Sim | |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Dados confidenciais, como certificados, chaves e valores com nomes secretos, são criptografados com chaves de instância gerenciada por serviço. |  |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | Todas as chaves de criptografia são por instância de serviço e são gerenciadas pelo serviço. |  |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | [A](../expressroute/index.yml) criptografia Express Route e VNet é fornecida pela [rede Azure.](../virtual-network/index.yml) |  |
| Chamadas criptografadas à API| Sim | As chamadas de plano de gerenciamento são feitas através [do Azure Resource Manager](../azure-resource-manager/index.yml) via TLS. Um JWT (token Web JSON) válido é necessário.  As chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação suportados (por exemplo, certificado de cliente ou JWT). |   |
 |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Usando o [kit de recursos de devOps de gerenciamento de gerenciamento de API do Azure](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>A vulnerabilidade verifica falsos positivos

Esta seção documenta vulnerabilidades comuns que não afetam o gerenciamento de API do Azure.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sangramento de ingressos (CVE-2016-9244) | O ticketbleed é uma vulnerabilidade na implementação da extensão TLS SessionTicket encontrada em alguns produtos F5. Permite o vazamento ("sangramento") de até 31 bytes de dados de memória não inicializada. Isso é causado pela pilha TLS enchimento de um ID de sessão, passado do cliente, com dados para torná-lo 32 bits de comprimento. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)