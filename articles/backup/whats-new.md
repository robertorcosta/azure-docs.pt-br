---
title: Novidades no Backup do Azure
description: Saiba mais sobre os novos recursos no backup do Azure.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ba29ddea5d5f096640f2bfc012c44ab06bb3e131
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309657"
---
# <a name="whats-new-in-azure-backup"></a>Novidades no Backup do Azure

O backup do Azure está constantemente aprimorando e liberando novos recursos que melhoram a proteção de seus dados no Azure. Esses novos recursos expandem sua proteção de dados para novos tipos de carga de trabalho, aumentam a segurança e melhoram a disponibilidade dos seus dados de backup. Eles também adicionam novos recursos de gerenciamento, monitoramento e automação.

Você pode saber mais sobre as novas versões ao marcar esta página ou [inscrevendo-se nas atualizações aqui](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Resumo de atualizações

- Novembro de 2020
  - [Modelo de Azure Resource Manager para backup de compartilhamento de arquivos do Azure (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Backups incrementais para bancos de dados SAP HANA em VMs do Azure](#incremental-backups-for-sap-hana-databases)
- Setembro de 2020
  - [Centro de Backup](#backup-center)
  - [Backup do Banco de Dados do Azure para PostgreSQL](#backup-azure-database-for-postgresql)
  - [Backup e restauração de disco seletivo](#selective-disk-backup-and-restore)
  - [Restauração entre regiões para bancos de dados SQL Server e SAP HANA em VMs do Azure](#cross-region-restore-for-sql-server-and-sap-hana)
  - [Suporte para backup de VMs com até 32 discos](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Experiência de configuração de backup simplificada para SQL em VMs do Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [SAP HANA de backup em máquinas virtuais RHEL do Azure](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [ZRS (armazenamento com redundância de zona) para dados de backup](#zone-redundant-storage-zrs-for-backup-data)
  - [Exclusão reversível para cargas de trabalho de SQL Server e SAP HANA em VMs do Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Modelo de Azure Resource Manager para o backup AFS

O backup do Azure agora dá suporte à configuração de backup para compartilhamentos de arquivos do Azure existentes usando um modelo de Azure Resource Manager (ARM). O modelo configura a proteção para um compartilhamento de arquivos existente do Azure especificando os detalhes apropriados para o cofre dos serviços de recuperação e a política de backup. Opcionalmente, ele cria um novo cofre de serviços de recuperação e uma política de backup e registra a conta de armazenamento que contém o compartilhamento de arquivos para o cofre dos serviços de recuperação.

Para obter mais informações, consulte [modelos de Azure Resource Manager para o backup do Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases"></a>Backups incrementais para bancos de dados SAP HANA

O backup do Azure agora dá suporte a backups incrementais para SAP HANA bancos de dados hospedados em VMs do Azure. Isso permite backups mais rápidos e econômicos de seus dados de SAP HANA.

Para obter mais informações, consulte [várias opções disponíveis durante a criação de uma política de backup](sap-hana-faq-backup-azure-vm.md#policy) e [como criar uma política de backup para bancos de dados do SAP Hana](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center"></a>Centro de Backup

O backup do Azure habilitou uma nova funcionalidade de gerenciamento nativo para gerenciar todo o seu espaço de backup a partir de um console central. O centro de backup fornece a capacidade de monitorar, operar, controlar e otimizar a proteção de dados em escala de maneira unificada consistente com as experiências de gerenciamento nativo do Azure.

Com o centro de backup, você obtém uma exibição agregada do seu inventário entre assinaturas, locais, grupos de recursos, cofres e até mesmo locatários usando o Azure Lighthouse. O centro de backup também é uma central de ações da qual você pode disparar suas atividades relacionadas a backup, como configurar backup, restauração, criação de políticas ou cofres, tudo a partir de um único lugar. Além disso, com a integração direta com o Azure Policy, agora você pode controlar seu ambiente e acompanhar a conformidade de uma perspectiva de backup. Políticas internas do Azure específicas ao backup do Azure também permitem que você configure backups em escala.

Para obter mais informações, consulte [visão geral do centro de backup](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql"></a>Backup do Banco de Dados do Azure para PostgreSQL

O backup do Azure e os serviços de banco de dados do Azure vêm juntos para criar uma solução de backup de classe empresarial para o Azure PostgreSQL (agora em versão prévia). Agora você pode atender às suas necessidades de proteção e conformidade de dados com uma política de backup controlada pelo cliente que permite a retenção de backups por até 10 anos. Com isso, você tem controle granular para gerenciar as operações de backup e restauração no nível de banco de dados individual. Da mesma forma, você pode restaurar em versões do PostgreSQL ou no armazenamento de BLOBs com facilidade.

Para obter mais informações, consulte [banco de dados do Azure para o backup do PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Backup e restauração de disco seletivo

O backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM juntas usando a solução de backup de máquina virtual. Agora, usando a funcionalidade de backup e restauração dos discos seletivos, você pode fazer backup de um subconjunto dos discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém apenas os discos que são incluídos na operação de backup.

Para obter mais informações, consulte [backup e restauração de disco seletivo para máquinas virtuais do Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>Restauração entre regiões para SQL Server e SAP HANA

Com a introdução da restauração entre regiões, agora você pode iniciar restaurações em uma região secundária para reduzir problemas reais de tempo de inatividade em uma região primária para seu ambiente. Isso faz com que a região secundária seja restaurada completamente controlada pelo cliente. O backup do Azure usa os dados de backup replicados para a região secundária para essas restaurações.

Agora, além do suporte para a restauração entre regiões para máquinas virtuais do Azure, o recurso foi estendido para restaurar bancos de dados SQL e SAP HANA em máquinas virtuais do Azure também.

Para obter mais informações, consulte [restauração entre regiões para bancos de dados SQL](restore-sql-database-azure-vm.md#cross-region-restore) e [restauração entre regiões para bancos de dados SAP Hana](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Suporte para backup de VMs com até 32 discos

Até agora, o backup do Azure tem suporte de 16 discos gerenciados por VM. Agora, o backup do Azure dá suporte ao backup de até 32 discos gerenciados por VM.

Para obter mais informações, consulte a [matriz de suporte de armazenamento de VM](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configuração de backup mais simples para SQL em VMs do Azure

A configuração de backups para seu SQL Server em VMs do Azure agora é ainda mais fácil com a configuração de backup embutida integrada no painel de VM do portal do Azure. Em apenas algumas etapas, você pode habilitar o backup de seus SQL Server para proteger todos os bancos de dados existentes, bem como aqueles que são adicionados no futuro.

Para obter mais informações, consulte [fazer backup de um SQL Server no painel VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>SAP HANA de backup em máquinas virtuais RHEL do Azure

O backup do Azure é a solução de backup nativo para o Azure e é BackInt certificado pelo SAP. O backup do Azure agora adicionou suporte para o Red Hat Enterprise Linux (RHEL), um dos sistemas operacionais Linux mais amplamente usados em execução SAP HANA.

Para obter mais informações, consulte a [matriz de suporte do cenário de backup do banco de dados SAP Hana](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>ZRS (armazenamento com redundância de zona) para dados de backup

O armazenamento do Azure fornece um excelente equilíbrio entre alto desempenho, alta disponibilidade e alta resiliência de dados com suas variadas opções de redundância. O backup do Azure permite que você estenda esses benefícios para os dados de backup também, com opções para armazenar seus backups em LRS (armazenamento com redundância local) e GRS (armazenamento com redundância geográfica). Agora, há opções de durabilidade adicionais com suporte adicionado para ZRS (armazenamento com redundância de zona).

Para obter mais informações, consulte [definir a redundância de armazenamento para o cofre dos serviços de recuperação](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Exclusão reversível para cargas de trabalho de SQL Server e SAP HANA

Preocupações sobre problemas de segurança, como malware, ransomware e invasão, estão aumentando. Esses problemas de segurança podem ser dispendiosos em termos de dinheiro e dados. Para se proteger contra tais ataques, o backup do Azure fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão.

Um desses recursos é exclusão reversível. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua um backup (ou os dados de backup sejam excluídos acidentalmente), os dados de backup são mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para você.

Agora, além do suporte de exclusão reversível para VMs do Azure, as cargas de trabalho SQL Server e SAP HANA em VMs do Azure também são protegidas com exclusão reversível.

Para obter mais informações, consulte [exclusão reversível para SQL Server na VM do Azure e SAP Hana em cargas de trabalho de VM do Azure](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Próximas etapas

- [Diretrizes e práticas recomendadas do backup do Azure](guidance-best-practices.md)
