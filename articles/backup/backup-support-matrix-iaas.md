---
title: Matriz de suporte para backup de VM do Azure
description: Fornece um resumo de configurações compatíveis e limitações ao fazer backup de VMs do Azure com o serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 86141532e0db80f75c6e79277b36060ecb939a53
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801426"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matriz de suporte para backup de VM do Azure

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações ao fazer backup de VMs do Azure com o backup do Azure.

Outras matrizes de suporte:

- [Matriz de suporte geral](backup-support-matrix.md) para o backup do Azure
- [Matriz de suporte](backup-support-matrix-mabs-dpm.md) para o servidor de backup do Azure/backup do System Center Data Protection Manager (DPM)
- [Matriz de suporte](backup-support-matrix-mars-agent.md) para backup com o agente de serviços de recuperação do Microsoft Azure (MARS)

## <a name="supported-scenarios"></a>Cenários com suporte

Veja como fazer backup e restaurar as VMs do Azure com o serviço de Backup do Azure.

**Cenário** | **Backup** | **Agente** |**Restaurar**
--- | --- | --- | ---
Backup direto de VMs do Azure  | Faça backup de toda a VM.  | Nenhum agente adicional é necessário na VM do Azure. O backup do Azure é instalado e usa uma extensão para o [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) que está em execução na VM. | Restaure da seguinte maneira:<br/><br/> - **Crie uma VM básica**. Isso será útil se a VM não tiver nenhuma configuração especial, como vários endereços IP.<br/><br/> - **Restaure o disco da VM**. Restaure o disco. Em seguida, anexe-o a uma VM existente ou crie uma nova VM do disco usando o PowerShell.<br/><br/> - **Substitua o disco da VM**. Se existir uma VM que usa discos gerenciados (não criptografados), você pode restaurar um disco e usá-lo para substituir um disco existente na VM.<br/><br/> - **Restaure arquivos/pastas específicas**. Você pode restaurar arquivos/pastas de uma VM em vez de uma VM inteira.
Backup direto de VMs do Azure (somente no Windows)  | Fazer backup de arquivos/pastas/volumes específicos. | Instale o [agente dos serviços de recuperação do Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Você pode executar o agente do MARS com a extensão de backup para o agente de VM do Azure para fazer backup da VM no nível de arquivo/pasta. | Restaure arquivos/pastas específicos.
Fazer backup de VM do Azure no servidor de backup  | Fazer backup de arquivos/pastas/volumes; arquivos de estado do sistema/bare-metal; dados de aplicativo para o System Center DPM ou para Backup do Microsoft Azure Server (MABS).<br/><br/> O DPM/MABS faz backup para o cofre de backup. | Instale o agente de proteção do DPM/MABS na VM. O agente de MARS é instalado no DPM/MABS.| Restaure arquivos/pastas/volumes; arquivos de estado do sistema/bare metal; dados de aplicativos.

Saiba mais sobre [o backup usando um servidor de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sobre [os requisitos de suporte](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **O backup do Azure agora oferece suporte a backup e restauração de disco seletivo usando a solução de backup de máquina virtual do Azure.**
>
>Hoje, o backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM em conjunto usando a solução de backup de máquina virtual. Com a funcionalidade excluir disco, você obtém uma opção para fazer backup de um ou alguns dos vários discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite que você tenha um subconjunto de discos restaurados do ponto de recuperação fornecido durante a operação de restauração. Isso se aplica à restauração tanto do instantâneo quanto do cofre.
>
>**Para se inscrever na versão prévia, escreva-nos emAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Ações de backup compatíveis

**Ação** | **Suporte**
--- | ---
Fazer backup de uma VM que está desligada/VM offline |  Com suporte.<br/><br/> O instantâneo só é consistente com a falha e não com o aplicativo.
Fazer backup de discos após a migração para o Managed disks |  Com suporte.<br/><br/> O backup continuará a funcionar. Nenhuma ação é necessária.
Backup de discos gerenciados depois de habilitar o bloqueio de grupo de recursos | Não há suporte.<br/><br/> O backup do Azure não pode excluir os pontos de restauração mais antigos, e os backups começarão a falhar quando o limite máximo de pontos de restauração for atingido.
Modificar a política de backup de uma VM |  Com suporte.<br/><br/> O backup da VM será feito usando as configurações de agendamento e retenção em nova política. Se as configurações de retenção forem estendidas, os pontos de recuperação existentes serão marcados e mantidos. Se eles forem reduzidos, os pontos de recuperação existentes serão removidos no próximo trabalho de limpeza e eventualmente excluídos.
Cancelar um trabalho de backup| Tem suporte durante o processo de instantâneo.<br/><br/> Não tem suporte quando o instantâneo está em transferência para o cofre.
Backup da VM para uma região ou uma assinatura diferentes |Não há suporte.
Backups por dia (com a extensão de VM do Azure) | Um backup agendado por dia.<br/><br/>O serviço de backup do Azure dá suporte a até nove backups sob demanda por dia, mas a Microsoft recomenda não mais do que quatro backups diários sob demanda para garantir o melhor desempenho.
Backups por dia (por meio do agente MARS) | Três backups agendados por dia.
Backups por dia (por meio do DPM/MABS) | Dois backups agendados por dia.
Backup mensal/anual| Não tem suporte durante o backup com a extensão de VM do Azure. Só há suporte para diários e semanais.<br/><br/> É possível configurar a política para manter backups diários/semanais por um período de retenção mensal/anual.
Ajuste automático de relógio | Não há suporte.<br/><br/> O backup do Azure não é ajustado automaticamente para alterações de horário de verão ao fazer backup de uma VM.<br/><br/>  Modifique a política manualmente, se necessário.
[Recursos de segurança para backups híbridos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Não há suporte para a desabilitação de recursos de segurança.
Fazer backup da VM cuja hora da máquina foi alterada | Não há suporte.<br/><br/> Se a hora da máquina for alterada para uma data/hora futura depois de habilitar o backup para essa VM; No entanto, mesmo se a alteração de tempo for revertida, o backup bem-sucedido não será garantido.  

## <a name="operating-system-support-windows"></a>Suporte do sistema operacional (Windows)

A tabela a seguir resume os sistemas operacionais com suporte ao fazer backup de VMs do Windows Azure.

**Cenário** | **Suporte do so**
--- | ---
Fazer backup com a extensão do agente de VM do Azure | -Cliente do Windows 10 (somente 64 bits) <br/><br/>-Windows Server 2019 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2016 (datacenter/Data Center Core/Standard) <br/><br/> -Windows Server 2012 R2 (datacenter/padrão) <br/><br/> -Windows Server 2008 R2 (RTM e SP1 Standard)  <br/><br/> -Windows Server 2008 (somente 64 bits)
Backup com o agente MARS | Sistemas operacionais [com suporte](backup-support-matrix-mars-agent.md#supported-operating-systems) .
Fazer backup com o DPM/MABS | Sistemas operacionais compatíveis para backup com [MABS](backup-mabs-protection-matrix.md) e [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.

## <a name="support-for-linux-backup"></a>Suporte de backup do Linux

Veja a seguir os itens compatíveis, caso deseje fazer backup de computadores Linux.

**Ação** | **Suporte**
--- | ---
Backup de VMs do Azure no Linux com o agente de VM do Azure para Linux | Backup consistente de arquivos.<br/><br/> Backup consistente com aplicativo usando [scripts personalizados](backup-azure-linux-app-consistent.md).<br/><br/> Durante a restauração, você pode criar uma nova VM, restaurar um disco e usá-lo para criar uma VM ou restaurar um disco e usá-lo para substituir um disco em uma VM existente. Você também pode restaurar arquivos e pastas individuais.
Backup de VMs do Azure no Linux com o agente MARS | Não há suporte.<br/><br/> O agente do MARS só pode ser instalado em computadores Windows.
Backup de VMs do Azure no Linux com DPM/MABS | Não há suporte.

## <a name="operating-system-support-linux"></a>Suporte do sistema operacional (Linux)

Para backups do Linux na VM do Azure, o Backup do Azure oferece suporte para a [lista de distribuições do Linux endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Observe o seguinte:

- O Backup do Azure não dá suporte ao principal sistema operacional Linux.
- O Backup do Azure não dá suporte a sistemas operacionais de 32 bits.
- Outras distribuições do Linux traga seu próprio trabalho podem funcionar contanto que o [agente de VM do Azure para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) esteja disponível na VM e, desde que o Python tenha suporte.
- O backup do Azure não oferece suporte a uma VM do Linux configurada por proxy se não tiver a versão 2,7 do Python instalada.

## <a name="backup-frequency-and-retention"></a>Frequência e retenção de backup

**Configuração** | **limites**
--- | ---
Pontos de recuperação máximos por instância protegida (máquina/carga de trabalho) | 9999.
Tempo máximo de expiração para um ponto de recuperação | Sem limite.
Frequência máxima de backup para o cofre (extensão da VM do Azure) | Uma vez por dia.
Frequência máxima de backup para o cofre (Agente MARS) | Três backups por dia.
Frequência máxima de backup para o DPM/MABS | A cada 15 minutos para SQL Server.<br/><br/> Uma vez por hora para outras cargas de trabalho.
Retenção do ponto de recuperação | Diário, semanal, mensal e anual.
Período de retenção máximo | Depende da frequência de backup.
Pontos de recuperação no disco do DPM/MABS | 64 para servidores de arquivos e 448 para servidores de aplicativos.<br/><br/> Os pontos de recuperação de fita são ilimitados para o DPM local.

## <a name="supported-restore-methods"></a>Métodos de restauração compatíveis

**Opção de restauração** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca rapidamente uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> Você pode especificar um nome para a VM, selecionar o grupo de recursos e a rede virtual (VNet) na qual ele será colocado e especificar uma conta de armazenamento para a VM restaurada. A nova VM deve ser criada na mesma região que a VM de origem.
**Restaurar disco** | Restaura um disco de VM, que pode ser usado para criar uma nova VM.<br/><br/> O Backup do Azure fornece um modelo para ajudá-lo a personalizar e criar uma VM. <br/><br> O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Os discos são copiados para o grupo de recursos que você especificar.<br/><br/> Como alternativa, você pode anexar o disco a uma VM existente ou criar uma nova VM usando o PowerShell.<br/><br/> Essa opção é útil se você quiser personalizar a VM, adicionar configurações que não existiam no momento do backup ou adicionar configurações que devem ser definidas usando o modelo ou o PowerShell.
**Substituir existente** | Você pode restaurar um disco e usá-lo para substituir um disco na VM existente.<br/><br/> A VM atual deve existir. Se ela tiver sido excluída, essa opção não poderá ser usada.<br/><br/> O backup do Azure tira um instantâneo da VM existente antes de substituir o disco e a armazena no local de preparo que você especificar. Os discos existentes conectados à VM são substituídos pelo ponto de restauração selecionado.<br/><br/> O instantâneo é copiado para o cofre e mantido de acordo com a política de retenção. <br/><br/> Após a operação de substituição de disco, o disco original é retido no grupo de recursos. Você pode optar por excluir manualmente os discos originais se eles não forem necessários. <br/><br/>Substituir existente tem suporte para VMs gerenciadas não criptografadas. Não há suporte para discos não gerenciados, [VMs generalizadas](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), ou para VMs [criadas usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauração tem mais ou menos discos do que a VM atual, o número de discos no ponto de restauração reflete apenas a configuração da VM.<br><br> Replace existing não tem suporte para VMs com recursos vinculados (como a [identidade gerenciada pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)) porque o aplicativo cliente de backup não tem permissões nesses recursos ao executar a restauração.
**Entre regiões (região secundária)** | A restauração entre regiões pode ser usada para restaurar VMs do Azure na região secundária, que é uma [região emparelhada do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Você pode restaurar todas as VMs do Azure para o ponto de recuperação selecionado se o backup for feito na região secundária.<br><br> Esse recurso está disponível para as opções abaixo:<br> * [Criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Atualmente, não há suporte para a opção [substituir discos existentes](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Permissões<br> A operação de restauração na região secundária pode ser executada por administradores de backup e administradores de aplicativos.

## <a name="support-for-file-level-restore"></a>Suporte para restauração no nível de arquivos

**Restaurar** | **Porta**
--- | ---
Restaurar arquivos entre sistemas operacionais | Você pode restaurar os arquivos de qualquer máquina que tenha o mesmo sistema operacional (ou compatível) que a VM do backup. Consulte a [tabela de so compatível](backup-azure-restore-files-from-vm.md#system-requirements).
Restaurar arquivos de VMs criptografadas | Não há suporte.
Restaurar arquivos de contas de armazenamento restritas à rede | Não há suporte.
Restaurar arquivos em máquinas virtuais usando Espaços de Armazenamento do Windows | Não há suporte para restaurar a mesma VM.<br/><br/> Em vez disso, restaure os arquivos em uma VM compatível.
Restaurar arquivos em VM Linux usando matrizes LVM/Raid | Não há suporte para restaurar a mesma VM.<br/><br/> Restaure em uma VM compatível.
Restaurar arquivos com configurações de rede especiais | Não há suporte para restaurar a mesma VM. <br/><br/> Restaure em uma VM compatível.

## <a name="support-for-vm-management"></a>Suporte para gerenciamento de VM

A tabela a seguir resume o suporte para backup durante as tarefas de gerenciamento de VM, como adicionar ou substituir discos de VM.

**Restaurar** | **Porta**
--- | ---
Restaure em região/assinatura/zona. | Não há suporte.
Restaurar uma VM existente | Use a opção para substituir disco.
Restaurar o disco com a conta de armazenamento habilitada com a Criptografia do Serviço de Armazenamento do Azure (SSE) | Não há suporte.<br/><br/> Restaure em uma conta que não esteja habilitado com SSE.
Restaurar as contas de armazenamento mistas |Não há suporte.<br/><br/> Com base no tipo de conta de armazenamento, todos os discos restaurados serão premium ou standard e não mistos.
Restaurar VM diretamente em um conjunto de disponibilidade | Para discos gerenciados, você pode restaurar o disco e usar a opção conjunto de disponibilidade no modelo.<br/><br/> Sem suporte para discos não gerenciados. Para discos não gerenciados, restaure o disco e crie uma VM no conjunto de disponibilidade.
Restaurar o backup de VMs não gerenciadas após a atualização para a VM gerenciada|  Com suporte.<br/><br/> Você pode restaurar discos e depois criar uma VM gerenciada.
Restaurar uma VM em um ponto de restauração antes de migrar a VM para discos gerenciados |  Com suporte.<br/><br/> Restaure os discos não gerenciados (padrão), converta os discos restaurados para discos gerenciados e crie uma VM com discos gerenciados.
Restaurar VM que foi excluída. |  Com suporte.<br/><br/> Você pode restaurar a VM usando um ponto de recuperação.
A restauração de uma VM DC (controladora de domínio) que faça parte de uma configuração com vários DCs por meio do portal | Com suporte se você restaurar o disco e criar uma VM usando o PowerShell.
Restaurar a VM em uma rede virtual diferente | Com suporte.<br/><br/> A rede virtual deve estar na mesma assinatura e região.

## <a name="vm-compute-support"></a>Suporte de VM de computação

**Compute** | **Suporte**
--- | ---
Tamanho da VM |Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM.<br/><br/> [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Backup de VMs em [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) |  Com suporte.<br/><br/> Não é possível restaurar uma VM em um conjunto disponível usando a opção para criar rapidamente uma VM. Em vez disso, ao restaurar a VM, restaure o disco e use-o para implantar uma VM ou restaurar um disco e usá-lo para substituir um disco existente.
Fazer backup de VMs implantadas com o [benefício de uso híbrido (Hub)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) |  Com suporte.
Fazer backup de VMs implantadas em um [conjunto de dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Não há suporte.
Fazer backup de VMs implantadas do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicado pela Microsoft, terceiros) | Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior). Não restauramos as VMs do Azure Marketplace apoiadas como VMs, pois elas precisam comprar informações, mas somente como discos.
Fazer backup de VMs implantadas de uma imagem personalizada (terceiros) | Com suporte.<br/><br/> A VM também deve estar em execução em um sistema operacional com suporte.<br/><br/> Ao recuperar arquivos na VM, você pode restaurar apenas em um sistema operacional compatível (não um SO anterior ou posterior).
Fazer backup de VMs que são migradas para o Azure|  Com suporte.<br/><br/> Para fazer backup da VM, o agente da VM deve estar instalado no computador migrado.
Fazer backup de consistência de várias VMs | O backup do Azure não fornece consistência de aplicativos e dados em várias VMs.
Backup com [configurações de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Sem suporte. <br/><br/> Se a restauração da VM do Azure com configurações de diagnóstico for disparada usando a opção [criar nova](backup-azure-arm-restore-vms.md#create-a-vm) , a restauração falhará.
Restauração de VMs fixadas por zona | Com suporte (para a VM que é submetida a backup depois de Jan 2019 e onde a [zona de disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) está disponível).<br/><br/>Atualmente, damos suporte à restauração para a mesma zona que está fixada em VMs. No entanto, se a zona não estiver disponível, a restauração falhará.
VMs Gen2 | Com suporte <br> O backup do Azure dá suporte ao backup e à restauração de [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Quando essas VMs são restauradas do ponto de recuperação, elas são restauradas como [VMs Gen2](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Suporte ao armazenamento de VM

**Componente** | **Suporte**
--- | ---
Discos de dados de VM do Azure | Faça backup de uma VM com 16 ou menos discos de dados.<BR> Para se inscrever para a visualização limitada de VMs com mais de 16 discos (até 32 discos), escreva para nós emAskAzureBackupTeam@microsoft.com
Tamanho do disco de dados | O tamanho do disco individual pode ter até 32 TB e um máximo de 256 TB combinados para todos os discos em uma VM.
Tipo de armazenamento | HDD Standard, SSD Standard SSD Premium.
Discos gerenciados |  Com suporte.
Discos criptografados |  Com suporte.<br/><br/> As VMs do Azure habilitadas com Azure Disk Encryption podem ser submetidas a backup (com ou sem o aplicativo do Azure AD).<br/><br/> As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você deve recuperar toda a VM.<br/><br/> Você pode habilitar a criptografia em VMs que já estão protegidos pelo Backup do Azure.
Discos com o Acelerador de Gravação habilitado | Não há suporte.<br/><br/> O backup do Azure exclui automaticamente os discos com Acelerador de Gravação habilitado durante o backup. Como não é feito backup, você não poderá restaurar esses discos de pontos de recuperação da VM.
Fazer backup & restaurar VMs/discos com eliminação de duplicação | O backup do Azure não oferece suporte à eliminação de duplicação. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  -O backup do Azure não elimina a duplicação entre VMs no cofre dos serviços de recuperação <br/> <br/>  -Se houver VMs no estado de eliminação de duplicação durante a restauração, os arquivos não poderão ser restaurados, pois o cofre não entenderá o formato. No entanto, você poderá executar com êxito a restauração completa da VM.
Adicionar o disco à VM protegida |  Com suporte.
Redimensionar o disco em uma VM protegida |  Com suporte.
Armazenamento compartilhado| Não há suporte para o backup de VMs usando Volume Compartilhado Clusterizado (CSV) ou Servidor de Arquivos de Escalabilidade Horizontal. Os gravadores de CSV provavelmente falharão durante o backup. Na restauração, os discos que contêm volumes CSV podem não vir.
[Discos compartilhados](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Não há suporte.

## <a name="vm-network-support"></a>Suporte de rede de VM

**Componente** | **Suporte**
--- | ---
Número de adaptadores de rede (NICs) | Até o número máximo de NICs com suporte em um tamanho específico de VM do Azure.<br/><br/> As NICs são criadas quando a VM é criada durante o processo de restauração.<br/><br/> O número de NICs na VM restaurada espelha o número de NICs na VM quando você habilitou a proteção. A remoção de NICs depois que você habilita a proteção não afeta a contagem.
Balanceador de carga interno/externo | Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
Vários endereços IP reservados | Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com múltiplos adaptadores de rede|  Com suporte. <br/><br/> [Saiba mais](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sobre a restauração de VMs com configurações de rede especiais.
VMs com endereços IP públicos|  Com suporte.<br/><br/> Associe um endereço IP público existente à NIC ou crie um endereço e associe-o à NIC após a restauração.
NSG (Grupo de Segurança de Rede) na NIC/sub-rede. | Com suporte.
Endereço IP estático | Não há suporte.<br/><br/> Uma nova VM que é criada a partir de um ponto de restauração recebe um endereço IP dinâmico.<br/><br/> Para VMs clássicas, não é possível fazer backup de uma VM com um endereço IP reservado e nenhum ponto de extremidade definido.
Endereço IP dinâmico | Com suporte.<br/><br/> Se a NIC na VM de origem usar o endereçamento de IP dinâmico, por padrão, a NIC na VM restaurada também a usará.
Gerenciador de Tráfego do Azure|  Com suporte.<br/><br/>Se a VM com backup estiver no Gerenciador de tráfego, adicione manualmente a VM restaurada à mesma instância do Gerenciador de tráfego.
DNS do Azure | Com suporte.
DNS Personalizado | Com suporte.
Conectividade de saída por meio do proxy HTTP |  Com suporte.<br/><br/> Não há suporte para proxy autenticado.
Pontos de extremidade de serviço de rede virtual|  Com suporte.<br/><br/> As configurações de firewall e de conta de armazenamento de rede virtual devem permitir o acesso de todas as redes.

## <a name="vm-security-and-encryption-support"></a>Segurança de VM e suporte de criptografia

O Backup do Azure dá suporte à criptografia para dados em trânsito e em repouso:

Tráfego de rede para o Azure:

- O tráfego de backup de servidores para o cofre dos serviços de recuperação é criptografado usando o criptografia AES 256.
- Os dados de backup são enviados por um link HTTPS seguro.
- Os dados de backup são armazenados no cofre dos Serviços de Recuperação na forma criptografada.
- Só você tem a senha para desbloquear esses dados. A Microsoft não pode descriptografar os dados de backup em momento algum.

  > [!WARNING]
  > Depois de configurar o cofre, somente você terá acesso à chave de criptografia. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave for perdida, a Microsoft não poderá recuperar os dados de backup.

Segurança de dados:

- Ao fazer backup de VMs do Azure, você precisa configurar a criptografia *na* máquina virtual.
- O backup do Azure dá suporte a Azure Disk Encryption, que usa o BitLocker em máquinas virtuais do Windows e **DMI-cript** em máquinas virtuais Linux.
- No back-end, o Backup do Azure usa [criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege dados em repouso.

**Computador** | **Em trânsito** | **Em repouso**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | ![Sim][green] | ![Sim][green]
VMs do Azure | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]

## <a name="vm-compression-support"></a>Suporte à compactação de VM

O backup dá suporte à compactação de tráfego de backup, conforme resumido na tabela a seguir. Observe o seguinte:

- Para VMs do Azure, a extensão de VM lê os dados diretamente da conta de armazenamento do Azure pela rede de armazenamento. Não é necessário compactar esse tráfego.
- Se você estiver usando o DPM ou o MABS, poderá economizar largura de banda compactando os dados antes de fazer backup no DPM/MABS.

**Computador** | **Compactar no DPM/MABS (TCP)** | **Compactar para o cofre (HTTPS)**
--- | --- | ---
Computadores Windows locais sem o DPM/MABS | NA | ![Sim][green]
VMs do Azure | NA | NA
Locais/VMs do Azure com o DPM | ![Sim][green] | ![Sim][green]
Locais/VMs do Azure com o MABS | ![Sim][green] | ![Sim][green]

## <a name="next-steps"></a>Próximas etapas

- [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md).
- [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
- [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
- [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
