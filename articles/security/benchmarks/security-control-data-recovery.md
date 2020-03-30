---
title: Controle de Segurança do Azure - Recuperação de Dados
description: Recuperação de dados de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934502"
---
# <a name="security-control-data-recovery"></a>Controle de Segurança: Recuperação de dados

Certifique-se de que todos os dados, configurações e segredos do sistema sejam automaticamente copiados regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Habilite o Backup do Azure e configure a fonte de backup (VMs Azure, SQL Server ou Compartilhamentos de Arquivos), bem como o período de freqüência e retenção desejados.

Como ativar o backup do Azure:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.2 | 10.2 | Cliente |

Habilite o Backup do Azure e direcione as VM(s), bem como os períodos de frequência e retenção desejados. Chaves gerenciadas pelo cliente de backup dentro do Azure Key Vault.

Como ativar o backup do Azure:

https://docs.microsoft.com/azure/backup/

Como fazer backup das chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.3 | 10.3 | Cliente |

Garanta a capacidade de executar periodicamente a restauração de dados de conteúdo dentro do Azure Backup. Se necessário, teste a restauração de uma VLAN isolada. Teste a restauração das chaves gerenciadas pelo cliente com backup.

Como recuperar arquivos do backup do Azure Virtual Machine:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Como restaurar as chaves do cofre no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Para backup local, a criptografia em repouso é realizada usando a frase secreta que você fornece ao fazer backup no Azure. Para VMs do Azure, os dados são criptografados em repouso usando SSE (Criptografia do Serviço de Armazenamento). Você pode habilitar o Soft-Delete no Key Vault para proteger as chaves contra exclusão acidental ou maliciosa.

Como ativar o Soft-Delete no Cofre de Chaves:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Próximas etapas

Veja o próximo controle de segurança: [Resposta a incidentes](security-control-incident-response.md)
