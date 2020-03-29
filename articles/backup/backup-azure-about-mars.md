---
title: Sobre o Agente MARS
description: Saiba como o Agente MARS suporta os cenários de backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673289"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Sobre o agente Microsoft Azure Recovery Services (MARS)

Este artigo descreve como o serviço de backup do Azure usa o agente Microsoft Azure Recovery Services (MARS) para fazer backup e restaurar arquivos, pastas e o estado de volume ou sistema de um computador local para o Azure.

O agente MARS suporta os seguintes cenários de backup:

![Cenários de backup da MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Arquivos e pastas**: Proteja seletivamente arquivos e pastas do Windows.
- **Nível de volume**: Proteja um volume inteiro do Windows da sua máquina.
- **Nível do sistema**: Proteja um estado inteiro do sistema Windows.

O agente MARS suporta os seguintes cenários de restauração:

![Cenários de recuperação de MARTE](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Mesmo servidor**: O servidor no qual o backup foi originalmente criado.
  - **Arquivos e pastas**: Escolha os arquivos e pastas individuais que deseja restaurar.
  - **Nível de volume**: Escolha o ponto de volume e recuperação que deseja restaurar e restaure-o no mesmo local ou em um local alternativo na mesma máquina.  Crie uma cópia dos arquivos existentes, sobreescreva arquivos existentes ou pule a recuperação de arquivos existentes.
  - **Nível do sistema**: Escolha o estado do sistema e o ponto de recuperação para restaurar a mesma máquina em um local especificado.

- **Servidor alternativo**: Um servidor diferente do servidor onde o backup foi feito.
  - **Arquivos e pastas**: Escolha os arquivos e pastas individuais cujo ponto de recuperação você deseja restaurar para uma máquina de destino.
  - **Nível de volume**: Escolha o ponto de volume e recuperação que deseja restaurar para outro local. Crie uma cópia dos arquivos existentes, sobreescreva arquivos existentes ou pule a recuperação de arquivos existentes.
  - **Nível do sistema**: Escolha o estado do sistema e o ponto de recuperação para restaurar como um arquivo Estado do Sistema para uma máquina alternativa.

## <a name="backup-process"></a>Processo de backup

1. A partir do portal Azure, crie um [cofre de Serviços de Recuperação](install-mars-agent.md#create-a-recovery-services-vault)e escolha arquivos, pastas e o estado do sistema a partir das metas de backup.
2. [Baixe as credenciais do cofre do Recovery Services e do instalador](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) de agentes em uma máquina no local.

    Para proteger a máquina no local, selecionando a opção Backup, escolha arquivos, pastas e o estado do sistema e, em seguida, baixe o agente MARS.

3. Prepare a infra-estrutura:

    a. Execute o instalador para [instalar o agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Use as credenciais de cofre baixadas para registrar a máquina no cofre dos Serviços de Recuperação.
4. A partir do console do agente no cliente, [configure o backup](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy). Especifique a política de retenção de seus dados de backup para começar a protegê-los.

![Diagrama do agente de backup do Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Cenários adicionais

- **Faça backup de arquivos e pastas específicos dentro de máquinas virtuais do Azure**: O principal método para fazer backup das máquinas virtuais (VMs) do Azure é usar uma extensão de backup do Azure na VM. A extensão apoia toda a VM. Se você quiser fazer backup de arquivos e pastas específicos dentro de uma VM, você pode instalar o agente MARS nas VMs do Azure. Para obter mais informações, consulte [Arquitetura: Backup do Azure VM incorporado](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Semeamento offline**: Backups completos iniciais de dados para o Azure normalmente transferem grandes quantidades de dados e requerem mais largura de banda da rede. Os backups subseqüentes transferem apenas a quantidade de dados delta ou incremental. O Backup do Azure compacta os backups inicias. Através do processo de *semeamento off-line,* o Azure Backup pode usar discos para carregar os dados iniciais de backup compactados off-line para o Azure. Para obter mais informações, consulte [o backup off-line do Azure usando a Caixa de Dados Do Azure](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Próximas etapas

[Matriz de suporte do agente do MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Perguntas frequentes sobre o agente MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
