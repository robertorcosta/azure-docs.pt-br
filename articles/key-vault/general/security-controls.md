---
title: Controles de segurança para Azure Key Vault
description: Uma lista de verificação de controles de segurança para avaliar o Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429857"
---
# <a name="security-controls-for-azure-key-vault"></a>Controles de segurança para Azure Key Vault

Este artigo documenta os controles de segurança incorporados no Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| Sim | Usando pontos finais de serviço da Virtual Network (VNet). |
| Suporte à injeção VNet| Não |  |
| Suporte de isolamento de rede e firewall| Sim | Usando as regras de firewall do VNet. |
| Suporte forçado de tunelamento| Não |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Usando o Log Analytics. |
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Usando o Log Analytics. |
| Registro e auditoria de data plan| Sim | Usando o Log Analytics. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | Usando a política de acesso ao Key Vault. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Todos os objetos são criptografados. |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | O cliente controla todas as chaves em seu Cofre de Chaves. Quando as teclas de suporte do módulo de segurança de hardware (HSM) são especificadas, um Fips Nível 2 HSM protege a chave, o certificado ou o segredo. |
| Criptografia de nível de coluna (Azure Data Services)| N/D |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | Toda a comunicação é por meio de chamadas à API criptografadas |
| Chamadas criptografadas à API| Sim | Usar HTTPS. |

## <a name="access-controls"></a>Controles de acesso

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Controles de acesso do plano de controle/gerenciamento | Sim | RBAC (Controle de Acesso Baseado em Função) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso ao Key Vault |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../../security/fundamentals/security-controls.md)