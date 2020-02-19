---
title: Matriz de suporte para recuperação de desastre de VM do Azure com Azure Site Recovery
description: Resume o suporte para a recuperação de desastre de VMs do Azure em uma região secundária com Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: d278f96acf8d8efc57a9ae7fb57f9a758339162a
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444070"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matriz de suporte para a recuperação de desastre de VM do Azure entre regiões do Azure

Este artigo resume o suporte e os pré-requisitos para a recuperação de desastre de VMs do Azure de uma região do Azure para outra, usando o serviço de [Azure site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Implantação** |  **Suporte**
--- | ---
**Azure portal** | Com suporte.
**PowerShell** | Com suporte. [Saiba mais](azure-to-azure-powershell.md)
**REST API** | Com suporte.
**CLI** | Sem suporte no momento


## <a name="resource-support"></a>Suporte de recurso

**Ação de recurso** | **Detalhes**
--- | --- 
**Mover cofres entre grupos de recursos** | Sem suporte
**Mover recursos de computação/armazenamento/rede entre os grupos de recursos** | Sem suporte.<br/><br/> Se você mover uma VM ou componentes associados, como armazenamento / rede, depois que a VM estiver replicando, será necessário desabilitar e reativar a replicação para a VM.
**Replicar VMs do Azure de uma assinatura para outra para recuperação de desastres** | Com suporte no mesmo locatário do Azure Active Directory.
**Migre as VMs pelas regiões dos clusters geográficos suportados (dentro e entre assinaturas)** | Com suporte no mesmo locatário do Azure Active Directory.
**Migrar máquinas virtuais na mesma região** | Sem suporte.

## <a name="region-support"></a>Suporte de regiões

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico. Clusters geográficos são definidos mantendo a latência de dados e a soberania em mente.


**Cluster geográfico** | **Regiões do Azure**
-- | --
Sul | Leste do Canadá, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA
Europa | Oeste do Reino Unido, Sul do Reino Unido, Europa Setentrional, Europa Ocidental, França central, sul da França, oeste da África do Sul, norte da África do Sul, leste da Noruega, oeste da Noruega
Ásia | Sul da Índia, Índia central, Índia ocidental, Sudeste Asiático, Ásia Oriental, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, EAU Central, Norte dos EAU
Austrália   | Leste da Austrália, Sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | US Gov Virginia, US Gov Iowa, US Gov – Arizona, US Gov – Texas, Leste do US DoD, Região Central do US DoD 
Alemanha | Centro da Alemanha, Nordeste da Alemanha
China | Leste da China, Norte da China, Norte da China2, Leste da China2
Regiões restritas reservadas para recuperação de desastres no país |Norte da Alemanha reservado para Centro-oeste da Alemanha, Oeste da Suíça reservado para Norte da Suíça, o sul da França reservado para clientes da França central 

>[!NOTE]
>
> - Para o **sul do Brasil**, você pode replicar e fazer failover para essas regiões: Sul EUA Central, Oeste EUA Central, leste dos EUA, leste dos EUA 2, oeste dos EUA, oeste dos EUA 2 e norte EUA Central.
> - O sul do Brasil só pode ser usado como uma região de origem da qual as VMs podem replicar usando Site Recovery. Ele não pode atuar como uma região de destino. Isso ocorre devido a problemas de latência devido a distâncias geográficas. Observe que se você fizer failover do Sul do Brasil como uma região de origem para um destino, o failback para o sul do Brasil da região de destino terá suporte.
> - Você pode trabalhar em regiões para as quais tem acesso apropriado.
> - Se a região na qual você deseja criar um cofre não for mostrada, verifique se sua assinatura tem acesso para criar recursos nessa região.
> - Se você não conseguir ver uma região em um cluster geográfico ao habilitar a replicação, verifique se sua assinatura tem permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento em cache usada pelo Site Recovery durante a replicação.

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento do uso geral V2 (quente e a camada fria) | Suportado | O uso de GPv2 não é recomendado porque os custos de transação para v2 são consideravelmente maiores que as contas de armazenamento v1.
Armazenamento Premium | Sem suporte | As contas de armazenamento standard são usadas para o armazenamento em cache, para ajudar a otimizar os custos.
Firewalls de Armazenamento do Azure para redes virtuais  | Suportado | Caso esteja usando a conta de armazenamento de destino ou a conta de armazenamento de cache habilitada para firewall, escolha ['Permitir serviços confiáveis da Microsoft'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Além disso, certifique-se de permitir o acesso a pelo menos uma sub-rede da VNet de origem.


## <a name="replicated-machine-operating-systems"></a>Sistemas Operacionais Replicados de Máquinas

O Site Recovery oferece suporte à replicação de VMs do Azure que executam os sistemas operacionais listados nesta seção.

### <a name="windows"></a>Windows


**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2019 | Com suporte para Server Core, servidor com experiência desktop.
Windows Server 2016  | Server Core com suporte, servidor com experiência desktop.
Windows Server 2012 R2 | Com suporte.
Windows Server 2012 | Com suporte.
Windows Server 2008 R2 com SP1/SP2 | Com suporte.<br/><br/> Da versão [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do serviço de mobilidade para VMs do Azure, você precisa instalar uma atualização de [Ssu (atualização da pilha de manutenção](https://support.microsoft.com/help/4490628) do Windows) e [SHA-2](https://support.microsoft.com/help/4474419) em computadores que executam o Windows Server 2008 R2 SP1/SP2.  O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre [os requisitos e a atualização do SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Com suporte.
Windows 8.1 (x64) | Com suporte.
Windows 8 (x64) | Com suporte.
Windows 7 (x64) com SP1 em diante | Da versão [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do serviço de mobilidade para VMs do Azure, você precisa instalar uma atualização de [Ssu (atualização da pilha de manutenção](https://support.microsoft.com/help/4490628) do Windows) e [SHA-2](https://support.microsoft.com/help/4474419) em computadores que executam o Windows 7 com SP1.  O SHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado.. Saiba mais sobre [os requisitos e a atualização do SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Sistema operacional** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0, 8,1
Ubuntu 14.04 LTS Server | [Versões de kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Servidor do Ubuntu 16.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Os servidores Ubuntu que usam a autenticação baseada em senha e a entrada e o pacote Cloud-init para configurar VMs de nuvem podem ter um logon baseado em senha desabilitado no failover (dependendo da configuração do cloudinit). O logon baseado em senha pode ser habilitado novamente na máquina virtual redefinindo a senha no menu suporte > solução de problemas > configurações (da VM com failover no portal do Azure.
Servidor Ubuntu 18, 4 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versões de kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versões de kernel com suporte)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versões de kernel com suporte)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Não há suporte para atualização de replicação de máquinas de SP3 para SP4. Se uma máquina replicada tiver sido atualizada, será necessário desativar a replicação e reativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Executando o kernel do Red Hat compatível ou o inquebrable Enterprise kernel versão 3, 4 & 5 (UEK3, UEK4, UEK5) 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9,32| 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14.04 LTS | 9.31 | 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14.04 LTS | 9,30 | 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
14.04 LTS | 9,29 | 3.13.0-24-Generic para 3.13.0-170-Generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-Generic para 4.4.0-148-Generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-Azure |
|||
16.04 LTS | 9,32 | 4.4.0-21-Generic para 4.4.0-171-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-74-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1066-Azure|
16.04 LTS | 9.31 | 4.4.0-21-Generic para 4.4.0-170-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-72-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1063-Azure|
16.04 LTS | [9,30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-Generic para 4.4.0-166-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-66-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1061-Azure|
16.04 LTS | 9,29 | 4.4.0-21-Generic para 4.4.0-164-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-64-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1059-Azure|
|||
18, 4 LTS | 9,32| 4.15.0-20-Generic para 4.15.0-74-Generic </br> 4.18.0-13-Generic para 4.18.0-25-Generic </br> 5.0.0-15-Generic para 5.0.0-37-Generic </br> 5.3.0-19-generic para 5.3.0-24-Generic </br> 4.15.0-1009-Azure para 4.15.0-1037-Azure </br> 4.18.0-1006-Azure para 4.18.0-1025-Azure </br> 5.0.0-1012-Azure para 5.0.0-1028-Azure </br> 5.3.0-1007-Azure para 5.3.0-1009-Azure|
18, 4 LTS | 9.31| 4.15.0-20-Generic para 4.15.0-72-Generic </br> 4.18.0-13-Generic para 4.18.0-25-Generic </br> 5.0.0-15-Generic para 5.0.0-37-Generic </br> 5.3.0-19-generic para 5.3.0-24-Generic </br> 4.15.0-1009-Azure para 4.15.0-1037-Azure </br> 4.18.0-1006-Azure para 4.18.0-1025-Azure </br> 5.0.0-1012-Azure para 5.0.0-1025-Azure </br> 5.3.0-1007-Azure|
18, 4 LTS | [9,30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-Generic para 4.15.0-66-Generic </br> 4.18.0-13-Generic para 4.18.0-25-Generic </br> 5.0.0-15-Generic para 5.0.0-32-Generic </br> 4.15.0-1009-Azure para 4.15.0-1037-Azure </br> 4.18.0-1006-Azure para 4.18.0-1025-Azure </br> 5.0.0-1012-Azure para 5.0.0-1023-Azure|
18, 4 LTS | [9,29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-Generic para 4.15.0-64-Generic </br> 4.18.0-13-Generic para 4.18.0-25-Generic </br> 5.0.0-15-Generic para 5.0.0-29-Generic </br> 4.15.0-1009-Azure para 4.15.0-1037-Azure </br> 4.18.0-1006-Azure para 4.18.0-1025-Azure </br> 5.0.0-1012-Azure para 5.0.0-1020-Azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Debian para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-AMD64 para 3.16.0-10-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 11-AMD64 |
Debian 8 | 9,28 | 3.16.0-4-AMD64 para 3.16.0-10-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 9-AMD64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel com suporte do SUSE Linux Enterprise Server 12 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,32 | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) têm suporte.</br></br> 4.4.138-4.7-Azure para 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure para 4.12.14-6.34-Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.31 | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) têm suporte.</br></br> 4.4.138-4.7-Azure para 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure para 4.12.14-6.29-Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,30 | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) têm suporte.</br></br> 4.4.138-4.7-Azure para 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure para 4.12.14-6.29-Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,29 | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) têm suporte.</br></br> 4.4.138-4.7-Azure para 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure para 4.12.14-6.23-Azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server de 15 versões de kernel com suporte para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9,32 | Há suporte para todos os [kernels SuSE 15 e 15 de estoque](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) .</br></br> 4.12.14-5.5-Azure para 4.12.14-8.22-Azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - sistema de arquivos Linux / armazenamento convidado

* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS, BTRFS
* Gerenciador de volumes: LVM2
* Software Multipath: Mapeador de dispositivos


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - configurações de computação

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Verifique se [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Suportado | Se você habilitar a replicação para uma VM do Azure com as opções padrão, um conjunto de disponibilidade será criado automaticamente, com base nas configurações de região de origem. Você pode modificar essas configurações.
Zonas de disponibilidade | Suportado |
Benefício de uso híbrido (HUB) | Suportado | Se a VM de origem tiver uma licença do HUB ativada, um failover de teste ou falha na VM também usará a licença do HUB.
conjuntos de escala de máquina virtual | Sem suporte |
Imagens da galeria do Azure - Microsoft publicada | Suportado | Suportado se a VM for executada em um sistema operacional suportado.
Imagens da Galeria do Azure – publicadas por terceiros | Suportado | Suportado se a VM for executada em um sistema operacional suportado.
Imagens personalizadas – publicadas por terceiros | Suportado | Suportado se a VM for executada em um sistema operacional suportado.
VMs migradas com o Site Recovery | Suportado | Se uma VM VMware ou uma máquina física foi migrada para o Azure usando o Site Recovery, você precisará desinstalar a versão mais antiga do serviço Mobility em execução na máquina e reiniciá-la antes de replicá-la para outra região do Azure.
Políticas de RBAC | Sem suporte | As políticas de RBAC (controle de acesso baseado em função) em VMs não são replicadas para a VM de failover na região de destino.
Extensões | Sem suporte | As extensões não são replicadas para a VM de failover na região de destino. Ele precisa ser instalado manualmente após o failover.

## <a name="replicated-machines---disk-actions"></a>As máquinas - ações de disco replicadas

**Ação** | **Detalhes**
-- | ---
Redimensionar o disco na VM replicada | Com suporte na VM de origem antes do failover. Não é necessário desabilitar/reabilitar a replicação.<br/><br/> Se você alterar a VM de origem após o failover, as alterações não serão capturadas.<br/><br/> Se você alterar o tamanho do disco na VM do Azure após o failover, as alterações não serão capturadas por Site Recovery e o failback será para o tamanho original da VM.
Adicionar um disco a uma VM replicada | Suportado

## <a name="replicated-machines---storage"></a>Máquinas replicadas - armazenamento

Esta tabela resumiu o suporte ao disco do SO do Azure VM, ao disco de dados e ao disco temporário.

- É importante observar os limites de disco e os destinos da VM para [Linux](../virtual-machines/linux/disk-scalability-targets.md) e [VMs do Windows](../virtual-machines/windows/disk-scalability-targets.md) para evitar problemas de desempenho.
- Se você implantar com as configurações padrão, o Site Recovery criará automaticamente discos e contas de armazenamento com base nas configurações de origem.
- Se você personalizar, certifique-se de seguir as diretrizes.

**Componente** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho máximo do disco do sistema operacional | 2048 GB | [Saiba mais](../virtual-machines/windows/managed-disks-overview.md) sobre discos de VM.
Disco temporário | Sem suporte | O disco temporário é sempre excluído da replicação.<br/><br/> Não armazene nenhum dado persistente no disco temporário. [Saiba mais](../virtual-machines/windows/managed-disks-overview.md).
Tamanho máximo do disco de dados | 8192 GB para discos gerenciados<br></br>4095 GB para discos não gerenciados|
Tamanho mínimo do disco de dados | Nenhuma restrição para discos não gerenciados. 2 GB para discos gerenciados | 
Número máximo de discos de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/windows/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento padrão. | Se a taxa média de alteração de dados no disco for continuamente maior que a máxima, a replicação não será recuperada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a replicação poderá recuperar, mas você poderá ver pontos de recuperação um pouco atrasados.
Disco de dados - conta de armazenamento padrão | Suportado |
Disco de dados - conta de armazenamento premium | Suportado | Se uma VM tiver discos distribuídos em contas de armazenamento premium e padrão, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que você tenha a mesma configuração de armazenamento na região de destino.
Disco gerenciado - standard | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
Disco gerenciado - premium | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
SSD Standard | Suportado |
Redundância | Há suporte para LRS e GRS.<br/><br/> Não há suporte para ZRS.
Armazenamento frio e quente | Sem suporte | Discos de VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Suportado |
Criptografia em repouso (SSE) | Suportado | SSE é a configuração padrão em contas de armazenamento.   
Criptografia em repouso (CMK) | Suportado | As chaves de software e HSM têm suporte para discos gerenciados    
Habilitar o ADE (Azure Disk Encryption) para o sistema operacional Windows | Com suporte para VMs com discos gerenciados. Não há suporte para VMs que usam discos não gerenciados |
ADE (Azure Disk Encryption) para sistema operacional Linux | Suportado |
Adição a quente | Suportado | A habilitação da replicação para um disco de dados que você adiciona a uma VM do Azure replicada tem suporte para VMs que usam discos gerenciados.
Disco de remoção quente | Sem suporte | Se você remover o disco de dados na VM, será necessário desabilitar a replicação e habilitar a replicação novamente para a VM.
Exclusão de disco | Suporte. Você deve usar o [PowerShell](azure-to-azure-exclude-disks.md) para configurar o. |  Os discos temporários são excluídos por padrão.
Espaços de armazenamento Diretos  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
Servidor de Arquivos de Expansão  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
LRS | Suportado |
GRS | Suportado |
RA-GRS | Suportado |
ZRS | Sem suporte |
Armazenamento Frio e Quente | Sem suporte | Não há suporte para discos de máquina virtual no armazenamento frio e quente
Firewalls de Armazenamento do Azure para redes virtuais  | Suportado | Se o acesso à rede virtual for restrito às contas de armazenamento, habilite [permitir serviços confiáveis da Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Suportado | Os custos das transações aumentam substancialmente em comparação com as contas de armazenamento V1 de uso geral
Geração 2 (inicialização UEFI) | Suportado

>[!IMPORTANT]
> Para evitar problemas de desempenho, certifique-se de seguir as metas de desempenho e escalabilidade de disco de VM para VMs [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md) . Se você usar as configurações padrão, Site Recovery criará os discos necessários e as contas de armazenamento, com base na configuração de origem. Se você personalizar e selecionar suas próprias configurações, siga os destinos de desempenho e escalabilidade de disco para suas VMs de origem.

## <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela a seguir resume os limites de Site Recovery.

- Esses limites se baseiam em nossos testes, mas, obviamente, não cobrem todas as combinações de e/s de aplicativo possíveis.
- Os resultados reais podem variar com base na combinação de e/s do aplicativo.
- Há dois limites a serem considerados, por variação de dados de disco e por variação de dados de máquina virtual.

**Destino de armazenamento** | **Média de e/s de disco de origem** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1684 GB por disco

## <a name="replicated-machines---networking"></a>Máquinas replicadas - rede
**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada durante o failover.<br/><br/> O número de NICs na VM de failover depende do número de NICs na VM de origem quando a replicação foi ativada. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará o número de NICs na VM replicada após o failover. Observe também que a ordem de NICs após o failover não tem garantia de ser igual à ordem original.
Balanceador de Carga de Internet | Suportado | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Suportado | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Endereço IP público | Suportado | Associe um endereço IP público existente à NIC. Ou crie um endereço IP público e associe-o à NIC usando um script de automação do Azure em um plano de recuperação.
NSG em NIC | Suportado | Associe o NSG à NIC usando um script de automação do Azure em um plano de recuperação.
NSG na sub-rede | Suportado | Associe o NSG à sub-rede usando um script de automação do Azure em um plano de recuperação.
Endereço IP reservado (estático) | Suportado | Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ela será atribuída à VM com falha.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede será reservado para a VM.<br/><br/> Você também pode especificar um endereço IP fixo e uma sub-rede na **itens replicados** > **configurações** > **de computação e rede** > **Interfaces de rede**.
Endereço IP dinâmico | Suportado | Se a NIC de origem tiver o endereçamento IP dinâmico, a NIC na VM com failover também é dinâmica por padrão.<br/><br/> Você pode modificar isso para um endereço IP fixo, se necessário.
Vários endereços IP | Sem suporte | Quando você faz failover de uma VM que tem uma NIC com vários endereços IP, somente o endereço IP primário da NIC na região de origem é mantido. Para atribuir vários endereços IP, você pode adicionar VMs a um [plano de recuperação](recovery-plan-overview.md) e anexar um script para atribuir endereços IP adicionais ao plano ou pode fazer a alteração manualmente ou com um script após o failover. 
Gerenciador de Tráfego     | Suportado | Você pode pré-configurar o Traffic Manager para que o tráfego seja roteado para o terminal na região de origem regularmente e para o terminal na região de destino em caso de failover.
DNS do Azure | Suportado |
DNS Personalizado  | Suportado |
Proxy não autenticado | Suportado | [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticado | Sem suporte | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada com o Azure Site Recovery.    
Conexão VPN site a site para local<br/><br/>(com ou sem o ExpressRoute)| Suportado | Verifique se o UDRs e o NSGs estão configurados de forma que o tráfego de Site Recovery não seja roteado para o local. [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)    
Conexão VNET a VNET | Suportado | [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)  
Pontos de extremidade de serviço de rede virtual | Suportado | Se você estiver restringindo o acesso à rede virtual para contas de armazenamento, certifique-se de que os serviços Microsoft confiáveis tenham a permissão para acessar a conta de armazenamento.
Redes aceleradas | Suportado | A rede acelerada deve estar ativada na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Próximas etapas
- Leia as [diretrizes de rede](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando as VMs do Azure](site-recovery-azure-to-azure.md).
