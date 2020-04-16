---
title: Controle de Segurança do Azure - Recuperação de Dados
description: Recuperação de dados do Controle de Segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408585"
---
# <a name="security-control-data-recovery"></a>Controle de Segurança: Recuperação de dados

Certifique-se de que todos os dados, configurações e segredos do sistema sejam automaticamente copiados regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Certifique-se de backups automatizados regulares

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Habilite o Backup do Azure e configure a fonte de backup (VMs Azure, SQL Server ou Compartilhamentos de Arquivos), bem como o período de freqüência e retenção desejados.

- [Como ativar o backup do Azure](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute backups completos do sistema e faça backup de chaves gerenciadas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.2 | 10.2 | Cliente |

Habilite o Backup do Azure e direcione as VM(s), bem como os períodos de frequência e retenção desejados. Chaves gerenciadas pelo cliente de backup dentro do Azure Key Vault.

- [Como ativar o backup do Azure](https://docs.microsoft.com/azure/backup/)

- [Como fazer backup das chaves do cofre no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.3 | 10.3 | Cliente |

Garanta a capacidade de executar periodicamente a restauração de dados de conteúdo dentro do Azure Backup. Teste a restauração das chaves gerenciadas pelo cliente com backup.

- [Como recuperar arquivos do backup do Azure Virtual Machine](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Como restaurar as chaves do cofre no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves gerenciadas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Para backup local, a criptografia em repouso é realizada usando a frase secreta que você fornece ao fazer backup no Azure. Para VMs do Azure, os dados são criptografados em repouso usando SSE (Criptografia do Serviço de Armazenamento). Use o controle de acesso baseado em função para proteger backups e chaves gerenciadas pelo cliente.  

Habilite a proteção soft-delete e purga no Key Vault para proteger as chaves contra exclusão acidental ou maliciosa.  Se o Azure Storage for usado para armazenar backups, habilite a exclusão suave para salvar e recuperar seus dados quando blobs ou blobsnapshots forem excluídos. 

- [Entenda o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Como ativar a proteção soft-delete e purga no Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Exclusão reversível para blobs do Armazenamento do Azure ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Próximas etapas

- Veja o próximo Controle de Segurança: [Resposta a Incidentes](security-control-incident-response.md)