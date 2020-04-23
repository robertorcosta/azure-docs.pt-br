---
title: Controles de segurança
titleSuffix: Azure Storage
description: Uma lista de verificação de controles de segurança para avaliar o Armazenamento Azure.
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082349"
---
# <a name="security-controls-for-azure-storage"></a>Controles de segurança para armazenamento Azure

Este artigo documenta os controles de segurança incorporados ao Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim |  |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | Consulte [a criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Criptografia de nível de coluna (Azure Data Services)| N/D |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | Suporte aos mecanismos PADRÃO HTTPS/TLS.  Os usuários também podem criptografar dados antes de serem transmitidos para o serviço. |
| Chamadas criptografadas à API| Sim |  |

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| Sim |  |
| Suporte a tags de serviço| Sim | Consulte [a visão geral das tags de serviço do Azure](../../virtual-network/service-tags-overview.md) para obter mais informações sobre tags de serviço suportadas pelo Azure Storage. |
| Suporte à injeção VNet| N/D |  |
| Isolamento de rede e suporte a firewall| Sim | |
| Suporte forçado de tunelamento| N/D |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Métricas do Monitor Azure|
| Registro e auditoria de plano de controle e gerenciamento | Sim | Log de atividades do gerenciador de recursos do Azure |
| Registro e auditoria de data plan| Sim | Registros de diagnóstico de serviço.|

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Diretório ativo do Azure, chave compartilhada, token de acesso compartilhado. |
| Autorização| Sim | Autorização de suporte via RBAC, ACLs POSIX e Tokens SAS |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Versão do provedor de recursos de suporte através de APIs do Azure Resource Manager |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../../security/fundamentals/security-controls.md)