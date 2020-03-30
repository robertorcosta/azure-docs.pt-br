---
title: Suporte para migração de servidores físicos no Azure Migrate
description: Saiba mais sobre o suporte para migração física de servidores no Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269543"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matriz de suporte para migração física de servidores

Este artigo resume as configurações de suporte e limitações para a migração de servidores físicos com [o Azure Migrate: Migração de servidores](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você está procurando informações sobre como avaliar servidores físicos para migração para o Azure, revise a [matriz de suporte de avaliação](migrate-support-matrix-physical.md).


## <a name="overview"></a>Visão geral

Você pode migrar máquinas locais como servidores físicos, usando a replicação baseada em agentes. Usando essa ferramenta, você pode migrar uma ampla variedade de computadores para o Azure:

- Servidores físicos no local.
- VMs virtualizados por plataformas como Xen, KVM.
- VMs hyper-V ou VMware VMs se por algum motivo você não quiser usar os fluxos [padrão Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware.](server-migrate-overview.md)
- VMs rodando em nuvens privadas.
- VMs rodando em nuvens públicas, como Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Limitações da migração

Você pode selecionar até 10 máquinas de uma só vez para replicação. Se você quiser migrar mais máquinas, então replique em grupos de 10.


## <a name="physical-server-requirements"></a>Requisitos de servidor físico

A tabela resume o suporte a servidores físicos que você deseja migrar usando migração baseada em agentes.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho do computador** | O Azure Migrate suporta a migração de qualquer carga de trabalho (digamos Active Directory, servidor SQL, etc.) em execução em uma máquina suportada.
**Sistemas operacionais** | Para obter as informações mais recentes, revise o suporte do [sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para recuperação do site. O Azure Migrate oferece suporte idêntico ao sistema operacional.
**Sistema de arquivos Linux/armazenamento de hóspedes** | Para obter as informações mais recentes, revise o suporte ao [sistema de arquivos Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para recuperação de site. O Azure Migrate oferece suporte idêntico ao sistema de arquivos Linux.
**Rede/Armazenamento** | Para obter as informações mais recentes, revise os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para recuperação do site. O Azure Migrate fornece requisitos idênticos de rede/armazenamento.
**Requisitos do Azure** | Para obter as informações mais recentes, revise os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [cálculo](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) da [rede Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para recuperação de site. O Azure Migrate tem requisitos idênticos para migração física do servidor.
**Serviço de mobilidade** | O agente de serviço de mobilidade deve ser instalado em cada máquina que você deseja migrar.
**Inicialização UEFI** | A máquina migrada no Azure será automaticamente convertida em uma VM Azure de inicialização do BIOS.<br/><br/> O disco do SISTEMA OPERACIONAL deve ter até quatro partições, e os volumes devem ser formatados com NTFS.
**Disco de destino** | As máquinas só podem ser migradas para discos gerenciados (HDD padrão, SSD premium) no Azure.
**Tamanho do disco** | Disco do SISTEMA OPERACIONAL de 2 TB; 8 TB para discos de dados.
**Limites de disco** |  Até 63 discos por máquina.
**Discos/volumes criptografados** |  Máquinas com discos/volumes criptografados não são suportadas para migração.
**Cluster de disco compartilhado** | Sem suporte.
**Discos independentes** |  Com suporte.
**Discos de passagem** |  Com suporte.
**NFS** | Os volumes NFS montados como volumes nas máquinas não serão replicados.
**metas iSCSI** | Máquinas com alvos iSCSI não são suportadas para migração sem agente.
**IO multicaminho** | Sem suporte.
**VMotion de armazenamento** | Com suporte
**NICs em equipe** | Sem suporte.
**IPv6** | Sem suporte.



## <a name="replication-appliance-requirements"></a>Requisitos de dispositivo para replicação

Se você configurar o aparelho de replicação manualmente em um servidor físico, certifique-se de que ele esteja em conformidade com os requisitos resumidos na tabela. Quando você configura o dispositivo de replicação Azure Migrate como um VMware VMm usando o modelo OVA fornecido no hub Azure Migrate, o aparelho é configurado com o Windows Server 2016 e cumpre com os requisitos de suporte. 

- Saiba mais sobre [os requisitos do aparelho de replicação.](migrate-replication-appliance.md#appliance-requirements)
- O MySQL deve ser instalado no aparelho. Conheça as [opções de instalação](migrate-replication-appliance.md#mysql-installation).
- Saiba mais sobre [URLs](migrate-replication-appliance.md#url-access) que o aparelho de replicação precisa acessar.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs no local replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando a Recuperação do Site executa uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verifica sistemas operacionais suportados.<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional suportado. | A verificação falha se não tiver suporte.
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

[Migrar](tutorial-migrate-physical-virtual-machines.md) servidores físicos.
