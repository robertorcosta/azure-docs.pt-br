---
title: Controles de segurança para Azure Key Vault
description: Uma lista de verificação de controles de segurança para avaliar Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81429857"
---
# <a name="security-controls-for-azure-key-vault"></a>Controles de segurança para Azure Key Vault

Este artigo documenta os controles de segurança internos do Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Usando pontos de extremidade de serviço de rede virtual (VNet). |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | Usando regras de firewall de VNet. |
| Suporte a túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o Log Analytics. |
| Registro em log e auditoria de plano de controle/gerenciamento| Sim | Usando o Log Analytics. |
| Log e auditoria do plano de dados| Sim | Usando o Log Analytics. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | Usando a política de acesso ao Key Vault. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Todos os objetos são criptografados. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | O cliente controla todas as chaves em seu Key Vault. Quando as chaves com suporte ao módulo de segurança de hardware (HSM) são especificadas, um HSM de nível 2 do FIPS protege a chave, o certificado ou o segredo. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de VNet e criptografia de VNet-VNet)| Sim | Toda a comunicação é por meio de chamadas à API criptografadas |
| Chamadas criptografadas à API| Sim | Usar HTTPS. |

## <a name="access-controls"></a>Controles de acesso

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Controles de acesso do plano de controle/gerenciamento | Sim | RBAC (Controle de Acesso Baseado em Função) do Azure Resource Manager |
| Controles de acesso do plano de dados (em cada nível de serviço) | Sim | Política de acesso ao Key Vault |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../../security/fundamentals/security-controls.md).