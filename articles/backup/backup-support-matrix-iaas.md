---
title: Matriz de suporte para backup de VM do Azure
description: Fornece um resumo de configurações compatíveis e limitações ao fazer backup de VMs do Azure com o serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: c30a1d1b30fcc7a12449b44d35704b3b43daa385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247898"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para backup de VM do Azure

Você pode usar o serviço de backup do [Azure](backup-overview.md) para fazer backup de máquinas e cargas de trabalho no local e máquinas virtuais do Azure (VMs). Este artigo resume as configurações e limitações de suporte quando você faz backup das VMs do Azure com o Azure Backup.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para backup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para backup do Azure Backup Server/System Center Data Protection Manager (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Cenários com suporte

Veja como fazer backup e restaurar as VMs do Azure com o serviço de Backup do Azure.

**Cenário** | **Backup** | **Agente** |**Restauração**
--- | --- | --- | ---
Backup direto de VMs do Azure  | Afaste-se de toda a VM.  | Nenhum agente adicional é necessário na VM Azure. O Azure Backup instala e usa uma extensão para o [agente Azure VM](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que está sendo executado na VM. | Restaure da seguinte maneira:<br/><br/> - **Crie uma VM básica**. Isso é útil se a VM não tiver uma configuração especial, como vários endereços IP.<br/><br/> - **Restaure o disco da VM**. Restaure o disco. Em seguida, anexe-o a uma VM existente ou crie uma nova VM a partir do disco usando o PowerShell.<br/><br/> - **Substitua o disco da VM**. Se existir uma VM que usa discos gerenciados (não criptografados), você pode restaurar um disco e usá-lo para substituir um disco existente na VM.<br/><br/> - **Restaure arquivos/pastas específicas**. Você pode restaurar arquivos/pastas de uma VM em vez de de toda a VM.
Backup direto de VMs do Azure (somente no Windows)  | Fazer backup de arquivos/pastas/volume específicos. | Instale o [agente De Serviços de Recuperação do Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Você pode executar o agente do MARS com a extensão de backup para o agente de VM do Azure para fazer backup da VM no nível de arquivo/pasta. | Restaure arquivos/pastas específicos.
Fazer backup de VM do Azure no servidor de backup  | Fazer backup de arquivos/pastas/volumes; estado do sistema/arquivos de metal nu; dados de aplicativos para O DPM do Centro do Sistema ou para o Microsoft Azure Backup Server (MABS).<br/><br/> O DPM/MABS então faz backup do cofre de backup. | Instale o agente de proteção DPM/MABS na VM. O agente de MARS é instalado no DPM/MABS.| Restaure arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos.

Saiba mais sobre backup [usando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre os [requisitos de suporte](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **O Azure Backup agora suporta backup e restauração de disco seletivo usando a solução de backup da Máquina Virtual do Azure.**
>
>Hoje, o Azure Backup suporta backup de todos os discos (Sistema Operacional e dados) em uma VM em conjunto usando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, você tem a opção de fazer backup de um ou poucos dos muitos discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda que você tenha um subconjunto de discos restaurado do ponto de recuperação dado durante a operação de restauração. Isso se aplica para restaurar tanto a partir do instantâneo quanto do cofre.
>
>**Para se inscrever para a pré-visualização, escreva para nós emAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Ações de backup compatíveis

**Ação** | **Suporte**
--- | ---
Fazer backup de uma VM que é shutdown/offline VM |  Com suporte.<br/><br/> O instantâneo só é consistente com a falha e não com o aplicativo.
Fazer backup de discos depois de migrar para discos gerenciados |  Com suporte.<br/><br/> O backup continuará a funcionar. Nenhuma ação é necessária.
Backup de discos gerenciados depois de habilitar o bloqueio de grupo de recursos | Sem suporte.<br/><br/> O Azure Backup não pode excluir os pontos de restauração mais antigos e os backups começarão a falhar quando o limite máximo de pontos de restauração for atingido.
Modificar a política de backup de uma VM |  Com suporte.<br/><br/> A VM será apoiada usando as configurações de cronograma e retenção na nova diretiva. Se as configurações de retenção forem estendidas, os pontos de recuperação existentes serão marcados e mantidos. Se eles forem reduzidos, os pontos de recuperação existentes serão podados no próximo trabalho de limpeza e eventualmente excluídos.
Cancelar um trabalho de backup| Tem suporte durante o processo de instantâneo.<br/><br/> Não tem suporte quando o instantâneo está em transferência para o cofre.
Backup da VM para uma região ou uma assinatura diferentes |Sem suporte.
Backups por dia (com a extensão de VM do Azure) | Um backup agendado por dia.<br/><br/>O serviço de backup do Azure suporta até nove backups demanda por dia, mas a Microsoft recomenda não mais do que quatro backups diários demanda para garantir o melhor desempenho.
Backups por dia (por meio do agente MARS) | Três backups agendados por dia.
Backups por dia (por meio do DPM/MABS) | Dois backups agendados por dia.
Backup mensal/anual| Não tem suporte durante o backup com a extensão de VM do Azure. Só há suporte para diários e semanais.<br/><br/> É possível configurar a política para manter backups diários/semanais por um período de retenção mensal/anual.
Ajuste automático de relógio | Sem suporte.<br/><br/> O Azure Backup não se ajusta automaticamente para alterações de horário de verão ao fazer backup de uma VM.<br/><br/>  Modifique a política manualmente, se necessário.
[Recursos de segurança para backups híbridos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Desativar recursos de segurança não é suportado.
Fazer backup da VM cujo tempo da máquina é alterado | Sem suporte.<br/><br/> Se o tempo da máquina for alterado para uma data-hora futura após ativar o backup para essa VM; No entanto, mesmo que a mudança de tempo seja revertida, o backup bem-sucedido não é garantido.  

## <a name="operating-system-support-windows"></a>Suporte do sistema operacional (Windows)

A tabela a seguir resume os sistemas operacionais suportados ao fazer backup das VMs do Windows Azure.

**Cenário** | **Suporte ao SO**
--- | ---
Fazer backup com a extensão do agente de VM do Azure | - Cliente windows 10 (apenas 64 bits) <br/><br/>- Windows Server 2019 (Data center/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Data center/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Data center/padrão) <br/><br/> - Windows Server 2008 R2 (PADRÃO RTM e SP1)  <br/><br/> - Windows Server 2008 (apenas 64 bits)
Backup com o agente MARS | Sistemas operacionais [suportados.](backup-support-matrix-mars-agent.md#supported-operating-systems)
Fazer backup com o DPM/MABS | Sistemas operacionais compatíveis para backup com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.

## <a name="support-for-linux-backup"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Ação** | **Suporte**
--- | ---
Backup de VMs do Azure no Linux com o agente de VM do Azure para Linux | Backup consistente de arquivos.<br/><br/> Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração, você pode criar uma nova VM, restaurar um disco e usá-lo para criar uma VM, ou restaurar um disco e usá-lo para substituir um disco em uma VM existente. Você também pode restaurar arquivos e pastas individuais.
Backup de VMs do Azure no Linux com o agente MARS | Sem suporte.<br/><br/> O agente do MARS só pode ser instalado em computadores Windows.
Backup de VMs do Azure no Linux com DPM/MABS | Sem suporte.

## <a name="operating-system-support-linux"></a>Suporte do sistema operacional (Linux)

Para backups do Linux na VM do Azure, o Backup do Azure oferece suporte para a [lista de distribuições do Linux endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Observe o seguinte:

- O Backup do Azure não dá suporte ao principal sistema operacional Linux.
- O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.
- Outras distribuições linux próprias podem funcionar desde que o [agente Azure VM para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível na VM, e enquanto o Python for suportado.
- O Azure Backup não suporta um VM Linux configurado por proxy se ele não tiver a versão 2.7 do Python instalada.

## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Configuração** | **Limites**
--- | ---
Pontos máximos de recuperação por instância protegida (máquina/carga de trabalho) | 9999.
Tempo máximo de expiração para um ponto de recuperação | Sem limite.
Frequência máxima de backup para o cofre (extensão da VM do Azure) | Uma vez por dia.
Frequência máxima de backup para o cofre (Agente MARS) | Três backups por dia.
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para o SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diariamente, semanalmente, mensalmente e anualmente.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos e 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.

## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

**Opção de restauração** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca rapidamente uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> Você pode especificar um nome para a VM, selecionar o grupo de recursos e a rede virtual (VNet) na qual ele será colocado e especificar uma conta de armazenamento para a VM restaurada. A nova VM deve ser criada na mesma região da VM de origem.
**Restaurar disco** | Restaura um disco VM, que pode ser usado para criar uma nova VM.<br/><br/> O Backup do Azure fornece um modelo para ajudá-lo a personalizar e criar uma VM. <br/><br> O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Os discos são copiados para o Grupo de recursos especificado.<br/><br/> Como alternativa, você pode anexar o disco a uma VM existente ou criar uma nova VM usando o PowerShell.<br/><br/> Essa opção é útil se você quiser personalizar a VM, adicionar configurações que não existiam no momento do backup ou adicionar configurações que devem ser definidas usando o modelo ou o PowerShell.
**Substituir existente** | Você pode restaurar um disco e usá-lo para substituir um disco na VM existente.<br/><br/> A VM atual deve existir. Se foi excluído, esta opção não pode ser usada.<br/><br/> O Azure Backup tira um instantâneo da VM existente antes de substituir o disco e armazena-o no local de preparação especificado. Os discos existentes conectados à VM são substituídos pelo ponto de restauração selecionado.<br/><br/> O instantâneo é copiado para o cofre e retido de acordo com a política de retenção. <br/><br/> Após a operação do disco de substituição, o disco original é retido no grupo de recursos. Você pode optar por excluir manualmente os discos originais se eles não forem necessários. <br/><br/>Substituir existente tem suporte para VMs gerenciadas não criptografadas. Não há suporte para discos não gerenciados, [VMs generalizadas](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), ou para VMs [criadas usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauração tem mais ou menos discos do que a VM atual, o número de discos no ponto de restauração reflete apenas a configuração da VM.<br><br> Substituir o existente não é suportado por VMs por recursos vinculados (como [identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)porque o aplicativo cliente de backup não tem permissões nesses recursos durante a execução da restauração.
**Região Transversal (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar as VMs do Azure na região secundária, que é uma [região emparelhada azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Você pode restaurar todas as VMs do Azure para o ponto de recuperação selecionado se o backup for feito na região secundária.<br><br> Este recurso está disponível para as opções abaixo:<br> * [Criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> No momento, não suportamos a opção [Substituir discos existentes.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Permissões<br> A operação de restauração na região secundária pode ser realizada por admins de backup e admins de aplicativos.

## <a name="support-for-file-level-restore"></a>Suporte para restauração no nível de arquivos

**Restauração** | **Suportado**
--- | ---
Restaurar arquivos entre sistemas operacionais | Você pode restaurar os arquivos de qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Consulte a [tabela do sistema operacional compatível](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar arquivos em VMs clássicas | Sem suporte.
Restaurar arquivos de VMs criptografadas | Sem suporte.
Restaurar arquivos de contas de armazenamento restritas à rede | Sem suporte.
Restaurar arquivos em máquinas virtuais usando Espaços de Armazenamento do Windows | Não há suporte para restaurar a mesma VM.<br/><br/> Em vez disso, restaure os arquivos em uma VM compatível.
Restaurar arquivos em VM Linux usando matrizes LVM/Raid | Não há suporte para restaurar a mesma VM.<br/><br/> Restaure em uma VM compatível.
Restaurar arquivos com configurações de rede especiais | Não há suporte para restaurar a mesma VM. <br/><br/> Restaure em uma VM compatível.

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela a seguir resume o suporte ao backup durante as tarefas de gerenciamento de VM, como adicionar ou substituir discos VM.

**Restauração** | **Suportado**
--- | ---
Restaure em região/assinatura/zona. | Sem suporte.
Restaurar uma VM existente | Use a opção para substituir disco.
Restaurar o disco com a conta de armazenamento habilitada com a Criptografia do Serviço de Armazenamento do Azure (SSE) | Sem suporte.<br/><br/> Restaure em uma conta que não esteja habilitado com SSE.
Restaurar as contas de armazenamento mistas |Sem suporte.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard e não mistos.
Restaurar VM diretamente em um conjunto de disponibilidade | Para discos gerenciados, você pode restaurar o disco e usar a opção de conjunto de disponibilidade no modelo.<br/><br/> Sem suporte para discos não gerenciados. Para discos não gerenciados, restaure o disco e crie uma VM no conjunto de disponibilidade.
Restaurar backup de VMs não gerenciadas após a atualização para VM gerenciado|  Com suporte.<br/><br/> Você pode restaurar discos e depois criar uma VM gerenciada.
Restaurar uma VM em um ponto de restauração antes de migrar a VM para discos gerenciados |  Com suporte.<br/><br/> Restaure os discos não gerenciados (padrão), converta os discos restaurados para discos gerenciados e crie uma VM com discos gerenciados.
Restaurar VM que foi excluída. |  Com suporte.<br/><br/> Você pode restaurar a VM usando um ponto de recuperação.
A restauração de uma VM DC (controladora de domínio) que faça parte de uma configuração com vários DCs por meio do portal | Suportado se você restaurar o disco e criar uma VM usando o PowerShell.
Restaurar VM em diferentes redes virtuais | Com suporte.<br/><br/> A rede virtual deve estar na mesma assinatura e região.

## <a name="vm-compute-support"></a>Suporte de VM de computação

**Calcular** | **Suporte**
--- | ---
Tamanho da VM |Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup de VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) |  Com suporte.<br/><br/> Você não pode restaurar uma VM em um conjunto disponível usando a opção para criar rapidamente uma VM. Em vez disso, quando você restaurar a VM, restaurar o disco e usá-lo para implantar uma VM, ou restaurar um disco e usá-lo para substituir um disco existente.
Fazer backup de VMs que são implantadas com [o Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) |  Com suporte.
Fazer backup de VMs que são implantadas em um [conjunto de escalas](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Sem suporte.
Fazer backup de VMs que são implantadas no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) | Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior). Não restauramos as VMs do Azure Marketplace apoiadas como VMs, pois essas necessidades de informações de compra, mas apenas como Discos.
Fazer backup de VMs que são implantadas a partir de uma imagem personalizada (terceiros) | Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior).
Fazer backup das VMs que são migradas para o Azure|  Com suporte.<br/><br/> Para fazer backup da VM, o agente da VM deve estar instalado no computador migrado.
Fazer backup da consistência multi-VM | O Azure Backup não fornece consistência de dados e aplicativos em várias VMs.
Backup com [configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Sem suporte. <br/><br/> Se a restauração da VM do Azure com configurações de diagnóstico for acionada usando a opção [Criar novo,](backup-azure-arm-restore-vms.md#create-a-vm) a restauração falhará.
Restauração de VMs com fixação zone | Suportado (para VM que é backup após janeiro de 2019 e onde [a zona](https://azure.microsoft.com/global-infrastructure/availability-zones/) de disponibilidade está disponível).<br/><br/>Atualmente, apoiamos a restauração para a mesma zona que está presa em VMs. No entanto, se a região não estiver disponível, a restauração falhará.
Gen2 VMs | Com suporte <br> O Azure Backup suporta backup e restauração de [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando essas VMs são restauradas a partir do ponto de recuperação, elas são restauradas como [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Suporte ao armazenamento de VM

**Componente** | **Suporte**
--- | ---
Discos de dados de VM do Azure | Fazer backup de uma VM com 16 ou menos discos de dados.<BR> Para se inscrever para a versão prévia privada de VMs com mais de 16 discos (até 32 discos), escreva para nós em AskAzureBackupTeam@microsoft.com
Tamanho do disco de dados | O tamanho do disco individual pode ser de até 32 TB e um máximo de 256 TB combinados para todos os discos em uma VM.
Tipo de armazenamento | HDD padrão, SSD padrão, SSD premium.
Discos gerenciados |  Com suporte.
Discos criptografados |  Com suporte.<br/><br/> As VMs do Azure habilitadas com o Azure Disk Encryption podem ser copiadas (com ou sem o aplicativo Azure AD).<br/><br/> As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você deve recuperar toda a VM.<br/><br/> Você pode habilitar a criptografia em VMs que já estão protegidos pelo Backup do Azure.
Discos com o Acelerador de Gravação habilitado | Sem suporte.<br/><br/> O backup do Azure exclui automaticamente os Discos com o Write Accelerator ativado durante o backup. Uma vez que eles não estão em backup, você não será capaz de restaurar esses discos de Pontos de Recuperação da VM.
Fazer backup & restaurar VMs/discos duplicados | O Azure Backup não suporta deduplicação. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - O Backup do Azure não é duplicado entre VMs no cofre dos Serviços de Recuperação <br/> <br/>  - Se houver VMs em estado de deduplicação durante a restauração, os arquivos não poderão ser restaurados, pois o cofre não entende o formato. No entanto, você será capaz de executar com sucesso a restauração completa da VM.
Adicionar o disco à VM protegida |  Com suporte.
Redimensionar o disco em uma VM protegida |  Com suporte.
Armazenamento compartilhado| O backup de VMs usando CSV (Cluster Hared Volume) ou Scale-Out File Server não é suportado. É provável que os escritores de CSV falhem durante o backup. Na restauração, discos contendo volumes CSV podem não aparecer.
[Discos compartilhados](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Sem suporte.

## <a name="vm-network-support"></a>Suporte de rede de VM

**Componente** | **Suporte**
--- | ---
Número de interfaces de rede (NICs) | Até o número máximo de NICs com suporte em um tamanho específico de VM do Azure.<br/><br/> As NICs são criadas quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs na VM restaurada espelha o número de NICs na VM quando você habilitou a proteção. A remoção de NICs depois de habilitar a proteção não afeta a contagem.
Balanceador de carga interno/externo | Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
Vários endereços IP reservados | Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com múltiplos adaptadores de rede|  Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com endereços IP públicos|  Com suporte.<br/><br/> Associe um endereço IP público existente com a NIC ou crie um endereço e associe-o à NIC após a restauração ser feita.
NSG (Grupo de Segurança de Rede) na NIC/sub-rede. | Com suporte.
Endereço IP estático | Sem suporte.<br/><br/> Uma nova VM criada a partir de um ponto de restauração é atribuída a um endereço IP dinâmico.<br/><br/> Para VMs clássicos, você não pode fazer backup de uma VM com um endereço IP reservado e sem ponto final definido.
Endereço IP dinâmico | Com suporte.<br/><br/> Se a NIC na VM de origem usar endereçamento IP dinâmico, por padrão, a NIC na VM restaurada também irá usá-la.
Gerenciador de Tráfego do Azure|  Com suporte.<br/><br/>Se a VM de backup estiver no Gerenciador de tráfego, adicione manualmente a VM restaurada à mesma instância do Gerenciador de Tráfego.
DNS do Azure | Com suporte.
DNS Personalizado | Com suporte.
Conectividade de saída por meio do proxy HTTP |  Com suporte.<br/><br/> Não há suporte para proxy autenticado.
Pontos de extremidade de serviço de rede virtual|  Com suporte.<br/><br/> As configurações de contas de armazenamento de firewall e rede virtual devem permitir o acesso de todas as redes.

## <a name="vm-security-and-encryption-support"></a>Suporte a segurança e criptografia de VM

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso:

Tráfego de rede para o Azure:

- O tráfego de backup de servidores para o cofre dos Serviços de Recuperação é criptografado usando o Advanced Encryption Standard 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação na forma criptografada.
- Só você tem a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

  > [!WARNING]
  > Depois de configurar o cofre, só você tem acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Segurança dos dados:

- Ao fazer backup das VMs do Azure, você precisa configurar criptografia *dentro* da máquina virtual.
- O Azure Backup suporta o Azure Disk Encryption, que usa o BitLocker em máquinas virtuais do Windows e nos **dm-crypt** em máquinas virtuais Linux.
- No back-end, o Backup do Azure usa [criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege dados em repouso.

**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]

## <a name="vm-compression-support"></a>Suporte à compactação de VM

O backup suporta a compactação do tráfego de backup, conforme resumido na tabela a seguir. Observe o seguinte:

- Para as VMs do Azure, a extensão VM lê os dados diretamente da conta de armazenamento Do Zure na rede de armazenamento. Não é necessário comprimir esse tráfego.
- Se você estiver usando DPM ou MABS, você pode economizar largura de banda comprimindo os dados antes que ele seja feito backup no DPM/MABS.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar para cofre (HTTPS)**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | NA | ![Sim][green]
VMs do Azure | NA | NA
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]

## <a name="next-steps"></a>Próximas etapas

- [Fazer backup das VMs azure](backup-azure-arm-vms-prepare.md).
- [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
- [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
