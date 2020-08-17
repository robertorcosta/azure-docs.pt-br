---
title: Visão geral de cofres de Serviços de Recuperação
description: Uma visão geral e a comparação entre os cofres de Serviços de Recuperação e os cofres de Backup do Azure.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2b292a39e38ef5e298f45c2babbee9fbd20c39ea
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261864"
---
# <a name="recovery-services-vaults-overview"></a>Visão geral dos cofres dos Serviços de Recuperação

Este artigo descreve os recursos de um cofre de Serviços de Recuperação. Um cofre de Serviços de Recuperação é uma entidade de armazenamento no Azure que hospeda dados. Os dados normalmente são cópias de dados ou informações de configuração de VMs (máquinas virtuais), cargas de trabalho, servidores ou estações de trabalho. Você pode usar cofres dos Serviços de Recuperação para armazenar dados de backup para vários serviços do Azure, como VMs de IaaS (Windows ou Linux) e bancos de dados SQL do Azure. Os cofres de Serviços de Recuperação dão suporte a System Center DPM, Windows Server, Servidor de Backup do Azure e outros. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento. Os cofres dos serviços de recuperação são baseados no modelo de Azure Resource Manager do Azure, que fornece recursos como:

- **Recursos aprimorados para ajudar a proteger dados de backup**: com os cofres de Serviços de Recuperação, o Backup do Azure fornece recursos de segurança para proteger backups em nuvem. Esses recursos de segurança asseguram que você possa proteger seus backups e recuperar dados com segurança, mesmo que os servidores de produção e de backup estejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitoramento central para seu ambiente de TI híbrida**: com os cofres de Serviços de Recuperação, você pode monitorar não apenas suas [VMs da IaaS do Azure](backup-azure-manage-vms.md), como também seus [ativos locais](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](backup-azure-monitoring-built-in-monitor.md)

- **RBAC (Controle de Acesso Baseado em Função)**: o RBAC oferece controle de gerenciamento de acesso detalhado no Azure. [O Azure fornece várias funções internas](../role-based-access-control/built-in-roles.md), e o Backup do Azure tem três [funções internas para gerenciar pontos de recuperação](backup-rbac-rs-vault.md). Cofres de Serviços de Recuperação são compatíveis com RBAC, que restringe o acesso de backup e restauração ao conjunto definido de funções de usuário. [Saiba mais](backup-rbac-rs-vault.md)

- **Exclusão reversível**: com exclusão reversível, mesmo que um ator mal-intencionado exclua um backup (ou os dados de backup sejam excluídos acidentalmente), os dados de backup serão mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para o cliente. [Saiba mais](backup-azure-security-feature-cloud.md).

- **Restauração entre regiões**: a CRR (restauração entre regiões) permite que você restaure as VMs do Azure em uma região secundária, que é uma região emparelhada do Azure. Se o Azure declarar um desastre na região primária, os dados replicados na região secundária estarão disponíveis para restauração na região secundária para reduzir o desastre real de tempo de inatividade na região primária para seu ambiente. [Saiba mais](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Configurações de armazenamento no cofre dos serviços de recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

- O backup do Azure manipula automaticamente o armazenamento para o cofre. Veja como [as configurações de armazenamento podem ser alteradas](./backup-create-rs-vault.md#set-storage-redundancy).

- Para saber mais sobre a redundância de armazenamento, consulte estes artigos sobre redundância [geográfica](../storage/common/storage-redundancy.md) e [local](../storage/common/storage-redundancy.md) .

### <a name="additional-resources"></a>Recursos adicionais

- [Cenários com suporte e sem suporte do cofre](backup-support-matrix.md#vault-support)
- [Perguntas frequentes sobre o cofre](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Assistente do Azure

O [Azure Advisor](../advisor/index.yml) é um consultor de nuvem personalizado que ajuda a otimizar o uso do Azure. Ele analisa o uso do Azure e fornece recomendações oportunas para ajudar a otimizar e proteger suas implantações. Ele fornece recomendações em quatro categorias: alta disponibilidade, segurança, desempenho e custo.

O assistente do Azure fornece [recomendações](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) por hora para VMs que não são submetidas a backup, portanto, você nunca perde o backup de VMs importantes. Você também pode controlar as recomendações adiando-as.  Você pode selecionar a recomendação e habilitar o backup em VMs em linha especificando o cofre (em que os backups serão armazenados) e a política de backup (agendamento de backups e retenção de cópias de backup).

![Assistente do Azure](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>Próximas etapas

Use os artigos a seguir para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-windows-with-mars-agent.md)
