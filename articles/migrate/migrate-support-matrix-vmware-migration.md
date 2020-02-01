---
title: Suporte para migração do VMware nas migrações para Azure
description: Saiba mais sobre o suporte para migração de VM do VMware nas migrações para Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 6593d4de6823f15f570ab8922d76cbe84fb0e348
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901549"
---
# <a name="support-matrix-for-vmware-migration"></a>Matriz de suporte para migração do VMware

Este artigo resume as configurações de suporte e as limitações para migrar VMs VMware com [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você estiver procurando informações sobre como avaliar VMs VMware para migração para o Azure, examine a [matriz de suporte de avaliação](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Opções de migração

Você pode migrar VMs VMware de duas maneiras:

- Com a migração sem agente: migre as máquinas virtuais independentemente de precisar instalar nada nelas. Você implanta o [dispositivo migrações para Azure](migrate-appliance.md) para migração sem agente.
- Com a migração baseada em agente: Instale um agente na VM para replicação. Para a migração baseada em agente, você precisa implantar um [dispositivo de replicação](migrate-replication-appliance.md).

Examine [Este artigo](server-migrate-overview.md) para descobrir qual método você deseja usar.

## <a name="migration-limitations"></a>Limitações de migração

- Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, faça a replicação em grupos de 10.
- Para migração sem agente de VMware, você pode executar até 100 replicações simultaneamente.

## <a name="agentless-vmware-servers"></a>Sem agente-servidores VMware

**VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5,5, 6,0, 6,5 ou 6,7.
**VMware vSphere host ESXI** | Versão 5,5, 6,0, 6,5 ou 6,7.
**vCenter Server permissões** | A migração sem agente usa o [dispositivo de migração](migrate-appliance.md). O dispositivo precisa destas permissões:<br/><br/> - **datastore. procurar**: permitir a navegação de arquivos de log da VM para solucionar problemas de criação e exclusão de instantâneos.<br/><br/> **Datastore. LowLevelFileOperations**: permitir operações de leitura/gravação/exclusão/renomeação no navegador de repositório de armazenamento, para solucionar problemas de criação e exclusão de instantâneos.<br/><br/> - **VirtualMachine. Configuration. DiskChangeTracking**: permitir habilitar ou desabilitar o controle de alterações de discos de VM, para efetuar pull de blocos de dados alterados entre instantâneos.<br/><br/> - **VirtualMachine. Configuration. DiskLease**: permitir operações de concessão de disco para uma VM, ler o disco usando o kit de desenvolvimento de disco virtual (VDDK) VMware vSphere.<br/><br/> - **VirtualMachine. Provisioning. AllowReadOnlyDiskAccess**: permitir a abertura de um disco em uma VM, para ler o disco usando o VDDK.<br/><br/> - **VirtualMachine. Provisioning. AllowVirtualMachineDownload**: permite operações de leitura em arquivos associados a uma VM, para baixar os logs e solucionar problemas se ocorrer falha.<br/><br/> -* * VirtualMachine. SnapshotManagement. * * *: permitir a criação e o gerenciamento de instantâneos de VM para replicação.<br/><br/> - **máquina virtual. interação. desligar**: permite que a VM seja desligada durante a migração para o Azure.



## <a name="agentless-vmware-vms"></a>Sem agente-VMs VMware

**Suporte** | **Detalhes**
--- | ---
**Sistemas operacionais com suporte** | Os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte do Azure podem ser migrados usando a migração sem agente.
**Alterações necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para os seguintes sistemas operacionais:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso.
**Inicialização do Linux** | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos.
**Inicialização UEFI** | As VMs com inicialização UEFI não têm suporte para migração.
**Tamanho do disco** | disco do sistema operacional de 2 TB; 4 TB para discos de dados.
**Limites de disco** |  Até 60 discos por VM.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Sem suporte.
**Discos independentes** | Sem suporte.
**Discos de RDM/PassThrough** | Se as VMs tiverem discos RDM ou de passagem, esses discos não serão replicados para o Azure.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**destinos iSCSI** | Não há suporte para VMs com destinos iSCSI para migração sem agente.
**E/s de vários caminhos** | Sem suporte.
**VMotion de armazenamento** | Sem suporte. A replicação não funcionará se uma VM usar o Storage vMotion.
**NICs agrupadas** | Sem suporte.
**IPv6** | Sem suporte.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD Premium) no Azure.
**Replicação simultânea** | 100 VMs por vCenter Server. Se você tiver mais, migre-os em lotes de 100.


## <a name="agentless-azure-migrate-appliance"></a>Sem agente-dispositivo de migrações para Azure 
A migração sem agente usa o dispositivo de migrações para Azure, implantado em uma VM VMware.

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o dispositivo precisa acessar.

## <a name="agentless-ports"></a>Portas sem agente

**Dispositivo** | **Conexão**
--- | ---
Baseado | Conexões de saída na porta 443 para carregar dados replicados no Azure e para se comunicar com os serviços de migração do Azure orquestrando a replicação e a migração.
Servidor vCenter | Conexões de entrada na porta 443 para permitir que o dispositivo coordene a replicação-criar instantâneos, copiar dados, liberar instantâneos
host vSphere/ESXI | Entrada na porta TCP 902 para o dispositivo replicar dados de instantâneos.


## <a name="agent-based-vmware-servers"></a>Servidores VMware baseados em agente
Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Requisitos da VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5,5, 6,0, 6,5 ou 6,7.
**VMware vSphere host ESXI** | Versão 5,5, 6,0, 6,5 ou 6,7.
**vCenter Server permissões** | Uma conta somente leitura para vCenter Server.

## <a name="agent-based-vmware-vms"></a>VMs do VMware baseadas em agente

A tabela resume o suporte de VM do VMware para VMs VMware que você deseja migrar usando a migração baseada em agente.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho da máquina** | As migrações para Azure dão suporte à migração de qualquer carga de trabalho (digamos Active Directory, SQL Server, etc.) em execução em um computador com suporte.
**Sistemas operacionais** | Para obter as informações mais recentes, examine o [suporte do sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para site Recovery. As migrações para Azure fornecem suporte ao sistema operacional de VM idêntico.
**Armazenamento de sistema de arquivos/convidado do Linux** | Para obter as informações mais recentes, examine o [suporte do sistema de arquivos do Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para site Recovery. As migrações para Azure têm suporte ao sistema de arquivos Linux idêntico.
**Rede/armazenamento** | Para obter as informações mais recentes, examine os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para site Recovery. As migrações para Azure fornecem requisitos de rede/armazenamento idênticos.
**Requisitos do Azure** | Para obter as informações mais recentes, examine os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [computação](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) do [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para site Recovery. As migrações para Azure têm requisitos idênticos para a migração do VMware.
**Serviço de mobilidade** | O agente do serviço de mobilidade deve ser instalado em cada VM que você deseja migrar.
**Inicialização UEFI** | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS.<br/><br/> O disco do sistema operacional deve ter até quatro partições, e os volumes devem ser formatados com NTFS.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD Premium) no Azure.
**Tamanho do disco** | disco do sistema operacional de 2 TB; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por VM.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Sem suporte.
**Discos independentes** | Com suporte.
**Discos de passagem** | Com suporte.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**destinos iSCSI** | Não há suporte para VMs com destinos iSCSI para migração sem agente.
**E/s de vários caminhos** | Sem suporte.
**VMotion de armazenamento** | Com suporte
**NICs agrupadas** | Sem suporte.
**IPv6** | Sem suporte.




## <a name="agent-based-replication-appliance"></a>Dispositivo de replicação baseado em agente 

Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no Hub migrações para Azure, o dispositivo executa o Windows Server 2016 e está em conformidade com os requisitos de suporte. Se você configurar o dispositivo de replicação manualmente em um servidor físico, verifique se ele está em conformidade com os requisitos.

- Saiba mais sobre [os requisitos do dispositivo de replicação](migrate-replication-appliance.md#appliance-requirements) para VMware.
- O MySQL deve estar instalado no dispositivo. Saiba mais sobre [as opções de instalação](migrate-replication-appliance.md#mysql-installation).
- Saiba mais sobre [URLs](migrate-replication-appliance.md#url-access) e [portas]() que o dispositivo de replicação precisa acessar.

## <a name="agent-based-ports"></a>Portas baseadas em agente

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação local (servidor de configuração) na porta HTTPS 443 de entrada, para o gerenciamento de replicação.<br/><br/> As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
Dispositivo de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.<br/> Por padrão, o servidor de processo é executado no dispositivo de replicação.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verifica os sistemas operacionais de VM VMware com suporte para migração.<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional com suporte. | A verificação falha se não tiver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falha se não tiver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte.
Disco FC | Sem suporte. | A verificação falha se não tiver suporte.
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador.
Nome da VM | De 1 a 63 caracteres.<br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/> -Antes de a migração habilitar o RDP na VM local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall do Windows** -> **aplicativos e recursos permitidos** para redes de **domínio e privadas** . Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada. Além disso, adicione um endereço IP público para a VM. |  


## <a name="next-steps"></a>Próximos passos

[Selecione](server-migrate-overview.md) uma opção de migração VMware.
