---
title: Suporte para migração de VMware no Azure Migrate
description: Saiba mais sobre o suporte para migração de VMware VMno no Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 9d8dc4dadc975a0fb69ea207f6062b72231460ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269504"
---
# <a name="support-matrix-for-vmware-migration"></a>Matriz de suporte para migração de VMware

Este artigo resume as configurações de suporte e as limitações para migrar VMs VMware com [o Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você está procurando informações sobre como avaliar VMs VMware para migração para o Azure, revise a [matriz de suporte de avaliação](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Opções de migração

Você pode migrar VMs VMware de algumas maneiras:

- Com migração sem agente: migre as VMs sem precisar instalar nada neles. Você implanta o [aparelho Azure Migrate](migrate-appliance.md) para migração sem agente.
- Com migração baseada em agente: instale um agente na VM para replicação. Para migração baseada em agente, você precisa implantar um [aparelho de replicação](migrate-replication-appliance.md).

Revise [este artigo](server-migrate-overview.md) para descobrir qual método você deseja usar.

## <a name="migration-limitations"></a>Limitações da migração

- Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, então replique em grupos de 10.
- Para migração sem agente de VMware, você pode executar até 100 replicações simultaneamente.

## <a name="agentless-vmware-servers"></a>Servidores Agentless-VMware

**Vmware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5.5, 6.0, 6.5 ou 6.7.
**Host VMware vSphere ESXI** | Versão 5.5, 6.0, 6.5 ou 6.7.
**permissões do vCenter Server** | A migração sem agente usa o [Migrar Appliance](migrate-appliance.md). O aparelho precisa dessas permissões:<br/><br/> - **Datastore.Browse**: Permitir a navegação de arquivos de registro vm para solucionar problemas de criação e exclusão de instantâneos.<br/><br/> - **Datastore.LowLevelFileOperations**: Permitir operações de leitura/gravação/exclusão/renomeação no navegador do datastore para solucionar problemas de criação e exclusão de instantâneos.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: Permitir ativar ou desativar o rastreamento de alterações de discos VM, para puxar blocos alterados de dados entre snapshots.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: Permitir operações de locação de disco para uma VM, para ler o disco usando o VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (especificamente para vSphere 6.0 ou superior) Permitir a abertura de um disco em uma VM para acesso aleatório de leitura no disco usando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: Permitir a abertura de um disco em uma VM, para ler o disco usando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Permitir a abertura de um disco em uma VM, para ler o disco usando o VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: Permite operações de leitura em arquivos associados a uma VM, para baixar os logs e solucionar problemas caso ocorra falha.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Permitir a criação e o gerenciamento de instantâneos de VM para replicação.<br/><br/> - **Máquina virtual.Interaction.Power Off**: Permita que a VM seja desligada durante a migração para o Azure.



## <a name="agentless-vmware-vms"></a>Agentless-VMware VMs

**Suporte** | **Detalhes**
--- | ---
**Sistemas operacionais suportados** | [Os](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) sistemas operacionais Windows e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure podem ser migrados usando migração sem agente.
**Mudanças necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. O Azure Migrate faz essas alterações automaticamente para os seguintes sistemas operacionais:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso.
**Inicialização do Linux** | Se /boot estiver em uma partição dedicada, ele deve residir no disco do SISTEMA OPERACIONAL e não ser espalhado por vários discos.<br/> Se /boot faz parte da partição raiz (/), então a partição '/' deve estar no disco do SISTEMA OPERACIONAL e não abranger outros discos.
**Inicialização UEFI** | As VMs com inicialização UEFI não são suportadas para migração.
**Tamanho do disco** | Disco do SISTEMA OPERACIONAL de 2 TB; 4 TB para discos de dados.
**Limites de disco** |  Até 60 discos por VM.
**Discos/volumes criptografados** | As VMs com discos/volumes criptografados não são suportadas para migração.
**Cluster de disco compartilhado** | Sem suporte.
**Discos independentes** | Sem suporte.
**Discos RDM/passagem** | Se as VMs tiverem discos RDM ou passthrough, esses discos não serão replicados no Azure.
**NFS** | Os volumes NFS montados como volumes nas VMs não serão replicados.
**metas iSCSI** | VMs com alvos iSCSI não são suportados para migração sem agente.
**IO multicaminho** | Sem suporte.
**VMotion de armazenamento** | Sem suporte. A replicação não funcionará se uma VM usar o vMotion de armazenamento.
**NICs em equipe** | Sem suporte.
**IPv6** | Sem suporte.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD premium) no Azure.
**Replicação simultânea** | 100 VMs por vCenter Server. Se você tiver mais, migre-os em lotes de 100.


## <a name="agentless-azure-migrate-appliance"></a>Aparelho de migração sem agente-Azure 
A migração sem agente usa o aparelho Azure Migrate, implantado em uma VMware VMware.

- Saiba mais sobre [os requisitos do aparelho](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o aparelho precisa acessar.

## <a name="agentless-ports"></a>Portas sem agente

**Dispositivo** | **Conexão**
--- | ---
Dispositivos | Conexões de saída na porta 443 para carregar dados replicados para o Azure e para se comunicar com os serviços do Azure Migrate orquestrando replicação e migração.
Servidor vCenter | Conexões de entrada na porta 443 para permitir que o aparelho orquestre a replicação - crie instantâneos, copie dados, libere instantâneos
host vSphere/EXSI | Entrada na porta TCP 902 para que o aparelho replique dados de instantâneos.


## <a name="agent-based-vmware-servers"></a>Servidores VMware baseados em agente
Esta tabela resume o suporte à avaliação e as limitações para servidores de virtualização VMware.

**Requisitos da VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5.5, 6.0, 6.5 ou 6.7.
**Host VMware vSphere ESXI** | Versão 5.5, 6.0, 6.5 ou 6.7.
**permissões do vCenter Server** | Uma conta somente leitura para o vCenter Server.

## <a name="agent-based-vmware-vms"></a>VMs VMware baseados em agente

A tabela resume o suporte ao VMware VMm para VMware VMware vMs que você deseja migrar usando migração baseada em agentes.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho do computador** | O Azure Migrate suporta a migração de qualquer carga de trabalho (digamos Active Directory, servidor SQL, etc.) em execução em uma máquina suportada.
**Sistemas operacionais** | Para obter as informações mais recentes, revise o suporte do [sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para recuperação do site. O Azure Migrate oferece suporte idêntico ao sistema operacional VM.
**Sistema de arquivos Linux/armazenamento de hóspedes** | Para obter as informações mais recentes, revise o suporte ao [sistema de arquivos Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para recuperação de site. O Azure Migrate tem suporte a sistema de arquivos Linux idêntico.
**Rede/Armazenamento** | Para obter as informações mais recentes, revise os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para recuperação do site. O Azure Migrate fornece requisitos idênticos de rede/armazenamento.
**Requisitos do Azure** | Para obter as informações mais recentes, revise os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [cálculo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) da [rede Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para recuperação de site. O Azure Migrate tem requisitos idênticos para migração de VMware.
**Serviço de mobilidade** | O agente de serviço de mobilidade deve ser instalado em cada VM que você deseja migrar.
**Inicialização UEFI** | A VM migrada no Azure será automaticamente convertida em uma VM de inicialização do BIOS.<br/><br/> O disco do SISTEMA OPERACIONAL deve ter até quatro partições, e os volumes devem ser formatados com NTFS.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD premium) no Azure.
**Tamanho do disco** | Disco do SISTEMA OPERACIONAL de 2 TB; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por VM.
**Discos/volumes criptografados** | As VMs com discos/volumes criptografados não são suportadas para migração.
**Cluster de disco compartilhado** | Sem suporte.
**Discos independentes** |  Com suporte.
**Discos de passagem** |  Com suporte.
**NFS** | Os volumes NFS montados como volumes nas VMs não serão replicados.
**metas iSCSI** | VMs com alvos iSCSI não são suportados para migração sem agente.
**IO multicaminho** | Sem suporte.
**VMotion de armazenamento** | Com suporte
**NICs em equipe** | Sem suporte.
**IPv6** | Sem suporte.




## <a name="agent-based-replication-appliance"></a>Dispositivo de replicação baseado em agente 

Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no hub Azure Migrate, o aparelho executa o Windows Server 2016 e cumpre com os requisitos de suporte. Se você configurar o aparelho de replicação manualmente em um servidor físico, certifique-se de que ele está em conformidade com os requisitos.

- Saiba mais sobre [os requisitos do aparelho de replicação](migrate-replication-appliance.md#appliance-requirements) para VMware.
- O MySQL deve ser instalado no aparelho. Conheça as [opções de instalação](migrate-replication-appliance.md#mysql-installation).
- Saiba mais sobre [URLs](migrate-replication-appliance.md#url-access) e [portas](migrate-replication-appliance.md#port-access) que o aparelho de replicação precisa acessar.

## <a name="agent-based-ports"></a>Portas baseadas em agentes

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço mobility em execução em VMs comunica-se com o dispositivo de replicação local (servidor de configuração) na porta HTTPS 443 de entrada, para gerenciamento de replicação.<br/><br/> As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
Aparelho de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza e criptografa e os envia para o armazenamento Azure na porta 443.<br/> Por padrão, o servidor de processo é executado no aparelho de replicação.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs no local replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando a Recuperação do Site executa uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verifica sistemas operacionais VMware VMs suportados para migração.<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional suportado. | A verificação falha se não tiver suporte.
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
Conecte-se após a migração - Windows | Para conectar-se às VMs do Azure executando o Windows após a migração:<br/> - Antes que a migração habilite o RDP na VM no local. Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/> Para acesso vpn de site para site, habilite rdp e permita rdP no **Windows Firewall** -> **Permitido aplicativos e recursos** para redes de domínio e **privado.** Além disso, verifique se a política de SAN do sistema operacional está definida **como OnlineAll**. [Saiba mais](prepare-for-migration.md). |
Conecte-se após a migração-Linux | Para conectar-se às VMs do Azure após a migração usando o SSH:<br/> Antes da migração, na máquina local, verifique se o serviço Secure Shell está definido como Start e que as regras de firewall permitem uma conexão SSH.<br/> Após o failover, na VM do Azure, permita conexões recebidas na porta SSH para as regras do grupo de segurança de rede sobre a falha sobre a VM e para a sub-rede Azure à qual está conectada. Além disso, adicione um endereço IP público para a VM. |  


## <a name="next-steps"></a>Próximas etapas

[Selecione](server-migrate-overview.md) uma opção de migração VMware.
