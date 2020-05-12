---
title: Matriz de suporte para backup de compartilhamento de arquivos do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de compartilhamentos de arquivos do Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 38e8cdba4913a56b4dc6b4398a99db9e9f93be51
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116723"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de compartilhamento de arquivos do Azure

Você pode usar o [serviço de backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) para fazer backup de compartilhamentos de arquivos do Azure. Este artigo resume as configurações de suporte ao fazer backup de compartilhamentos de arquivos do Azure com o backup do Azure.

## <a name="supported-geos"></a>ÁREAS geográficas com suporte

O backup para compartilhamentos de arquivos do Azure está disponível no seguinte áreas geográficas:

**Regiões de GA**:<br>
Sudeste da Austrália (ASE), centro-Canadá (CNC), Oeste EUA Central (WCUS), oeste dos EUA 2 (WUS 2), sul da Índia (INS), norte EUA Central (NCUS), leste do Japão (JPE), sul do Brasil (BRS), Sul Ásia Oriental (SEA), Oeste da Suíça (SZW), EAU Central (UAC), leste da Noruega (né), Índia ocidental (INW), Austrália Central (ACL), Coreia central (KRC), oeste do Japão (JPW), África do Sul (SAN), Oeste do Reino Unido (UKW) , Sul da Coreia (KRS), Norte da Alemanha (GN), oeste da Noruega (NWW), África do Sul Oeste (visto), Norte da Suíça (SZN), Centro-oeste da Alemanha (GWC), Norte dos EAU (UAN), França central (FRC), Índia central (INC), leste do Canadá (CNE), Ásia Oriental (EA), leste da Austrália (AE), EUA Central (CUS), oeste dos EUA (WUS), US Gov Arizona (UGA), US Gov Texas (UGT), US Gov-Virgínia (UGV), US DoD Central (UDC), US DoD leste (UDE)

**Regiões com suporte (como parte da versão prévia), mas ainda não GA**:<br>
Leste dos EUA (EUS), leste dos EUA 2 (EUS2), Europa Setentrional (NE), Sul EUA Central (SCUS), Sul do Reino Unido (UKS), Europa Ocidental (nós)

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | O backup do Azure dá suporte a compartilhamentos de arquivos do Azure presentes nas contas de armazenamento de tipo v1, com finalidade geral V2 e tipos de armazenamento de arquivos |
| Desempenho              | O backup do Azure dá suporte a compartilhamentos de arquivos nas contas de armazenamento Standard e Premium |
| Replicação              | Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento com qualquer tipo de replicação |

## <a name="supported-file-shares"></a>Compartilhamentos de arquivos com suporte

| Tipo de compartilhamento de arquivos                                   | Suporte   |
| -------------------------------------------------- | --------- |
| Standard                                           | Com suporte |
| grande                                              | Com suporte |
| Premium                                            | Com suporte |
| Compartilhamentos de arquivos conectados com o serviço de sincronização de arquivos do Azure | Com suporte |

## <a name="protection-limits"></a>Limites de proteção

| Configuração                                                      | Limite |
| ------------------------------------------------------------ | ----- |
| Número máximo de compartilhamentos de arquivos que podem ser protegidos por dia por cofre | 200   |
| Número máximo de contas de armazenamento que podem ser registradas por cofre por dia | 50    |

## <a name="backup-limits"></a>Limites do Backup

| Configuração                                      | Limite |
| -------------------------------------------- | ----- |
| Número máximo de backups sob demanda por dia | 4     |
| Número máximo de backups agendados por dia | 1     |

## <a name="restore-limits"></a>Restaurar limites

| Configuração                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Número máximo de restaurações por dia                           | 10      |
| Número máximo de arquivos por restauração                         | 10      |
| Tamanho máximo de restauração recomendado por restauração para compartilhamentos de arquivos grandes | 15 TiB |

## <a name="retention-limits"></a>Limites de retenção

| Configuração                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Total máximo de pontos de recuperação por compartilhamento de arquivos a qualquer momento | 200      |
| Retenção máxima do ponto de recuperação criado pelo backup sob demanda | 10 anos |
| Retenção máxima de pontos de recuperação diários (instantâneos) por compartilhamento de arquivos| 200 dias |
| Retenção máxima de pontos de recuperação semanais (instantâneos) por compartilhamento de arquivos | 200 semanas |
| Retenção máxima de pontos de recuperação mensais (instantâneos) por compartilhamento de arquivos | 120 meses |
| Retenção máxima de pontos de recuperação anuais (instantâneos) por compartilhamento de arquivos | 10 anos |

## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

| Método Restore     | Detalhes                                                      |
| ------------------ | ------------------------------------------------------------ |
| Restauração completa de compartilhamento | Você pode restaurar o compartilhamento de arquivos completo para o local original ou alternativo |
| Restauração no nível do item | Você pode restaurar arquivos e pastas individuais para o local original ou alternativo |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [fazer backup de compartilhamentos de arquivos do Azure](backup-afs.md)
* Saiba como [restaurar compartilhamentos de arquivos do Azure](restore-afs.md)
* Saiba como [gerenciar backups de compartilhamento de arquivos do Azure](manage-afs-backup.md)
