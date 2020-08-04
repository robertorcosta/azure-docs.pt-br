---
title: Matriz de suporte para a recuperação de desastre da VM do Azure com o Azure Site Recovery
description: Resume o suporte para a recuperação de desastre de VMs do Azure em uma região secundária com o Azure Site Recovery.
ms.topic: article
ms.date: 07/14/2020
ms.author: raynew
ms.openlocfilehash: d4beec60a2cd705884ee79296c9afab2afc08c2e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534491"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Matriz de suporte para recuperação de desastre de VM do Azure entre regiões do Azure

Este artigo resume o suporte e os pré-requisitos para a recuperação de desastre de VMs do Azure de uma região do Azure para outra, usando o serviço [Azure Site Recovery](site-recovery-overview.md).


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
Europa | Oeste do Reino Unido, Sul do Reino Unido, Europa Setentrional, Europa Ocidental, oeste da África do Sul, norte da África do Sul, leste da Noruega, oeste da Noruega, França central
Ásia | Sul da Índia, Índia Central, Oeste da Índia, Sudeste Asiático, Leste da Ásia, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia
Austrália    | Leste da Austrália, Sudeste da Austrália, Austrália Central, Austrália Central 2
Azure Government    | US Gov Virginia, US Gov Iowa, US Gov – Arizona, US Gov – Texas, Leste do US DoD, Região Central do US DoD
Alemanha    | Centro da Alemanha, Nordeste da Alemanha
China | Leste da China, Norte da China, Norte da China2, Leste da China2
Regiões restritas reservadas para recuperação de desastre dentro do país |Norte da Alemanha reservado para Centro-Oeste da Alemanha, Oeste da Suíça reservado para Norte da Suíça, Sul da França reservado para França Central, EAU Central restrito para clientes do Norte dos EAU

>[!NOTE]
>
> - Para a região **Sul do Brasil**, você pode replicar e fazer failover para estas regiões: Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2 e Centro-Norte dos EUA.
> - A Sul do Brasil só pode ser usado como uma região de origem da qual as VMs podem replicar usando o Site Recovery. Ela não pode atuar como uma região de destino. Isso ocorre graças a problemas de latência devido a distâncias geográficas. Observe que se você fizer failover do Sul do Brasil como uma região de origem para um destino, o failback para o Sul do Brasil da região de destino será um procedimento compatível.
> - Você pode trabalhar em regiões para as quais tem acesso apropriado.
> - Se a região na qual deseja criar um cofre não for exibida, verifique se sua assinatura tem acesso para criar recursos nessa região.
> - Se você não conseguir ver uma região em um cluster geográfico ao habilitar a replicação, verifique se a sua assinatura tem permissões para criar VMs nessa região.



## <a name="cache-storage"></a>Armazenamento em cache

Esta tabela resume o suporte para a conta de armazenamento em cache usada pelo Site Recovery durante a replicação.

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Contas de armazenamento do uso geral V2 (quente e a camada fria) | Com suporte | O uso de GPv2 não é recomendado porque os custos de transação para V2 são substancialmente maiores do que para que as contas de armazenamento V1.
Armazenamento Premium | Sem suporte | As contas de Armazenamento Standard são usadas para o armazenamento em cache, para ajudar a otimizar os custos.
Firewalls de Armazenamento do Azure para redes virtuais  | Com suporte | Caso esteja usando a conta de armazenamento de destino ou a conta de armazenamento de cache habilitada para firewall, escolha ['Permitir serviços confiáveis da Microsoft'](../storage/common/storage-network-security.md#exceptions).<br></br>Além disso, certifique-se de permitir o acesso a pelo menos uma sub-rede da VNet de origem.


## <a name="replicated-machine-operating-systems"></a>Sistemas Operacionais Replicados de Máquinas

O Site Recovery oferece suporte à replicação de VMs do Azure que executam os sistemas operacionais listados nesta seção. Observe que, se um computador que já está replicando for atualizado posteriormente (ou sofrer downgrade) para um kernel principal diferente, você precisará desabilitar a replicação e reabilitar a replicação após a atualização ou downgrade.

### <a name="windows"></a>Windows


**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2019 | Compatível com Server Core, Servidor com Experiência Desktop.
Windows Server 2016  | Compatível com Server Core, Servidor com Experiência Desktop.
Windows Server 2012 R2 | Com suporte.
Windows Server 2012 | Com suporte.
Windows Server 2008 R2 com SP1/SP2 | Com suporte.<br/><br/> Na versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do Serviço de Mobilidade para VMs do Azure e nas posteriores, você precisa instalar uma [SSU (atualização de pilha de manutenção)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) em computadores que estejam executando o Windows Server 2008 R2 SP1/SP2.  O SHA-1 não tem mais suporte desde setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Com suporte.
Windows 8.1 (x64) | Com suporte.
Windows 8 (x64) | Com suporte.
Windows 7 (x64) com SP1 em diante | Na versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) da extensão do Serviço de Mobilidade para VMs do Azure e nas posteriores, você precisa instalar uma [SSU (atualização de pilha de manutenção)](https://support.microsoft.com/help/4490628) e [atualização SHA-2](https://support.microsoft.com/help/4474419) em computadores que estejam executando o Windows 7 com SP1.  O SHA-1 não tem mais suporte desde setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Sistema operacional** | **Detalhes**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609/)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, [7,8](https://support.microsoft.com/help/4564347/), 8,0, 8,1, [8,2](https://support.microsoft.com/en-us/help/4570609)
Ubuntu 14.04 LTS Server | [Versões de kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Servidor do Ubuntu 16.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Servidores Ubuntu usando autenticação e entrada baseados em senha, bem como o pacote cloud-init para configurar VMs em nuvem, podem ter a entrada baseada em senha desabilitada no failover (dependendo da configuração do cloudinit). A entrada baseada em senha pode ser habilitada novamente na máquina virtual com a redefinição da senha no menu Suporte > Solução de problemas > Configurações (da VM com falha no portal do Azure).
Servidor Ubuntu 18.04 LTS | [Versão do kernel com suporte](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | Inclui suporte para todos os 7. versões do [kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines) nas versões *x*
Debian 8 | Inclui suporte para todos os 8. versões do [kernel com suporte](#supported-debian-kernel-versions-for-azure-virtual-machines) nas versões *x*
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4, SP5 [(versões de kernel com suporte)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines) </br> (* * O SP5 está disponível por meio do patch mais recente do 9,33 para o cenário de recuperação de desastre do Azure para Azure).
SUSE Linux Enterprise Server 15 | 15 e 15 SP1. [(Versões de kernel com suporte)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Não há suporte para atualização de replicação de máquinas de SP3 para SP4. Se uma máquina replicada tiver sido atualizada, será necessário desativar a replicação e reativar a replicação após a atualização.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [8,0](https://support.microsoft.com/help/4573888/)  <br/> Executando o kernel compatível com Red Hat ou o Unbreakable Enterprise Kernel Release 3, 4 e 5 (UEK3, UEK4 e UEK5)<br/><br/>[8.1](https://support.microsoft.com/help/4573888/)<br/>Em execução em todos os kernels UEK e RedHat kernel <= 3.10.0-1062. * têm suporte. O suporte para o restante dos kernels do RedHat estará disponível em 9,36, esperado pelo final de agosto.

* * Observação: para oferecer suporte a kernels do Linux mais recentes dentro de 30 dias após o lançamento, Azure Site Recovery distribuir o patch do Hot Fix sobre a versão mais recente do agente de mobilidade. Essa correção é distribuída entre duas versões de versão principais. Para atualizar para a versão mais recente do agente de mobilidade (incluindo patch de Hot Fix), siga as etapas mencionadas neste [artigo](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Esse patch é lançado atualmente para agentes de mobilidade usados no cenário de recuperação de desastre do Azure para Azure.

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9,32, [9,33](https://support.microsoft.com/help/4564347/),[9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/)| 3.13.0-24-generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
|||
16.04 LTS | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-Generic para 4.4.0-184-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-106-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1089-Azure</br> 4.15.0-107-Generic, 4.4.0-185-Generic & 4.15.0-1091-Azure por meio do patch de Hot Fix 9,35 * * |
16.04 LTS | [9,33](https://support.microsoft.com/help/4564347/) | 4.4.0-21-Generic para 4.4.0-178-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-99-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1082-Azure </br> 4.15.0-101-genérica & 4.4.0-179-Generic por meio do patch de Hot Fix 9,33 * *|
16.04 LTS | 9.32 | 4.4.0-21-generic a 4.4.0-171-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-74-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-generic a 4.4.0-170-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-72-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1063-azure|
|||
18.04 LTS | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-Generic para 4.15.0-108-Generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-Generic para 5.0.0-52-Generic </br> 5.3.0-19-generic para 5.3.0-61-Generic </br> 4.15.0-1009-Azure para 4.15.0-1089-Azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-Azure para 5.0.0-1036-Azure </br> 5.3.0-1007-Azure para 5.3.0-1031-Azure </br> 4.15.0E-109-Generic, 5.0.0-53-genérico, 5.3.0-62-Generic, 4.15.0-1091-Azure & 5.3.0-1032-Azure por meio do patch de Hot Fix 9,35 * *|
18.04 LTS | [9,33](https://support.microsoft.com/help/4564347/) | 4.15.0-20-Generic para 4.15.0-99-Generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-Generic para 5.0.0-47-Generic </br> 5.3.0-19-generic para 5.3.0-51-Generic </br> 4.15.0-1009-Azure para 4.15.0-1082-Azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-Azure para 5.0.0-1036-Azure </br> 5.3.0-1007-Azure para 5.3.0-1020-Azure </br> 4.15.0-101-Generic, 5.0.0-48-genérica, 5.3.0-1022-Azure & 5.3.0-53-Generic por meio do patch de Hot Fix 9,33 * *|
18.04 LTS | 9.32| 4.15.0-20-generic a 4.15.0-74-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic a 5.0.0-37-generic </br> 5.3.0-19-generic a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-generic a 4.15.0-72-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic a 5.0.0-37-generic </br> 5.3.0-19-generic a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1025-azure </br> 5.3.0-1007-azure|

* * Observação: para oferecer suporte a kernels do Linux mais recentes dentro de 30 dias após o lançamento, Azure Site Recovery distribuir o patch do Hot Fix sobre a versão mais recente do agente de mobilidade. Essa correção é distribuída entre duas versões de versão principais. Para atualizar para a versão mais recente do agente de mobilidade (incluindo patch de Hot Fix), siga as etapas mencionadas neste [artigo](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure). Esse patch é lançado atualmente para agentes de mobilidade usados no cenário de recuperação de desastre do Azure para Azure.

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Debian para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | 9,31, 9,32, [9,33](https://support.microsoft.com/help/4564347/), [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,35](https://support.microsoft.com/help/4573888/) | 3.16.0-4-AMD64 para 3.16.0-11-AMD64, 4.9.0-0. BPO. 4-AMD64 para 4.9.0-0. BPO. 11-AMD64 |
Debian 8 | 9,31, 9,32, [9,33](https://support.microsoft.com/help/4564347/), [9,34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel com suporte do SUSE Linux Enterprise Server 12 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/) | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://www.suse.com/support/kb/doc/?id=000019587) têm suporte.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-Azure para 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure para 4.12.14-16.19-Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3 e SP4) | 9,32, [9,33](https://support.microsoft.com/help/4564347/) [9,35](https://support.microsoft.com/help/4573888/) | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://www.suse.com/support/kb/doc/?id=000019587) têm suporte.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | patch de Hot Fix 9,33 | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://www.suse.com/support/kb/doc/?id=000019587) têm suporte.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure </br> 4.12.14-16,7-Azure para 4.12.14-16.13-Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3 e SP4) | 9.31 | Todos os [estoques SuSE 12 SP1, SP2, SP3 e kernels do SP4](https://www.suse.com/support/kb/doc/?id=000019587) têm suporte.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Versões de kernel compatíveis do SUSE Linux Enterprise Server 15 para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | [9,34](https://support.microsoft.com/help/4570609), [9,35](https://support.microsoft.com/help/4573888/)  | Por padrão, todos os [kernels 15 e SUSE 15 padrão](https://www.suse.com/support/kb/doc/?id=000019587) são compatíveis.</br></br> 4.12.14-5.5-Azure para 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure para 4.12.14-8.33-Azure 
|SUSE Linux Enterprise Server 15 e 15 SP1 | [9,33](https://support.microsoft.com/help/4564347/) | Por padrão, todos os [kernels 15 e SUSE 15 padrão](https://www.suse.com/support/kb/doc/?id=000019587) são compatíveis.</br></br> 4.12.14-5.5-Azure para 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure para 4.12.14-8.30-Azure |
SUSE Linux Enterprise Server 15 e 15 SP1 | 9.32 | Por padrão, todos os [kernels 15 e SUSE 15 padrão](https://www.suse.com/support/kb/doc/?id=000019587) são compatíveis.</br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Máquinas replicadas - sistema de arquivos Linux / armazenamento convidado

* Sistemas de arquivos: ext3, ext4, XFS e BTRFS
* Gerenciador de volume: LVM2
* Software de múltiplos caminhos: Mapeador de Dispositivos


## <a name="replicated-machines---compute-settings"></a>Máquinas replicadas - configurações de computação

**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Verifique se [tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md).
Conjuntos de disponibilidade | Com suporte | Se você habilitar a replicação para uma VM do Azure com as opções padrão, um conjunto de disponibilidade será criado automaticamente com base nas configurações da região de origem. Você pode modificar essas configurações.
Zonas de disponibilidade | Com suporte |
Benefício de uso híbrido (HUB) | Com suporte | Se a VM de origem tiver uma licença do HUB ativada, um failover de teste ou falha na VM também usará a licença do HUB.
conjuntos de escala de máquina virtual | Sem suporte |
Imagens da galeria do Azure - Microsoft publicada | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens da Galeria do Azure – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
Imagens personalizadas – publicadas por terceiros | Com suporte | Suportado se a VM for executada em um sistema operacional suportado.
VMs migradas com o Site Recovery | Com suporte | Se uma VM VMware ou uma máquina física foi migrada para o Azure usando o Site Recovery, você precisará desinstalar a versão mais antiga do serviço Mobility em execução na máquina e reiniciá-la antes de replicá-la para outra região do Azure.
Políticas de RBAC | Sem suporte | As políticas de RBAC (controle de acesso baseado em função) em VMs não são replicadas para a VM de failover na região de destino.
Extensões | Sem suporte | As extensões não são replicadas para a VM de failover na região de destino. Ele precisa ser instalado manualmente após o failover.
Grupos de posicionamento de proximidade | Com suporte | As máquinas virtuais localizadas dentro de um grupo de posicionamento de proximidade podem ser protegidas usando Site Recovery.


## <a name="replicated-machines---disk-actions"></a>As máquinas - ações de disco replicadas

**Ação** | **Detalhes**
-- | ---
Redimensionar o disco na VM replicada | Compatível com a VM de origem antes do failover. Não é necessário desabilitar/reabilitar a replicação.<br/><br/> Se você alterar a VM de origem após o failover, as alterações não serão capturadas.<br/><br/> Se você alterar o tamanho do disco na VM do Azure após o failover, as alterações não serão capturadas por Site Recovery e o failback será para o tamanho original da VM.
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
Tamanho máximo do disco de dados | 8\.192 GB para discos gerenciados<br></br>4\.095 GB para discos não gerenciados|
Tamanho mínimo do disco de dados | Sem restrições para discos não gerenciados. 2 GB para discos gerenciados |
Número máximo de discos de dados | Até 64, de acordo com o suporte para um tamanho específico de VM do Azure | [Saiba mais](../virtual-machines/windows/sizes.md) sobre os tamanhos de VM.
Taxa de alteração do disco de dados | Máximo de 20 MBps por disco para o armazenamento Premium. Máximo de 2 MBps por disco para armazenamento padrão. | Se a taxa média de alteração de dados no disco for continuamente maior que a máxima, a replicação não será recuperada.<br/><br/>  No entanto, se o máximo for excedido esporadicamente, a replicação poderá recuperar, mas você poderá ver pontos de recuperação um pouco atrasados.
Disco de dados - conta de armazenamento padrão | Com suporte |
Disco de dados - conta de armazenamento premium | Com suporte | Se uma VM tiver discos distribuídos em contas de armazenamento premium e padrão, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, para garantir que você tenha a mesma configuração de armazenamento na região de destino.
Disco gerenciado - standard | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
Disco gerenciado - premium | Suporte para regiões do Azure nas quais há suporte para Azure Site Recovery. |
SSD Standard | Com suporte |
Redundância | Há suporte para LRS e GRS.<br/><br/> Não há suporte para ZRS.
Armazenamento frio e quente | Sem suporte | Discos de VM não são suportados em armazenamento fresco e quente
Espaços de Armazenamento | Com suporte |
Criptografia em repouso (SSE) | Com suporte | SSE é a configuração padrão em contas de armazenamento.
Criptografia em repouso (CMK) | Com suporte | As chaves de software e HSM são compatíveis com discos gerenciados
Criptografia dupla em repouso | Com suporte | Saiba mais sobre as regiões com suporte para [Windows](../virtual-machines/windows/disk-encryption.md) e [Linux](../virtual-machines/linux/disk-encryption.md)
Habilitar o ADE (Azure Disk Encryption) para o sistema operacional Windows | Compatível com VMs com discos gerenciados. | VMs usando discos não gerenciados não são compatíveis. <br/><br/> Chaves protegidas por HSM não são compatíveis. <br/><br/> A criptografia de volumes individuais em apenas um disco não é um procedimento compatível. |
ADE (Azure Disk Encryption) para sistema operacional Linux | Compatível com VMs com discos gerenciados. | VMs usando discos não gerenciados não são compatíveis. <br/><br/> Chaves protegidas por HSM não são compatíveis. <br/><br/> A criptografia de volumes individuais em apenas um disco não é um procedimento compatível. <br><br> Problema conhecido com a habilitação da replicação. [Saiba mais.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-troubleshoot-errors#enable-protection-failed-as-the-installer-is-unable-to-find-the--root-disk-error-code-151137) |
Rotação de chave SAS | Sem suporte | Se a chave SAS para contas de armazenamento for girada, o cliente precisará desabilitar e reabilitar a replicação. |
Adição a quente    | Com suporte | A habilitação da replicação para um disco de dados que você adiciona a uma VM do Azure replicada é compatível com VMs que usam discos gerenciados. <br/><br/> É possível adicionar a quente somente um disco por vez a uma VM do Azure. A adição paralela de vários discos não é um procedimento compatível. |
Remoção de disco a quente    | Sem suporte | Se você remover um disco de dados da VM, precisará desabilitar a replicação e habilitá-la novamente na VM.
Exclusão de disco | Suporte. Você precisa usar o [PowerShell](azure-to-azure-exclude-disks.md) para configurar. |    Os discos temporários são excluídos por padrão.
Espaços de armazenamento Diretos  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
Servidor de Arquivos de Expansão  | Com suporte para pontos de recuperação de falha consistentes. Sem suporte para pontos de recuperação de aplicativo consistentes. |
DRBD | Discos que fazem parte de uma instalação do DRBD não são compatíveis. |
LRS | Com suporte |
GRS | Com suporte |
RA-GRS | Com suporte |
ZRS | Sem suporte |
Armazenamento Frio e Quente | Sem suporte | Não há suporte para discos de máquina virtual no armazenamento frio e quente
Firewalls de Armazenamento do Azure para redes virtuais  | Com suporte | Se você estiver restringindo o acesso da rede virtual a contas de armazenamento, habilite [Permitir serviços Microsoft confiáveis](../storage/common/storage-network-security.md#exceptions).
Contas de armazenamento V2 de uso geral (camadas Hot e Cool) | Com suporte | Os custos das transações aumentam substancialmente em comparação com as contas de armazenamento V1 de uso geral
Geração 2 (inicialização de UEFI) | Com suporte
Discos NVMe | Sem suporte
Discos compartilhados do Azure | Sem suporte
Opção de transferência segura | Com suporte

>[!IMPORTANT]
> Para evitar problemas de desempenho, verifique se você está seguindo as metas de desempenho e escalabilidade de disco de VM para VMs do [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou do [Windows](../virtual-machines/windows/disk-scalability-targets.md). Se você usar as configurações padrão, o Site Recovery criará as contas de armazenamento e discos necessários com base na configuração de origem. Se você personalizar e selecionar suas configurações, siga as metas de desempenho e escalabilidade de discos para suas VMs de origem.

## <a name="limits-and-data-change-rates"></a>Limites e taxas de alteração de dados

A tabela a seguir resume os limites do Site Recovery.

- Esses limites baseiam-se nos nossos testes, mas obviamente não abrangem todas as combinações possíveis de E/S de aplicativos.
- Os resultados reais podem variar dependendo da combinação de E/S do aplicativo.
- Há dois limites a considerar, a rotatividade de dados por disco e a rotatividade de dados da máquina virtual.

**Destino de armazenamento** | **E/S média de disco de origem** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1\.684 GB por disco

## <a name="replicated-machines---networking"></a>Máquinas replicadas - rede
**Configuração** | **Suporte** | **Detalhes**
--- | --- | ---
NIC | Número máximo suportado para um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada durante o failover.<br/><br/> O número de NICs na VM de failover depende do número de NICs na VM de origem quando a replicação foi ativada. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará o número de NICs na VM replicada após o failover. <br/><br/> A ordem de NICs após o failover não tem garantia de ser igual à ordem original. <br/><br/> Você pode renomear NICs na região de destino com base nas convenções de nomenclatura da sua organização.
Balanceador de Carga de Internet | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Com suporte | Associe o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Endereço IP público | Com suporte | Associe um endereço IP público existente à NIC. Ou crie um endereço IP público e associe-o à NIC usando um script de automação do Azure em um plano de recuperação.
NSG em NIC | Com suporte | Associe o NSG à NIC usando um script de automação do Azure em um plano de recuperação.
NSG na sub-rede | Com suporte | Associe o NSG à sub-rede usando um script de automação do Azure em um plano de recuperação.
Endereço IP reservado (estático) | Com suporte | Se a NIC na VM de origem tiver um endereço IP estático e a sub-rede de destino tiver o mesmo endereço IP disponível, ela será atribuída à VM com falha.<br/><br/> Se a sub-rede de destino não tiver o mesmo endereço IP disponível, um dos endereços IP disponíveis na sub-rede será reservado para a VM.<br/><br/> Você também pode especificar um endereço IP fixo e uma sub-rede na **itens replicados** > **configurações** > **de computação e rede** > **Interfaces de rede**.
Endereço IP dinâmico | Com suporte | Se a NIC de origem tiver o endereçamento IP dinâmico, a NIC na VM com failover também é dinâmica por padrão.<br/><br/> Você pode modificar isso para um endereço IP fixo, se necessário.
Vários endereços IP | Sem suporte | Quando você faz failover de uma VM que tem uma NIC com vários endereços IP, somente o endereço IP primário da NIC na região de origem é mantido. Para atribuir vários endereços IP, você pode adicionar VMs a um [plano de recuperação](recovery-plan-overview.md) e anexar um script para atribuir endereços IP adicionais ao plano ou pode fazer a alteração manualmente ou com um script após o failover.
Gerenciador de Tráfego     | Com suporte | Você pode pré-configurar o Traffic Manager para que o tráfego seja roteado para o terminal na região de origem regularmente e para o terminal na região de destino em caso de failover.
DNS do Azure | Com suporte |
DNS Personalizado    | Com suporte |
Proxy não autenticado | Com suporte | [Saiba mais](./azure-to-azure-about-networking.md)
Proxy autenticado | Sem suporte | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada com o Azure Site Recovery.
Conexão VPN site a site para um destino local<br/><br/>(com ou sem o ExpressRoute)| Com suporte | Verifique se as UDRs e os NSGs são configurados de tal forma que o tráfego do Site Recovery não seja encaminhado para o local. [Saiba mais](./azure-to-azure-about-networking.md)
Conexão VNET a VNET    | Com suporte | [Saiba mais](./azure-to-azure-about-networking.md)
Pontos de extremidade de serviço de rede virtual | Com suporte | Se você estiver restringindo o acesso à rede virtual para contas de armazenamento, certifique-se de que os serviços Microsoft confiáveis tenham a permissão para acessar a conta de armazenamento.
Redes aceleradas | Com suporte | A rede acelerada deve estar ativada na VM de origem. [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).
Dispositivo de Rede da Palo Alto | Sem suporte | Com dispositivos de terceiros, geralmente há restrições impostas pelo provedor dentro da máquina virtual. O Azure Site Recovery precisa que o agente, as extensões e a conectividade de saída estejam disponíveis. Mas o dispositivo não permite que nenhuma atividade de saída seja configurada dentro da máquina virtual.
IPv6  | Sem suporte | Também não há suporte para configurações mistas que incluem IPv4 e IPv6. Libere a sub-rede do intervalo de IPv6 antes de realizar operações do Site Recovery.
Acesso de link privado ao serviço de Site Recovery | Com suporte | [Saiba mais](azure-to-azure-how-to-enable-replication-private-endpoints.md)



## <a name="next-steps"></a>Próximas etapas

- Leia as [diretrizes de rede](./azure-to-azure-about-networking.md) para replicar VMs do Azure.
- Implante a recuperação de desastres [replicando as VMs do Azure](./azure-to-azure-quickstart.md).

