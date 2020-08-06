---
title: Controles de segurança
titleSuffix: Azure Storage
description: Exibir listas de verificação de controle de segurança para avaliar o armazenamento do Azure. As áreas cobertas são proteção de dados, rede, monitoramento e registro, identidade e configuração.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: b816e7bd5e00b21700bc994fc0860195d39f2915
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826510"
---
# <a name="security-controls-for-azure-storage"></a>Controles de segurança para o armazenamento do Azure

Este artigo documenta os controles de segurança incorporados ao armazenamento do Azure.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim |  |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | Consulte [criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | Suporte a mecanismos HTTPS/TLS padrão.  Os usuários também podem criptografar dados antes de serem transmitidos para o serviço. |
| Chamadas criptografadas à API| Sim |  |

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte a marcas de serviço| Sim | Consulte [visão geral das marcas de serviço do Azure](../../virtual-network/service-tags-overview.md) para obter mais informações sobre as marcas de serviço com suporte no armazenamento do Azure. |
| Suporte à injeção de VNet| N/D |  |
| Isolamento de rede e suporte a firewall| Sim | |
| Suporte a túnel forçado| N/D |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Métricas do Azure Monitor|
| Registro e auditoria do plano de gerenciamento e controle | Sim | Log de Atividades do Azure |
| Log e auditoria do plano de dados| Sim | Azure Monitor logs de recursos |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Azure Active Directory, chave compartilhada, token de acesso compartilhado. |
| Autorização| Sim | Suporte à autorização via RBAC, ACLs POSIX e tokens SAS |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Suporte ao controle de versão do provedor de recursos por meio de APIs de Azure Resource Manager |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../../security/fundamentals/security-controls.md).