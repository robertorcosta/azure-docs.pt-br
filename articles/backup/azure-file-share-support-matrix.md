---
title: Matriz de suporte para backup de compartilhamento de arquivos do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de compartilhamentos de arquivos do Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 03190c6b83fceec88442583a57ff46756cfe6221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195682"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de compartilhamento de arquivos do Azure

Você pode usar o [serviço de backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) para fazer backup de compartilhamentos de arquivos do Azure. Este artigo resume as configurações de suporte ao fazer backup de compartilhamentos de arquivos do Azure com o backup do Azure.

## <a name="supported-geos"></a>ÁREAS geográficas com suporte

O backup para compartilhamentos de arquivos do Azure está disponível no seguinte áreas geográficas:

| Regiões de GA | Regiões com suporte, mas não GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Sudeste da Austrália (ASE), centro-Canadá (CNC), Oeste EUA Central (WCUS), oeste dos EUA 2 (WUS 2), sul da Índia (INS), norte EUA Central (NCUS), leste do Japão (JPE), sul do Brasil (BRS), Sul Ásia Oriental (SEA)                                                     |Leste da Austrália (AE), leste do Canadá (CE), Ásia Oriental (EA), leste dos EUA (EUS), leste dos EUA 2 (EUS2), oeste do Japão (JPW), Índia central (INC), Coreia central (KRC), sul da Coreia (KRS), Europa Setentrional (NE), Sul EUA Central (SCUS), Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW), Europa Ocidental (nós), oeste dos EUA (WUS), US Gov Arizona (UGA), US Gov Texas (UGT), US Gov-Virgínia (UGV), central da Austrália (ACL) , Índia ocidental (INW), norte da África do Sul (SAN), Norte dos EAU (UAN), França central (FRC), Norte da Alemanha (GN), Centro-oeste da Alemanha (GWC), África do Sul Oeste (visto), EAU Central (UAC), leste da Noruega (né), Noruega ocidental (NWW), Norte da Suíça (SZN), EUA Central (CUS)           |

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | O backup do Azure dá suporte a compartilhamentos de arquivos do Azure presentes no uso geral v1, de uso geral V2 e do tipo de compartilhamento de arquivo contas de armazenamento |
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
