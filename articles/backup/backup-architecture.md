---
title: Visão geral da arquitetura
description: Fornece uma visão geral da arquitetura, componentes e processos usados pelo serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273612"
---
# <a name="azure-backup-architecture-and-components"></a>Arquitetura e componentes do Azure Backup

Você pode usar o serviço de backup do [Azure](backup-overview.md) para fazer backup de dados na plataforma em nuvem do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos de Backup do Azure.

## <a name="what-does-azure-backup-do"></a>O que o Backup do Azure faz?

O Backup do Azure faz backup dos dados, do estado da máquina e das cargas de trabalho em execução em máquinas locais e instâncias de máquina virtual (VM) do Azure. Há vários cenários de Backup do Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona o Backup do Azure?

Você pode fazer backup de máquinas e dados usando uma série de métodos:

- **Fazer backup dos computadores locais**:
  - Você pode fazer backup de máquinas Windows no local diretamente para o Azure usando o agente Azure Backup Microsoft Azure Recovery Services (MARS). Computadores Linux não tem suporte.
  - Você pode fazer backup de máquinas no local para um servidor de backup - o DPM (System Center Data Protection Manager) ou o Microsoft Azure Backup Server (MABS). Em seguida, você pode fazer backup do servidor de backup para um cofre de Serviços de Recuperação no Azure.

- **Fazer backup das VMs do Azure**:
  - Você pode fazer backup de VMs do Azure diretamente. O Azure Backup instala uma extensão de backup para o agente Azure VM que está sendo executado na VM. Essa extensão faz backup de toda a VM.
  - Você pode fazer backup de arquivos e pastas específicos na VM do Azure executando o agente MARS.
  - Você pode fazer backup das VMs do Azure para o MABS que está sendo executado no Azure, e você pode então fazer backup do MABS para um cofre de Serviços de Recuperação.

Saiba mais sobre [o que você pode fazer backup](backup-overview.md) e sobre [cenários de backup suportados.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Onde os dados são armazenados em backup?

O Azure Backup armazena dados de backup em um cofre de Serviços de Recuperação. Um cofre é uma entidade de armazenamento on-line no Azure que é usada para armazenar dados, como cópias de backup, pontos de recuperação e políticas de backup.

Os cofres dos Serviços de Recuperação têm os seguintes recursos:

- Os cofres facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.
- Em cada assinatura do Azure, você pode criar até 500 cofres.
- Você pode monitorar itens de backup em um cofre, incluindo VMs azure e máquinas no local.
- É possível gerenciar o acesso ao cofre com o [controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do Azure.
- Você especifica como os dados no cofre são replicados para redundância:
  - **LRS (Locally redundante storage, armazenamento redundante)**: Para proteger contra falhas em um data center, você pode usar LRS. O LRS replica os dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **GrS (Geo-redundante)**: Para proteger contra paralisações em toda a região, você pode usar GRS. O GRS replica seus dados para uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Por padrão, os cofres dos Serviços de Recuperação usam GRS.

## <a name="backup-agents"></a>Agentes de backup

O Azure Backup fornece diferentes agentes de backup, dependendo do tipo de máquina que está sendo copiada:

**Agente** | **Detalhes**
--- | ---
**Agente de MARTE** | <ul><li>É executado em máquinas individuais do Windows Server no local para fazer backup de arquivos, pastas e o estado do sistema.</li> <li>É executado em VMs do Azure para fazer backup de arquivos, pastas e o estado do sistema.</li> <li>É executado em servidores DPM/MABS para fazer backup do disco de armazenamento local DPM/MABS para o Azure.</li></ul>
**Extensão da VM do Azure** | É executado em VMs do Azure para fazer backup delas em um cofre.

## <a name="backup-types"></a>Tipos de backup

A tabela a seguir explica os diferentes tipos de backups e quando eles são usados:

**Tipo de backup** | **Detalhes** | **Uso**
--- | --- | ---
**Cheio** | Um backup completo contém toda a fonte de dados. É preciso mais largura de banda de rede do que backups diferenciais ou incrementais. | Usado no backup inicial.
**Differential** |  Um backup diferencial armazena os blocos que mudaram desde o backup completo inicial. Usa uma quantidade menor de rede e armazenamento e não mantém cópias redundantes de dados inalterados.<br/><br/> Ineficiente porque os blocos de dados que são inalterados entre backups posteriores são transferidos e armazenados. | Não é usada pelo Backup do Azure.
**Incremental** | Um backup incremental armazena apenas os blocos de dados que mudaram desde o backup anterior. Alto armazenamento e eficiência de rede. <br/><br/> Com backup incremental, não há necessidade de complementar com backups completos. | Usado pelo MABS/DPM para backups em disco e usado em todos os backups no Azure. Não usado para backup do SQL Server.

## <a name="sql-server-backup-types"></a>Tipos de backup do SQL Server

A tabela a seguir explica os diferentes tipos de backups usados para bancos de dados SQL Server e com que frequência eles são usados:

**Tipo de backup** | **Detalhes** | **Uso**
--- | --- | ---
**Backup completo** | Um backup completo do banco de dados faz backup do banco de dados inteiro. Ele contém todos os dados em um banco de dados específico ou em um conjunto de arquivos ou arquivos. Um backup completo também contém registros suficientes para recuperar esses dados. | No máximo, você pode acionar um backup completo por dia.<br/><br/> Você pode optar por fazer um backup completo em um intervalo diário ou semanal.
**Backup diferencial** | Um backup diferencial é baseado no backup de dados completos mais recente e anterior.<br/><br/> Ele captura apenas os dados que foram alterados desde o backup completo. |  No máximo, você pode acionar um backup diferencial por dia.<br/><br/> Você não pode configurar um backup completo e um backup diferencial no mesmo dia.
**Backup do registro de transações** | Um backup de log permite a restauração pontual até um determinado segundo. | No máximo, você pode configurar backups de log de transações a cada 15 minutos.

### <a name="comparison-of-backup-types"></a>Comparação de tipos de backup

O consumo do armazenamento, o RTO (objetivo do tempo de recuperação) e o consumo de rede variam para cada tipo de backup. A imagem a seguir mostra uma comparação dos tipos de backup:

- A fonte de dados A é composta por 10 blocos de armazenamento, A1-A10, que são copiados mensalmente.
- Os blocos A2, A3, A4 e A9 alteram-se no primeiro mês e o bloco A5 altera-se no próximo mês.
- Para backups diferenciais, no segundo mês é feito o backup dos blocos que foram alterados, A2, A3, A4 e A9. No terceiro mês, é feito novamente o backup desses mesmos blocos, junto com o bloco A5 que foi alterado. O backup dos blocos alterados continua a ser feito até que o próximo backup completo aconteça.
- Para backups incrementais, no segundo mês, os blocos A2, A3, A4 e A9 são marcados como alterados e transferidos. No terceiro mês, somente o bloco alterado A5 é marcado e transferido.

![Imagem mostrando comparações de métodos de backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Recursos de backup

A tabela a seguir resume os recursos suportados para os diferentes tipos de backup:

**Recurso** | **Backup direto de arquivos e pastas (usando o agente MARS)** | **Azure VM Backup** | **Máquinas ou aplicativos com DPM/MABS**
--- | --- | --- | ---
Fazer backup para o cofre | ![Sim][green] | ![Sim][green] | ![Sim][green]
Fazer backup no disco DPM/MABS e depois para o Azure | | | ![Sim][green]
Compactar os dados enviados para backup | ![Sim][green] | Nenhuma compactação é usada durante a transferência de dados. O armazenamento está um pouco inflado, mas a restauração é mais rápida.  | ![Sim][green]
Executa o backup incremental |![Sim][green] |![Sim][green] |![Sim][green]
Fazer backup de discos com eliminação de duplicação | | | ![Parcialmente][yellow]<br/><br/> Para servidores do MABS/DPM implantados apenas localmente.

![Chave de tabela](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Conceitos básicos de política de backup do Microsoft Azure

- Uma política de backup é criada por cofre.
- Uma política de backup pode ser criada para o backup das seguintes cargas de trabalho
  - VM do Azure
  - SQL na VM do Azure
  - Compartilhamento de arquivos do Azure
- Uma política pode ser atribuída a muitos recursos. Uma política de backup de VM do Azure pode ser usada para proteger várias VMs do Azure.
- Uma política consiste em dois componentes
  - Agenda: Ao fazer o backup
  - Retenção: Por quanto tempo cada backup deverá ser mantido.
- A programação pode ser definida como "diária" ou "semanal" com um ponto específico do tempo.
- A retenção pode ser definida para pontos de backup "diários", "semanais", "mensais", "anuais".
- "semanal" refere-se a um backup em um determinado dia da semana, "mensalmente" significa um backup em um determinado dia do mês e "anual" refere-se a um backup em um determinado dia do ano.
- A retenção para pontos de backup "mensais" e "anuais" é chamada de "LongTermRetention".
- Quando um cofre é criado, uma política para backups do Azure VM chamada "DefaultPolicy" também é criada e pode ser usada para fazer backup das VMs do Azure.

## <a name="architecture-built-in-azure-vm-backup"></a>Arquitetura: Backup azure VM incorporado

1. Quando você habilita o backup de uma VM Azure, um backup é executado de acordo com o cronograma especificado.
1. Durante o primeiro backup, uma extensão de backup é instalada na VM se a VM estiver em execução.
    - Para VMs do Windows, a extensão VMSnapshot está instalada.
    - Para VMs Linux, a extensão VMSnapshot Linux está instalada.
1. A extensão tem um instantâneo no nível de armazenamento.
    - Para VMs do Windows que estão em execução, o Backup coordena com o VSS (Windows Volume Shadow Copy Service, serviço de cópia de sombra de volume do Windows) para tirar um instantâneo consistente com o aplicativo da VM. Por padrão, o backup recebe backups VSS completos. Se o Backup não conseguir obter um instantâneo consistente do aplicativo, ele gerará um instantâneo consistente com o arquivo.
    - Para VMs Linux, o backup leva um instantâneo consistente com o arquivo. Para instantâneos consistentes com aplicativos, você precisa personalizar manualmente scripts pré/post.
    - O backup é otimizado pela execução do backup em cada disco de VM em paralelo. Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados.
1. Após o instantâneo, os dados são transferidos para o cofre.
    - Apenas blocos de dados alterados desde o último backup são copiados.
    - Os dados não são criptografados. O Azure Backup pode fazer backup de VMs do Azure que foram criptografadas usando o Azure Disk Encryption.
    - Dados de instantâneo não podem ser imediatamente copiados no cofre. Nos horários de pico, o backup pode levar algumas horas. O tempo total de backup de uma VM será inferior a 24 horas para políticas diárias de backup.
1. Depois que os dados são enviados para o cofre, um ponto de recuperação é criado. Por padrão, os instantâneos são retidos por dois dias antes de serem excluídos. Esse recurso permite restaurar a operação a partir desses instantâneos, reduzindo assim os tempos de restauração. Reduz o tempo necessário para transformar e copiar dados do cofre. Consulte [o recurso de restauração instantânea do backup do Azure](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

Você não precisa permitir explicitamente que a conectividade com a internet faça backup das Suas VMs do Azure.

![Backup de VMs do Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arquitetura: Backup direto de máquinas do Windows Server no local ou arquivos ou pastas Do Azure VM

1. Para configurar o cenário, você baixa e instala o agente MARS na máquina. Em seguida, selecione o que fazer backup, quando os backups serão executados e por quanto tempo eles serão mantidos no Azure.
1. O backup inicial é executado de acordo com as configurações de backup.
1. O agente MARS usa vss para tirar um instantâneo point-in-time dos volumes selecionados para backup.
    - O agente MARS usa apenas a operação de gravação do sistema Windows para capturar o instantâneo.
    - Como o agente não usa nenhum aplicativo de escritores VSS, ele não captura instantâneos consistentes com aplicativos.
1. Depois de tirar o instantâneo com o VSS, o agente MARS cria um disco rígido virtual (VHD) na pasta de cache especificada quando configurou o backup. O agente também armazena cheques para cada bloco de dados.
1. Backups incrementais são executados de acordo com o cronograma especificado, a menos que você execute um backup demanda.
1. Nos backups incrementais, os arquivos alterados são identificados e um novo VHD é criado. O VHD é compactado e criptografado, e então é enviado para o cofre.
1. Após o backup incremental terminar, o novo VHD é mesclado com o VHD criado após a replicação inicial. Este VHD mesclado fornece o estado mais recente a ser usado para comparação para backup contínuo.

![Backup de máquinas Windows Server no local com agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: Fazer backup do DPM/MABS

1. Você instala o agente de proteção DPM ou MABS em máquinas que deseja proteger. Em seguida, adicione as máquinas a um grupo de proteção DPM.
    - Para proteger as máquinas locais, o servidor do DPM ou MABS deverá ser local.
    - Para proteger as VMs do Azure, o servidor do MABS deverá estar no Azure, em execução como uma VM do Azure.
    - Com o DPM/MABS, você pode proteger volumes de backup, compartilhamentos, arquivos e pastas. Você também pode proteger o estado do sistema de uma máquina (metal nu) e pode proteger aplicativos específicos com configurações de backup com reconhecimento de aplicativos.
1. Ao configurar a proteção para uma máquina ou aplicativo no DPM/MABS, você seleciona fazer backup do disco local MABS/DPM para armazenamento a curto prazo e para o Azure para proteção on-line. Você também especifica quando o backup para o armazenamento DPM/MABS local deve ser executado e quando o backup on-line para o Azure deve ser executado.
1. O disco da carga de trabalho protegida é feito em backup nos discos MABS/DPM locais, de acordo com o cronograma especificado.
1. Os discos DPM/MABS são copiados para o cofre pelo agente MARS que está sendo executado no servidor DPM/MABS.

![Backup de máquinas e cargas de trabalho protegidas pelo DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Armazenamento da VM do Azure

As VMs do Azure usam discos para armazenar seu sistema operacional, aplicativos e dados. Cada Azure VM tem pelo menos dois discos: um disco para o sistema operacional e um disco temporário. As VMs do Azure também podem ter discos de dados para dados do aplicativo. Os discos são armazenados como VHDs.

- Os VHDs são armazenados como blobs de página em contas de armazenamento padrão ou premium no Azure:
  - **Armazenamento padrão:** Suporte de disco confiável e de baixo custo para VMs executando cargas de trabalho que não são sensíveis à latência. O armazenamento padrão pode usar discos padrão de unidade de estado sólido (SSD) ou discos de disco rígido padrão (HDD).
  - **Armazenamento premium:** Suporte a disco de alto desempenho. Usa discos SSD premium.
- Há diferentes níveis de desempenho de discos:
  - **Disco HDD padrão:** Apoiado por HDDs, e usado para armazenamento econômico.
  - **Disco SSD padrão:** Combina elementos de discos SSD premium e discos HDD padrão. Oferece desempenho e confiabilidade mais consistentes do que o HDD, mas ainda é econômico.
  - **Disco SSD premium:** Apoiado por SSDs, e fornece alto desempenho e baixa latência para VMs que estão executando cargas de trabalho intensivas em I/O.
- Os discos podem ser gerenciados ou não:
  - **Discos não gerenciados:** Tipo tradicional de discos usados por VMs. Com esses discos, você cria sua própria conta de armazenamento e a especifica ao criar o disco. Em seguida, você precisa descobrir como maximizar os recursos de armazenamento para suas VMs.
  - **Discos gerenciados:** O Azure cria e gerencia as contas de armazenamento para você. Você especifica o tamanho do disco e o nível de desempenho e o Azure cria discos gerenciados para você. À medida que você adiciona discos e dimensiona VMs, o Azure lida com as contas de armazenamento.

Para obter mais informações sobre o armazenamento em disco e os tipos de disco disponíveis para VMs, consulte estes artigos:

- [Discos gerenciados do Azure para VMs windows](../virtual-machines/windows/managed-disks-overview.md)
- [Discos gerenciados do Azure para VMs Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipos de disco disponíveis para VMs](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Fazer backup e restaurar VMs Azure com armazenamento premium

Você pode fazer backup das VMs do Azure usando armazenamento premium com backup do Azure:

- Durante o processo de backup de VMs com armazenamento premium, o serviço de backup cria um local temporário de preparação, chamado *AzureBackup,* na conta de armazenamento. O tamanho do local de encenação é igual ao tamanho do instantâneo do ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium tenha espaço livre suficiente para acomodar o local de preparo temporário. Para obter mais informações, consulte [metas de escalabilidade para contas de armazenamento blob de página premium](../storage/blobs/scalability-targets-premium-page-blobs.md). Não modifique o local de preparo.
- Após a conclusão do trabalho de backup, o local de preparo será excluído.
- O preço do armazenamento usado para o local de preparo é consistente com os [preços do armazenamento premium](../virtual-machines/windows/disks-types.md#billing).

Quando você restaura as VMs do Azure usando armazenamento premium, você pode restaurá-las para armazenamento premium ou padrão. Normalmente, você os restauraria para armazenamento premium. Mas se você precisar apenas de um subconjunto de arquivos da VM, pode ser econômico restaurá-los para o armazenamento padrão.

### <a name="back-up-and-restore-managed-disks"></a>Fazer backup e restaurar discos gerenciados

Você pode fazer backup das VMs do Azure com discos gerenciados:

- Faça o backup de VMs com discos gerenciados da mesma maneira que faria com qualquer outra VM do Azure. Você pode fazer backup da VM diretamente nas configurações da máquina virtual ou pode habilitar o backup para VMs no cofre dos Serviços de Recuperação.
- Você pode fazer backup de VMs em discos gerenciados por meio de coleções de RestorePoint criadas com base em discos gerenciados.
- O Azure Backup também suporta o backup de VMs com discos gerenciados que foram criptografados usando o Azure Disk Encryption.

Quando você restaura VMs com discos gerenciados, você pode restaurar para uma VM completa com discos gerenciados ou para uma conta de armazenamento:

- Durante o processo de restauração, o Azure lida com os discos gerenciados. Se você estiver usando a opção de conta de armazenamento, você gerenciará a conta de armazenamento criada durante o processo de restauração.
- Se você restaurar uma VM gerenciada que esteja criptografada, certifique-se de que as chaves e segredos da VM existam no cofre de chaves antes de iniciar o processo de restauração.

## <a name="next-steps"></a>Próximas etapas

- Revise a matriz de suporte para [saber sobre recursos suportados e limitações para cenários de backup](backup-support-matrix.md).
- Configure backup para um desses cenários:
  - [Fazer backup das VMs azure](backup-azure-arm-vms-prepare.md).
  - [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
  - [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
  - [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
