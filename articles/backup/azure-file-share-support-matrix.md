---
title: Matriz de suporte para backup de compartilhamento de arquivos do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de compartilhamentos de arquivos do Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 42578cc83ef193801fa700ec7d136385411e5f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684631"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de compartilhamento de arquivos do Azure

Você pode usar o [serviço de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) para fazer backup de compartilhamentos de arquivos do Azure. Este artigo resume as configurações de suporte ao fazer backup de compartilhamentos de arquivos do Azure com o Backup do Azure.

## <a name="supported-geos"></a>Áreas geográficas com suporte

O backup para compartilhamentos de arquivos do Azure está disponível nas seguintes áreas geográficas:

**Regiões de GA**:<br>
Sudeste da Austrália (ASE), Canadá Central (CNC), Centro-Oeste dos EUA (WCUS), Centro-Sul dos EUA (SCUS), Oeste dos EUA 2 (WUS 2), Sul da Índia (INS), Centro-Norte dos EUA (NCUS), Leste do Japão (JPE), Sul do Brasil (BRS), Sudeste da Ásia (SEA), Oeste da Suíça (SZW), EAU Central (UAC), Leste da Noruega (NWE), Oeste da Índia (INW), Austrália Central (ACL), Coreia Central (KRC), Oeste do Japão (JPW), Norte da África do Sul (SAN), Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW), Sul da Coreia (KRS), Norte da Europa (NE), Norte da Alemanha (GN), Oeste da Noruega (NWW), Oeste da África do Sul (SAW), Norte da Suíça (SZN), Centro-Oeste da Alemanha (GWC), Norte dos EAU (UAN), França Central (FRC), Índia Central (INC), Leste do Canadá (CNE), Leste da Ásia (EA), Leste da Austrália (AE),  EUA Central (CUS), Oeste dos EUA (WUS), US Gov - Arizona (UGA), US Gov - Texas (UGT), US Gov - Virgínia (UGV), US DoD Central (UDC), Leste do US DoD (UDE)

**Regiões com suporte (como parte da versão prévia), mas ainda não GA**:<br>
Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Oeste da Europa (WE)

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | O Backup do Azure oferece suporte a compartilhamentos de arquivos do Azure presentes nas contas de armazenamento de uso geral v1, de uso geral v2 e tipo de armazenamento de arquivos |
| Desempenho              | O Backup do Azure oferece suporte a compartilhamentos de arquivos nas contas de Armazenamento Premium e padrão |
| Replicação              | Há suporte para compartilhamentos de arquivos do Azure em Contas de Armazenamento com qualquer tipo de replicação |

## <a name="supported-file-shares"></a>Compartilhamentos de arquivos com suporte

| Tipo de compartilhamento de arquivos                                   | Suporte   |
| -------------------------------------------------- | --------- |
| Standard                                           | Com suporte |
| grande                                              | Com suporte |
| Premium                                            | Com suporte |
| Compartilhamentos de arquivos conectados ao serviço de sincronização de Arquivos do Azure | Com suporte |

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

## <a name="restore-limits"></a>Limites de restauração

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

| Método de restauração     | Detalhes                                                      |
| ------------------ | ------------------------------------------------------------ |
| Restauração completa de compartilhamento | Você pode restaurar um compartilhamento completo de arquivos para a localização original ou uma localização alternativa |
| Restauração no nível do item | Você pode restaurar arquivos e pastas individuais para a localização original ou alternativa |

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Fazer backup de compartilhamentos de arquivos do Azure](backup-afs.md)
* Saiba como [Restaurar compartilhamentos de arquivos do Azure](restore-afs.md)
* Saiba como [Gerenciar backups de compartilhamentos de arquivos do Azure](manage-afs-backup.md)
