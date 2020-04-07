---
title: Matriz de suporte para recuperação de desastres do Azure VM com recuperação do site do Azure
description: Resume o suporte à recuperação de desastres do Azure VMs para uma região secundária com o Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: f61f32ddc0a1cc6575907bc72522228b77552947
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673798"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matriz de suporte para recuperação de desastres da Azure VM entre as regiões do Azure

Este artigo resume o suporte e os pré-requisitos para a recuperação de desastres de VMs azure de uma região azure para outra, usando o serviço [Azure Site Recovery.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Suporte ao método de implantação

**Implantação** |  **Suporte**
--- | ---
**Portal do Azure** |  Com suporte.
**PowerShell** |  Com suporte. [Saiba mais](azure-to-azure-powershell.md)
**API REST** |  Com suporte.
**CLI** | Sem suporte no momento


## <a name="resource-support"></a>Suporte de recurso

**Ação de recurso** | **Detalhes**
--- | ---
**Mova cofres entre grupos de recursos** | Sem suporte
**Mover recursos de computação/armazenamento/rede entre os grupos de recursos** | Sem suporte.<br/><br/> Se você mover uma VM ou componentes associados, como armazenamento / rede, depois que a VM estiver replicando, será necessário desabilitar e reativar a replicação para a VM.
**Replicar VMs do Azure de uma assinatura para outra para recuperação de desastres** | Com suporte no mesmo locatário do Azure Active Directory.
**Migre as VMs pelas regiões dos clusters geográficos suportados (dentro e entre assinaturas)** | Com suporte no mesmo locatário do Azure Active Directory.
**Migrar máquinas virtuais na mesma região** | Sem suporte.

## <a name="region-support"></a>Suporte de regiões

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico. Clusters geográficos são definidos mantendo a latência de dados e a soberania em mente.


**Cluster geográfico** | **Regiões azure**
-- | --
Sul | Leste do Canadá, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA
Europa | Reino Unido Oeste, Reino Unido Sul, Norte da Europa, Europa Ocidental, África do Sul Oeste, África do Sul Norte, Noruega Leste, Noruega Oeste
Ásia | Índia do Sul, Índia Central, Índia Ocidental, Sudeste Asiático, Leste da Ásia, Japão Leste, Japão Oeste, Coréia Central, Coréia do Sul
Austrália    | Leste da Austrália, Sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | US Gov Virginia, US Gov Iowa, US Gov – Arizona, US Gov – Texas, Leste do US DoD, Região Central do US DoD
Alemanha    | Centro da Alemanha, Nordeste da Alemanha
China | Leste da China, Norte da China, Norte da China2, Leste da China2
Regiões restritas reservadas para recuperação de desastres no país |Alemanha Norte reservado para alemanha Centro-Oeste, Suíça Oeste reservado para suíça norte, França Sul reservado para França Central, Emirados Unidos Central restrito para clientes norte dos Emirados Unidos

>[!NOTE]
>
> - Para **o Brasil Sul,** você pode replicar e falhar nessas regiões: Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, OESTE DOS EUA, US$ 2 e Norte Central dos EUA.
> - O Brasil Sul só pode ser usado como uma região de origem a partir da qual as VMs podem se replicar usando o Site Recovery. Não pode agir como uma região alvo. Isso se deve a problemas de latência devido a distâncias geográficas. Note-se que se você falhar do Brasil Sul como uma região de origem para um alvo, o failback para o Brasil Sul a partir da região alvo é apoiado.
> - Você pode trabalhar dentro de regiões para as quais você tem acesso apropriado.
> - Se a região em que você deseja criar um cofre não aparecer, certifique-se de que sua assinatura tenha acesso para criar recursos nessa região.
> - Se você não puder ver uma região dentro de um cluster geográfico ao habilitar a replicação, certifique-se de que sua assinatura tenha permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento em cache usada pelo Site Recovery durante a replicação.

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento do uso geral V2 (quente e a camada fria) | Com suporte | O uso do GPv2 não é recomendado porque os custos de transação para V2 são substancialmente mais altos do que as contas de armazenamento V1.
Armazenamento Premium | Sem suporte | As contas de armazenamento padrão são usadas para armazenamento em cache, para ajudar a otimizar custos.
Firewalls de Armazenamento do Azure para redes virtuais  | Com suporte | Caso esteja usando a conta de armazenamento de destino ou a conta de armazenamento de cache habilitada para firewall, escolha ['Permitir serviços confiáveis da Microsoft'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Além disso, certifique-se de permitir o acesso a pelo menos uma sub-rede da VNet de origem.


## <a name="replicated-machine-operating-systems"></a>Sistemas Operacionais Replicados de Máquinas

O Site Recovery oferece suporte à replicação de VMs do Azure que executam os sistemas operacionais listados nesta seção. Observe que se uma máquina já replicante for posteriormente atualizada (ou rebaixada) para um kernel principal diferente, você precisará desativar a replicação e reativar a replicação após a atualização.

### <a name="windows"></a>Windows


**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2019 | Suportado para server core, servidor com experiência de desktop.
Windows Server 2016  | Núcleo de servidor suportado, servidor com experiência de desktop.
Windows Server 2012 R2 |  Com suporte.
Windows Server 2012 |  Com suporte.
Windows Server 2008 R2 com SP1/SP2 |  Com suporte.<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão de serviço mobility para VMs Azure, você precisa instalar uma atualização de pilha de manutenção do Windows [(SSU)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) em máquinas que executam o Windows Server 2008 R2 SP1/SP2.  O SHA-1 não é suportado a partir de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e os requisitos do SHA-2.](https://aka.ms/SHA-2KB)
Windows 10 (x64) |  Com suporte.
Windows 8.1 (x64) |  Com suporte.
Windows 8 (x64) |  Com suporte.
Windows 7 (x64) com SP1 em diante | A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão de serviço mobility para VMs Do Azure, você precisa instalar uma atualização de pilha de manutenção do Windows [(SSU)](https://support.microsoft.com/help/4490628) e [uma atualização SHA-2](https://support.microsoft.com/help/4474419) em máquinas que executam o Windows 7 com o SP1.  O SHA-1 não é suportado a partir de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não instalará/atualizará como esperado.. Saiba mais sobre [a atualização e os requisitos do SHA-2.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Sistema operacional** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0,](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [Versões de kernel suportadas](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Servidor do Ubuntu 16.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Os servidores Ubuntu que usam autenticação e login baseados em senha e o pacote de entrada na nuvem para configurar VMs na nuvem podem ter o login baseado em senha desativado no failover (dependendo da configuração do cloudinit). O login baseado em senha pode ser reativado na máquina virtual, redefinindo a senha do menu De suporte > solução de problemas > Configurações (da falha sobre vm no portal Azure.
Servidor Ubuntu 18.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Versões de kernel suportadas](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versões de kernel suportadas](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versões suportadas do kernel)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versões suportadas do kernel)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Não há suporte para atualização de replicação de máquinas de SP3 para SP4. Se uma máquina replicada tiver sido atualizada, será necessário desativar a replicação e reativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Executando o kernel compatível com red hat ou unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-genérico para 4.4.0-171-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-74-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-genérico para 4.4.0-170-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-72-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-genérico para 4.4.0-166-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-66-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-genérico para 4.4.0-164-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-64-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-genérico para 4.15.0-74-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico para 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1028-azure </br> 5.3.0-1007-azure para 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-genérico para 4.15.0-72-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico para 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-genérico para 4.15.0-66-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-32-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-genérico para 4.15.0-64-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico para 5.0.0-29-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Debian para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel com suporte do SUSE Linux Enterprise Server 12 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.32 | Todos os [kernels SUSE 12 SP1, SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.31 | Todos os [kernels SUSE 12 SP1, SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.30 | Todos os [kernels SUSE 12 SP1, SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.29 | Todos os [kernels SUSE 12 SP1, SP2,SP3,SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) são suportados.</br></br> 4.4.138-4.7-azure para 4.4.180-4.31-azure,</br>4.12.14-6.3-azure para 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel SUSE Linux Enterprise Server 15 suportadas para máquinas virtuais Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Todos os [sUSE 15 e 15 núcleos](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) de estoque são suportados.</br></br> 4.12.14-5.5-azure para 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - sistema de arquivos Linux / armazenamento convidado

* Sistemas de arquivos: ext3, ext4, XFS, BTRFS
* Gerenciador de volumes: LVM2
* Software Multipath: Mapeador de dispositivos


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - configurações de computação

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Verifique se [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Com suporte | Se você habilitar a replicação de uma VM Do Azure com as opções padrão, um conjunto de disponibilidade será criado automaticamente, com base nas configurações da região de origem. Você pode modificar essas configurações.
Zonas de disponibilidade | Com suporte |
Benefício de uso híbrido (HUB) | Com suporte | Se a VM de origem tiver uma licença do HUB ativada, um failover de teste ou falha na VM também usará a licença do HUB.
conjuntos de escala de máquina virtual | Sem suporte |
Imagens da galeria do Azure - Microsoft publicada | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens da Galeria do Azure – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens personalizadas – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
VMs migradas com o Site Recovery | Com suporte | Se uma VM VMware ou uma máquina física foi migrada para o Azure usando o Site Recovery, você precisará desinstalar a versão mais antiga do serviço Mobility em execução na máquina e reiniciá-la antes de replicá-la para outra região do Azure.
Políticas de RBAC | Sem suporte | As políticas de controle de acesso baseado em função (RBAC) em VMs não são replicadas para a VM failover na região de destino.
Extensões | Sem suporte | As extensões não são replicadas no VM failover na região alvo. Ele precisa ser instalado manualmente após o failover.

## <a name="replicated-machines---disk-actions"></a>As máquinas - ações de disco replicadas

**Ação** | **Detalhes**
-- | ---
Redimensionar o disco na VM replicada | Suportado na VM de origem antes do failover. Não há necessidade de desativar/reativar a replicação.<br/><br/> Se você alterar a VM de origem após o failover, as alterações não serão capturadas.<br/><br/> Se você alterar o tamanho do disco na VM do Azure após o failover, as alterações não serão capturadas pela Recuperação do Site e o failback será para o tamanho original da VM.
Adicionar um disco a uma VM replicada | Com suporte

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
Tamanho mínimo do disco de dados | Não há restrição para discos não gerenciados. 2 GB para discos gerenciados |
Número máximo de discos de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/windows/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 10 MBps por disco para armazenamento premium. Máximo de 2 MBps por disco para armazenamento padrão. | Se a taxa média de alteração de dados no disco for continuamente maior que a máxima, a replicação não será recuperada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a replicação poderá recuperar, mas você poderá ver pontos de recuperação um pouco atrasados.
Disco de dados - conta de armazenamento padrão | Com suporte |
Disco de dados - conta de armazenamento premium | Com suporte | Se uma VM tiver discos distribuídos em contas de armazenamento premium e padrão, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que você tenha a mesma configuração de armazenamento na região de destino.
Disco gerenciado - standard | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
Disco gerenciado - premium | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
SSD Standard | Com suporte |
Redundância | Há suporte para LRS e GRS.<br/><br/> Não há suporte para ZRS.
Armazenamento frio e quente | Sem suporte | Discos de VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Com suporte |
Criptografia em repouso (SSE) | Com suporte | SSE é a configuração padrão em contas de armazenamento.
Criptografia em repouso (CMK) | Com suporte | As teclas Software e HSM são suportadas para discos gerenciados
Habilitar o ADE (Azure Disk Encryption) para o sistema operacional Windows | Suportado para VMs com discos gerenciados. | As VMs que usam discos não gerenciados não são suportadas. <br/><br/> As chaves protegidas por HSM não são suportadas. |
ADE (Azure Disk Encryption) para sistema operacional Linux | Suportado para VMs com discos gerenciados. | As VMs que usam discos não gerenciados não são suportadas. <br/><br/> As chaves protegidas por HSM não são suportadas. |
Adição quente    | Com suporte | A ativação da replicação de um disco de dados que você adiciona a uma VM Azure replicada é suportada para VMs que usam discos gerenciados.
Disco de remoção quente    | Sem suporte | Se você remover o disco de dados na VM, você precisa desativar a replicação e ativar a replicação novamente para a VM.
Exclusão de disco | Suporte. Você deve usar [o PowerShell](azure-to-azure-exclude-disks.md) para configurar. |    Discos temporários são excluídos por padrão.
Espaços de armazenamento Diretos  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
Servidor de Arquivos de Expansão  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
DRBD | Os discos que fazem parte de uma configuração DRBD não são suportados. |
LRS | Com suporte |
GRS | Com suporte |
RA-GRS | Com suporte |
ZRS | Sem suporte |
Armazenamento Frio e Quente | Sem suporte | Não há suporte para discos de máquina virtual no armazenamento frio e quente
Firewalls de Armazenamento do Azure para redes virtuais  | Com suporte | Se restringir o acesso à rede virtual a contas de armazenamento, [habilite permitir serviços confiáveis da Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Com suporte | Os custos das transações aumentam substancialmente em comparação com as contas de armazenamento V1 de uso geral
Geração 2 (inicialização UEFI) | Com suporte

>[!IMPORTANT]
> Para evitar problemas de desempenho, certifique-se de seguir as metas de escalabilidade e desempenho do disco VM para [VMs Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows.](../virtual-machines/windows/disk-scalability-targets.md) Se você usar configurações padrão, o Site Recovery criará os discos e contas de armazenamento necessários, com base na configuração de origem. Se você personalizar e selecionar suas próprias configurações, siga as metas de escalabilidade e desempenho do disco para suas VMs de origem.

## <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela a seguir resume os limites de recuperação do site.

- Esses limites são baseados em nossos testes, mas obviamente não cobrem todas as combinações possíveis de I/O de aplicação.
- Os resultados reais podem variar de acordo com a mistura de I/O do aplicativo.
- Há dois limites a considerar, por churn de dados de disco e por churn de dados de máquina virtual.

**Destino de armazenamento** | **I/O de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de dados de disco de origem churn por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1684 GB por disco

## <a name="replicated-machines---networking"></a>Máquinas replicadas - rede
**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada durante o failover.<br/><br/> O número de NICs na VM de failover depende do número de NICs na VM de origem quando a replicação foi ativada. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará o número de NICs na VM replicada após o failover. Observe também que a ordem dos NICs após failover não é garantida como sendo a mesma da ordem original.
Balanceador de Carga de Internet | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Endereço IP público | Com suporte | Associe um endereço IP público existente à NIC. Ou crie um endereço IP público e associe-o à NIC usando um script de automação do Azure em um plano de recuperação.
NSG em NIC | Com suporte | Associe o NSG à NIC usando um script de automação do Azure em um plano de recuperação.
NSG na sub-rede | Com suporte | Associe o NSG à sub-rede usando um script de automação do Azure em um plano de recuperação.
Endereço IP reservado (estático) | Com suporte | Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ela será atribuída à VM com falha.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede será reservado para a VM.<br/><br/> Você também pode especificar um endereço IP fixo e uma sub-rede em **interfaces** > de configuração de itens replicados**e** > **interfaces** > **de rede**de rede .
Endereço IP dinâmico | Com suporte | Se a NIC de origem tiver o endereçamento IP dinâmico, a NIC na VM com failover também é dinâmica por padrão.<br/><br/> Você pode modificar isso para um endereço IP fixo, se necessário.
Vários endereços IP | Sem suporte | Quando você falha sobre uma VM que tem uma NIC com vários endereços IP, apenas o endereço IP principal da NIC na região de origem é mantido. Para atribuir vários endereços IP, você pode adicionar VMs a um [plano de recuperação](recovery-plan-overview.md) e anexar um script para atribuir endereços IP adicionais ao plano, ou você pode fazer a alteração manualmente ou com um script após failover.
Gerenciador de Tráfego     | Com suporte | Você pode pré-configurar o Traffic Manager para que o tráfego seja roteado para o terminal na região de origem regularmente e para o terminal na região de destino em caso de failover.
DNS do Azure | Com suporte |
DNS Personalizado    | Com suporte |
Proxy não autenticado | Com suporte | [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)
Proxy autenticado | Sem suporte | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada com o Azure Site Recovery.
Conexão VPN entre sites e locais<br/><br/>(com ou sem ExpressRoute)| Com suporte | Certifique-se de que os UDRs e NSGs estejam configurados de forma que o tráfego de recuperação do site não seja roteado para os locais. [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)
Conexão VNET a VNET    | Com suporte | [Saiba mais](site-recovery-azure-to-azure-networking-guidance.md)
Pontos de extremidade de serviço de rede virtual | Com suporte | Se você estiver restringindo o acesso à rede virtual para contas de armazenamento, certifique-se de que os serviços Microsoft confiáveis tenham a permissão para acessar a conta de armazenamento.
Redes aceleradas | Com suporte | A rede acelerada deve estar ativada na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Próximas etapas
- Leia [as orientações de rede](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando as VMs do Azure](site-recovery-azure-to-azure.md).
