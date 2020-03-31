---
title: Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação
description: Descreve como fazer backup de VMs do Azure em um cofre de Serviços de Recuperação usando o Backup do Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273508"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação

Este artigo descreve como fazer backup de VMs do Azure em um cofre de Serviços de Recuperação, usando o serviço de backup do [Azure.](backup-overview.md)

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> * Prepare VMs do Azure.
> * Crie um cofre.
> * Descubra As VMs e configure uma política de backup.
> * Habilite o backup para VMs Azure.
> * Executar o backup inicial.

> [!NOTE]
> Este artigo descreve como configurar um cofre e selecionar VMs para fazer backup. Isso é útil se você quiser fazer backup de várias VMs. Alternativamente, você pode [fazer backup de uma única VM Azure](backup-azure-vms-first-look-arm.md) diretamente das configurações da VM.

## <a name="before-you-start"></a>Antes de começar

* [Revise](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup do Azure VM.
* [Saiba mais sobre](backup-azure-vms-introduction.md) backup de VM do Azure e a extensão de backup.
* [Revise a matriz de suporte](backup-support-matrix-iaas.md) antes de configurar o backup.

Além disso, há algumas coisas que você pode precisar fazer em algumas circunstâncias:

* **Instale o agente VM no VM**: O Backup Do Azure faz backup das VMs do Azure instalando uma extensão para o agente Azure VM em execução na máquina. Se a VM foi criada a partir de uma imagem de mercado do Azure, o agente será instalado e executado. Se você criar uma VM personalizada ou migrar uma máquina no local, talvez seja necessário [instalar o agente manualmente](#install-the-vm-agent).

## <a name="create-a-vault"></a>Criar um cofre

 Um cofre armazena backups e pontos de recuperação criados ao longo do tempo e armazena as políticas de backup associadas às máquinas submetidas a backup. Crie um cofre da seguinte maneira:

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Na pesquisa, digite **Serviços de Recuperação**. Em **Serviços,** clique **em cofres de serviços de recuperação**.

     ![Busca por cofres dos Serviços de Recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. No menu **de cofres dos Serviços de Recuperação,** clique **em +Adicionar**.

     ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. No **cofre dos Serviços de Recuperação,** digite um nome amigável para identificar o cofre.
    * O nome deve ser exclusivo para a assinatura do Azure.
    * Ele pode conter caracteres de 2 a 50.
    * Ele deve começar com uma letra e pode conter apenas letras, números e hifens.
5. Selecione a assinatura do Azure, o grupo de recursos e a região geográfica em que o cofre deve ser criado. Em seguida, clique em **Criar**.
    * Pode levar algum tempo para que o cofre seja criado.
    * Monitore as notificações de status na área superior direita do portal.

Depois que o cofre é criado, ele aparece na lista de cofres dos Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

![Lista de cofres de backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> O Azure Backup agora permite a personalização do nome do grupo de recursos criado pelo serviço de backup do Azure. Para obter mais informações, consulte [o grupo de recursos Azure Backup para Máquinas Virtuais](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu principal mecanismo de backup, recomendamos que você use GRS.
* Você pode usar [o armazenamento localmente redundante (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modificar o tipo de replicação de armazenamento da seguinte forma:

1. No novo cofre, clique em **Propriedades** na seção **Configurações.**
2. Em **Propriedades,** em **Configuração de backup,** clique em **Atualizar**.
3. Selecione o tipo de replicação de armazenamento e clique **em Salvar**.

      ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Você não pode modificar o tipo de replicação de armazenamento depois que o cofre estiver configurado e contiver itens de backup. Se você quiser fazer isso, você precisa recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de backup

Configure uma política de backup para o cofre.

1. No cofre, clique em **+Backup** na seção **Visão geral.**

   ![Botão Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. Em **Backup Goal** > **Onde sua carga de trabalho está sendo executado?** selecione **Azure**. Em **Que você deseja fazer backup?** selecione Máquina >   **Virtual****OK**. Isso registra a extensão da VM no cofre.

   ![Painéis Backup e Meta de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Na **Política de Backup**, escolha a política que você deseja associar ao cofre.
    * A política padrão apoia a VM uma vez por dia. Os backups diários são mantidos por 30 dias. Os instantâneos de recuperação instantânea são retidos por dois dias.
    * Se você não quiser usar a diretiva padrão, selecione **Criar novo**e crie uma diretiva personalizada conforme descrito no procedimento seguinte.

      ![Política de backup padrão](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Em **Selecionar máquinas virtuais,** selecione as VMs que deseja fazer backup usando a diretiva. Em seguida, clique em **OK**.

   * As VMs selecionadas são validadas.
   * Você só pode escolher máquinas virtuais na mesma região que o cofre.
   * O backup das VMs só pode ser feito em um único cofre.

     ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Em **Backup,** clique **em Ativar backup**. Isso implantará a política no cofre e nas VMs e instalará a extensão de backup no agente da VM em execução na VM do Azure.

     ![Botão "Habilitar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de habilitar o backup:

* O serviço de Backup instala a extensão de backup independentemente de a VM estar em execução.
* Um backup inicial será executado de acordo com seu cronograma de backup.
* Quando os backups forem executados, observe que:
  * Uma VM que está sendo em execução tem a maior chance de capturar um ponto de recuperação consistente com o aplicativo.
  * No entanto, mesmo que a VM esteja desligada, ela é backup. Tal VM é conhecida como uma VM offline. Neste caso, o ponto de recuperação será consistente com a queda.
* A conectividade de saída explícita não é necessária para permitir o backup de VMs do Azure.

### <a name="create-a-custom-policy"></a>Criar uma política personalizada

Se você for selecionado para criar uma nova diretiva de backup, preencha as configurações de diretiva.

1. Em **Nome da política,** especifique um nome significativo.
2. No **cronograma de backup,** especifique quando os backups devem ser feitos. Você pode fazer backups diários ou semanais para As VMs do Azure.
3. Em **Instant Restore,** especifique quanto tempo deseja reter instantâneos localmente para restauração instantânea.
    * Quando você restaura, os discos VM de backup são copiados do armazenamento, em toda a rede, para o local de armazenamento de recuperação. Com a restauração instantânea, você pode aproveitar instantâneos armazenados localmente durante um trabalho de backup, sem esperar que os dados de backup sejam transferidos para o cofre.
    * Você pode reter instantâneos para restauração instantânea por entre um a cinco dias. Dois dias é a configuração padrão.
4. Na **faixa de retenção,** especifique quanto tempo deseja manter seus pontos de backup diários ou semanais.
5. Em **Retenção de ponto de backup mensal,** especifique se deseja manter um backup mensal de seus backups diários ou semanais.
6. Clique em **OK** para salvar a política.

    ![Nova política de backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > O Backup do Azure não oferece suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. À medida que as alterações de tempo ocorrem, modifique as políticas de backup manualmente conforme necessário.

## <a name="trigger-the-initial-backup"></a>Acione o backup inicial

O backup inicial será executado de acordo com o cronograma, mas você pode executá-lo imediatamente da seguinte forma:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de backup,** clique **em Azure Virtual Machine**.
3. Na lista **Itens de backup,** clique nas elipses (...).
4. Clique em **Fazer backup agora**.
5. Em **Backup Now,** use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o progresso do trabalho no painel do cofre > **backup jobs** > **em andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.

## <a name="verify-backup-job-status"></a>Verifique o status do trabalho de backup

Os detalhes do trabalho de backup para cada backup de VM consistem em duas fases, a fase **Snapshot** seguida pela fase **transferir dados para o cofre.**<br/>
A fase de instantâneo garante a disponibilidade de um ponto de recuperação armazenado junto com os discos para **Restaurações Instantâneas** e está disponível por um máximo de cinco dias, dependendo da retenção de instantâneo configurada pelo usuário. Transferir dados para o cofre cria um ponto de recuperação no cofre para retenção a longo prazo. A transferência de dados para o cofre só começa após a fase de instantâneo ser concluída.

  ![Status do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Existem duas **Sub Tarefas** em execução no backend, uma para o trabalho de backup front-end que pode ser verificada a partir da lâmina de detalhes do trabalho de **backup,** conforme dado abaixo:

  ![Status do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Os **dados de transferência para a** fase do cofre podem levar vários dias para serem concluídos, dependendo do tamanho dos discos, do churn por disco e de vários outros fatores.

O status do trabalho pode variar dependendo dos seguintes cenários:

**Instantâneo** | **Transferir os dados para o cofre** | **Status do Trabalho**
--- | --- | ---
Concluído | Em andamento | Em andamento
Concluído | Ignorado | Concluído
Concluído | Concluído | Concluído
Concluído | Falhou | Concluído com aviso
Falhou | Falhou | Falhou

Agora, com essa capacidade, para a mesma VM, dois backups podem ser executados em paralelo, mas em qualquer fase (snapshot, transfer data to vault) apenas uma subtarefa pode ser executada. Assim, em cenários, um trabalho de backup em andamento resultou na falha de backup do dia seguinte será evitado com essa funcionalidade de desacoplamento. Os backups do dia subsequente podem ter o snapshot concluído enquanto **os dados de transferência para o cofre** são ignorados se o trabalho de backup de um dia anterior estiver em andamento.
O ponto de recuperação incremental criado no cofre capturará todo o churn do último ponto de recuperação criado no cofre. Não há impacto de custo no usuário.

## <a name="optional-steps"></a>Etapas opcionais

### <a name="install-the-vm-agent"></a>Instalar o agente de VM

O Backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se a VM foi criada a partir de uma imagem do Azure Marketplace, o agente será instalado e executado. Se você criar uma VM personalizada ou migrar uma máquina no local, talvez seja necessário instalar o agente manualmente, conforme resumido na tabela.

**Vm** | **Detalhes**
--- | ---
**Windows** | 1. [Baixe e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o arquivo MSI do agente.<br/><br/> 2. Instale com permissões de admin na máquina.<br/><br/> 3. Verifique a instalação. Em *C:\WindowsAzure\Packages* na VM, clique com o botão direito do mouse **WaAppAgent.exe** > **Properties**. Na guia **Detalhes,** **a versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se você estiver atualizando o agente, certifique-se de que nenhuma operação de backup está sendo realizada e [reinstale o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale usando um rpm ou um pacote DEB do repositório de pacotes da sua distribuição. Este é o método preferido para instalar e atualizar o agente Azure Linux. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não recomendamos instalá-lo a partir daí.<br/><br/> Se você estiver atualizando o agente, certifique-se de que nenhuma operação de backup está sendo realizada e atualize os binários.

>[!NOTE]
> **O Azure Backup agora suporta backup e restauração de disco seletivo usando a solução de backup da Máquina Virtual do Azure.**
>
>Hoje, o Azure Backup suporta backup de todos os discos (Sistema Operacional e dados) em uma VM em conjunto usando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, você tem a opção de fazer backup de um ou poucos dos muitos discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda que você tenha um subconjunto de discos restaurado do ponto de recuperação dado durante a operação de restauração. Isso se aplica para restaurar tanto a partir do instantâneo quanto do cofre.
>
>**Para se inscrever para a pré-visualização, escreva para nós emAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Próximas etapas

* Soluciona problemas com [agentes azure VM](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup do Azure VM](backup-azure-vms-troubleshoot.md).
* [Restaurar](backup-azure-arm-restore-vms.md) VMs azure.
