---
title: Matriz de suporte para recuperação de desastres do VMware/físico no Azure Site Recovery
description: Resume o suporte para recuperação de desastre de VMs VMware e servidor físico para o Azure usando Azure Site Recovery.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 8e1fd7e839c50458ddcfc9f83d152d7b45cd6672
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956246"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure

Este artigo resume os componentes e as configurações com suporte para recuperação de desastres de VMs VMware e servidores físicos no Azure usando [Azure site Recovery](site-recovery-overview.md).

- [Saiba mais](vmware-azure-architecture.md) sobre a arquitetura de recuperação de desastre do VMware VM/servidor físico.
- Siga nossos [tutoriais](tutorial-prepare-azure.md) para experimentar a recuperação de desastres.

## <a name="deployment-scenarios"></a>Cenários de implantação

**Cenário** | **Detalhes**
--- | ---
Recuperação de desastre de VMs VMware | Replicação de VMs VMware locais para Azure. Você pode implantar esse cenário no portal do Azure ou usando o [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperação de desastre de servidores físicos | Replicação de servidores físicos Windows/Linux locais para Azure. Implante esse cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
vCenter Server | Versão 7,0, 6,7, 6,5, 6,0 ou 5,5 | Recomendamos que você use um servidor vCenter em sua implantação de recuperação de desastre.
Hosts vSphere | Versão 7,0, 6,7, 6,5, 6,0 ou 5,5 | Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede que o servidor de processo. Por padrão, o servidor de processo é executado no servidor de configuração. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuração do Azure Site Recovery

O servidor de configuração é um computador local que executa componentes do Site Recovery, incluindo o servidor de configuração, servidor de processo e o servidor de destino mestre.

- Para VMs VMware, você define o servidor de configuração baixando um modelo OVF para criar uma VM VMware.
- Para servidores físicos, você configura manualmente o computador do servidor de configuração.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Os discos incluem o disco do sistema operacional, disco de cache do servidor de processo e a unidade de retenção para failback.
Espaço livre em disco | 600 GB de espaço para o cache do servidor de processo.
Espaço livre em disco | 600 GB de espaço para a unidade de retenção.
Sistema operacional  | Windows Server 2012 R2 ou Windows Server 2016 com experiência desktop <br/><br> Se você planeja usar o destino mestre interno deste dispositivo para failback, verifique se a versão do sistema operacional é igual ou superior aos itens replicados.|
Localidade do sistema operacional | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Não é necessário para o Configuration Server versão [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) ou posterior.
Funções do Windows Server | Não habilitar Active Directory Domain Services; Serviços de Informações da Internet (IIS) ou Hyper-V.
Políticas de grupo| - Impedir o acesso ao prompt de comando. <br/> - Impedir o acesso às ferramentas de edição do registro. <br/> - Lógica de confiança para anexos de arquivo. <br/> - Ativar a execução do script. <br/> - [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Verifique se você:<br/><br/> -Não tem um site padrão pré-existente <br/> - Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Não tem site/aplicativo preexistente ouvindo na porta 443<br/>
Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware)
Tipo de endereço IP | Estático
Portas | 443 usado para orquestração de canal de controle<br/>9443 para o transporte de dados

## <a name="replicated-machines"></a>Computadores replicados

O Site Recovery dá suporte para replicação de qualquer carga de trabalho em execução em um computador com suporte.

> [!Note]
> A tabela a seguir lista o suporte para computadores com a inicialização do BIOS. Consulte a seção de [armazenamento](#storage) para obter suporte em computadores baseados em UEFI.

**Componente** | **Detalhes**
--- | ---
Configurações do computador | Os computadores que são replicados para o Azure precisam atender aos [requisitos do Azure](#azure-vm-requirements).
Carga de trabalho do computador | O Site Recovery dá suporte para replicação de qualquer carga de trabalho em execução em um computador com suporte. [Saiba mais](https://aka.ms/asr_workload).
Nome do computador | Verifique se o nome de exibição do computador não se enquadra nos [nomes de recursos reservados do Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name)<br/><br/> Os nomes de volumes lógicos não diferenciam maiúsculas de minúsculas. Certifique-se de que dois volumes em um dispositivo tenham o mesmo nome. Por exemplo: volumes com nomes "voLUME1", "voLUME1" não podem ser protegidos por meio de Azure Site Recovery.

### <a name="for-windows"></a>Para Windows

**Sistema operacional** | **Detalhes**
--- | ---
Windows Server 2019 | Com suporte do [pacote cumulativo de atualizações 34](https://support.microsoft.com/help/4490016) (versão 9,22 do serviço de mobilidade) em diante.
Windows Server 2016 64 bits | Compatível com Server Core, Servidor com Experiência Desktop.
Windows Server 2012 R2/Windows Server 2012 | Com suporte.
Windows Server 2008 R2 com SP1 em diante. | Com suporte.<br/><br/> Da versão [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente do serviço de mobilidade, você precisa de um [Ssu (atualização da pilha de manutenção)](https://support.microsoft.com/help/4490628) e da [atualização do SHA-2](https://support.microsoft.com/help/4474419) instaladas em computadores que executam o Windows 2008 R2 com SP1 ou posterior. O SHA-1 não tem mais suporte desde setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 com SP2 ou posterior (64 bits/32 bits) |  Com suporte somente para migração. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/><br/> Da versão [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente do serviço de mobilidade, você precisa de um [Ssu (atualização da pilha de manutenção)](https://support.microsoft.com/help/4493730) e da [atualização do SHA-2](https://support.microsoft.com/help/4474419) instaladas em computadores com Windows 2008 SP2. ISHA-1 não tem suporte de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).
Windows 10, Windows 8.1, Windows 8 | Com suporte.
Windows 7 com SP1 64 bits | Com suporte do [pacote cumulativo de atualizações 36](https://support.microsoft.com/help/4503156) (versão 9,22 do serviço de mobilidade) em diante. </br></br> De [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente do serviço de mobilidade, você precisa de um [Ssu (atualização da pilha de manutenção)](https://support.microsoft.com/help/4490628) e da [atualização do SHA-2](https://support.microsoft.com/help/4474419) instaladas em computadores com o Windows 7 SP1.  O SHA-1 não tem mais suporte desde setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não será instalada/atualizada conforme o esperado. Saiba mais sobre a [atualização e os requisitos do SHA-2](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus).

### <a name="for-linux"></a>Para Linux

**Sistema operacional** | **Detalhes**
--- | ---
Linux | Somente o sistema de 64 bits tem suporte. Não há suporte para o sistema de 32 bits.<br/><br/>Todos os servidores Linux devem ter [componentes de Lis (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) instalados. É necessário inicializar o servidor no Azure após failover/failover de teste. Se os componentes LIS internos estiverem ausentes, certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para que os computadores inicializem no Azure. <br/><br/> O Site Recovery orquestra o failover para executar servidores Linux no Azure. No entanto, os fornecedores de Linux podem limitar o suporte a apenas versões de distribuição que não atingiram o fim da vida útil.<br/><br/> Nas distribuições Linux, apenas os kernels de estoque que fazem parte da versão/atualização de versão secundária de distribuição têm suporte.<br/><br/> Não há suporte para atualização de computadores protegidos nas versões principais de distribuição do Linux. Para atualizar, desabilite a replicação, atualize o sistema operacional e, em seguida, habilite a replicação novamente.<br/><br/> [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre o suporte para Linux e tecnologia de código-fonte aberto no Azure.
Linux Red Hat Enterprise | 5,2 a 5,11</b><br/> 6,1 a 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609) <br/> Alguns kernels mais antigos em servidores que executam Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 não têm [componentes de Lis (Linux Integration Services)](https://www.microsoft.com/download/details.aspx?id=55106) pré-instalados. Se os componentes LIS internos estiverem ausentes, certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para que os computadores inicializem no Azure.
Linux: CentOS | 5,2 a 5,11</b><br/> 6,1 a 6,10</b><br/> 7,0 a 7,8<br/> <br/> 8,0, 8,1, [8,2](https://support.microsoft.com/help/4570609) <br/><br/> Alguns kernels mais antigos em servidores que executam o CentOS 5.2-5.11 & 6.1-6.10 não têm [componentes LIS (Integration Services do Linux)](https://www.microsoft.com/download/details.aspx?id=55106) pré-instalados. Se os componentes LIS internos estiverem ausentes, certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para que os computadores inicializem no Azure.
Ubuntu | Ubuntu 14, 4 LTS Server [(examinar as versões de kernel com suporte)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16, 4 LTS Server [(examinar as versões de kernel com suporte)](#ubuntu-kernel-versions) </br> Ubuntu 18, 4 LTS Server [(examinar as versões de kernel com suporte)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 (inclui suporte para todos os 7. *x*, 8. *x* versões) [(examinar as versões de kernel com suporte)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(examine as versões de kernel com suporte)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(examine as versões de kernel com suporte)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Não há suporte para a atualização de máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para o SP4. Para atualizar, desabilite a replicação e habilite novamente após a atualização.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6 e [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Executando o kernel compatível com Red Hat ou o Unbreakable Enterprise Kernel Release 3, 4 e 5 (UEK3, UEK4 e UEK5)

> [!Note]
> Para cada uma das versões do Windows, Azure Site Recovery dá suporte apenas a compilações de [LTSC (canal de manutenção em longo prazo)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) .  Versões de [canal semianuais](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) atualmente não têm suporte no momento.

### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu

**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | [9,32][9.32 UR], [9,33](https://support.microsoft.com/help/4564347/), [9,34](https://support.microsoft.com/help/4570609) | 3.13.0-24-generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
|||
16.04 LTS | [9,34](https://support.microsoft.com/help/4570609) | 4.4.0-21-Generic para 4.4.0-178-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-101-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1083-Azure |
16.04 LTS | [9,33](https://support.microsoft.com/help/4564347/) | 4.4.0-21-Generic para 4.4.0-178-Generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-99-Generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1082-Azure|
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-generic a 4.4.0-171-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-74-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-generic a 4.4.0-170-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-72-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1063-azure|
|||
18.04 LTS | [9,34](https://support.microsoft.com/help/4570609) | 4.15.0-20-Generic para 4.15.0-101-Generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-Generic para 5.0.0-48-Generic </br> 5.3.0-19-generic para 5.3.0-53-Generic </br> 4.15.0-1009-Azure para 4.15.0-1083-Azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-Azure para 5.0.0-1036-Azure </br> 5.3.0-1007-Azure para 5.3.0-1022-Azure|
18.04 LTS | [9,33](https://support.microsoft.com/help/4564347/) | 4.15.0-20-Generic para 4.15.0-99-Generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-Generic para 5.0.0-47-Generic </br> 5.3.0-19-generic para 5.3.0-51-Generic </br> 4.15.0-1009-Azure para 4.15.0-1082-Azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-Azure para 5.0.0-1036-Azure </br> 5.3.0-1007-Azure para 5.3.0-1020-Azure|
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generic a 4.15.0-74-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic a 5.0.0-37-generic </br> 5.3.0-19-generic a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1028-azure </br> 5.3.0-1007-azure a 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-generic a 4.15.0-72-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic a 5.0.0-37-generic </br> 5.3.0-19-generic a 5.3.0-24-generic </br> 4.15.0-1009-azure a 4.15.0-1037-azure </br> 4.18.0-1006-azure a 4.18.0-1025-azure </br> 5.0.0-1012-azure a 5.0.0-1025-azure </br> 5.3.0-1007-azure|

### <a name="debian-kernel-versions"></a>Versões de Kernel do Debian


**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | [9,31][9.31 UR], [9,32][9.32 UR], [9,33](https://support.microsoft.com/help/4564347/), [9,34](https://support.microsoft.com/help/4570609)| 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,31][9.31 UR], [9,32][9.32 UR], [9,33](https://support.microsoft.com/help/4564347/), [9,34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões de kernel com suporte

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,34](https://support.microsoft.com/help/4570609) | Todos os kernels SUSE 12 SP1, SP2, SP3 e SP4 padrão são compatíveis.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-Azure para 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure para 4.12.14-16.13-Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3 e SP4) | 9,32, [9,33](https://support.microsoft.com/help/4564347/) | Todos os kernels SUSE 12 SP1, SP2, SP3 e SP4 padrão são compatíveis.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3 e SP4) | 9.31 | Todos os kernels SUSE 12 SP1, SP2, SP3 e SP4 padrão são compatíveis.</br></br> 4.4.138-4.7-azure a 4.4.180-4.31-azure,</br>4.12.14-6.3-azure a 4.12.14-6.29-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 versões de kernel com suporte

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | [9,34](https://support.microsoft.com/help/4570609)  | Por padrão, todos os [kernels 15 e SUSE 15 padrão](https://www.suse.com/support/kb/doc/?id=000019587) são compatíveis.</br></br> 4.12.14-5.5-Azure para 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure para 4.12.14-8.30-Azure 
SUSE Linux Enterprise Server 15 e 15 SP1 | [9,33](https://support.microsoft.com/help/4564347/) | Por padrão, todos os [kernels 15 e SUSE 15 padrão](https://www.suse.com/support/kb/doc/?id=000019587) são compatíveis.</br></br> 4.12.14-5.5-Azure para 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure para 4.12.14-8.30-Azure |
SUSE Linux Enterprise Server 15 e 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | Por padrão, todos os [kernels 15 e SUSE 15 padrão](https://www.suse.com/support/kb/doc/?id=000019587) são compatíveis. </br></br> 4.12.14-5.5-azure a 4.12.14-8.22-azure



## <a name="linux-file-systemsguest-storage"></a>Sistemas de arquivos Linux/armazenamento convidado

**Componente** | **Com suporte**
--- | ---
Sistemas de arquivos | ext3, ext4, XFS, BTRFS (condições aplicáveis de acordo com esta tabela)
Provisionamento LVM (gerenciamento de volume lógico)| Provisão espessa-Sim <br></br> Provisionamento dinâmico-não
Gerenciador de volumes | -O LVM tem suporte.<br/> -/boot no LVM tem suporte do [pacote cumulativo de atualizações 31](https://support.microsoft.com/help/4478871/) (versão 9,20 do serviço de mobilidade) em diante. Ele não tem suporte em versões anteriores do serviço de mobilidade.<br/> -Não há suporte para vários discos de sistema operacional.
Dispositivos de armazenamento paravirtualizados | Não há suporte para dispositivos exportados por drivers paravirtualizados.
Dispositivos de E/S de bloqueio de várias filas | Sem suporte.
Servidores físicos com o controlador de armazenamento CCISS da HP | Sem suporte.
Convenção de nomenclatura de ponto de montagem/dispositivo | O nome do dispositivo ou o nome do ponto de montagem deve ser exclusivo.<br/> Verifique se não há dois dispositivos/pontos de montagem com nomes que diferenciam maiúsculas de minúsculas. Por exemplo, não há suporte para nomear dispositivos para a mesma VM que *Device1* e *Device1* .
Diretórios | Se você estiver executando uma versão do serviço de mobilidade anterior à versão 9,20 (lançada no [pacote cumulativo de atualizações 31](https://support.microsoft.com/help/4478871/)), essas restrições se aplicarão:<br/><br/> -Esses diretórios (se configurados como partições/sistemas de arquivos separados) devem estar no mesmo disco do sistema operacional no servidor de origem:/(raiz),/boot,/usr,/usr/local,/var,/etc.</br> -O diretório/boot deve estar em uma partição de disco e não ser um volume LVM.<br/><br/> Da versão 9,20 em diante, essas restrições não se aplicam. 
Diretório de inicialização | -Discos de inicialização não deve estar no formato de partição GPT. Essa é uma limitação da arquitetura do Azure. Os discos GPT têm suporte como discos de dados.<br/><br/> Não há suporte para vários discos de inicialização em uma VM<br/><br/> -/boot em um volume LVM em mais de um disco não tem suporte.<br/> -Um computador sem um disco de inicialização não pode ser replicado.
Requisitos de espaço livre| 2 GB na /partição raiz <br/><br/> 250 MB na pasta de instalação
XFSv5 | Os recursos de XFSv5 em sistemas de arquivos XFS, como a soma de verificação de metadados, têm suporte (serviço de mobilidade versão 9,10 em diante).<br/> Use o utilitário xfs_info para verificar o superbloco XFS da partição. Se `ftype` é definido como 1, os recursos do XFSv5 estão em uso.
BTRFS | O BTRFS tem suporte do [pacote cumulativo de atualizações 34](https://support.microsoft.com/help/4490016) (versão 9,22 do serviço de mobilidade) em diante. BTRFS não tem suporte se:<br/><br/> -O subvolume do sistema de arquivos BTRFS é alterado após habilitar a proteção.</br> -O sistema de arquivos BTRFS está espalhado por vários discos.</br> -O sistema de arquivos BTRFS dá suporte a RAID.

## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco na VM replicada | Com suporte na VM de origem antes do failover, diretamente nas propriedades da VM. Não é necessário desabilitar/reabilitar a replicação.<br/><br/> Se você alterar a VM de origem após o failover, as alterações não serão capturadas.<br/><br/> Se você alterar o tamanho do disco na VM do Azure após o failover, ao fazer failback, Site Recovery criará uma nova VM com as atualizações.
Adicionar disco na VM replicada | Sem suporte.<br/> Desabilite a replicação da VM, adicione o disco e, em seguida, habilite novamente a replicação.

## <a name="network"></a>Rede

**Componente** | **Com suporte**
--- | ---
Agrupamento NIC da rede do host | Compatível com VMs da VMware. <br/><br/>Sem suporte para a replicação de computador físico.
VLAN da rede do host | Sim.
IPv4 da rede do host | Sim.
IPv6 da rede do host | Não.
Agrupamento NIC da rede do convidado/servidor | Não.
IPv4 da rede do convidado/servidor | Sim.
IPv6 da rede do convidado/servidor | Não.
IP estático da rede do convidado/servidor (Windows) | Sim.
IP estático da rede do convidado/servidor (Linux) | Sim. <br/><br/>As VMs são configuradas para usar o DHCP no failback.
Várias NICs da rede do convidado/servidor | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede VM do Azure (após o failover)

**Componente** | **Com suporte**
--- | ---
Azure ExpressRoute | Sim
ILB | Sim
ELB | Sim
Gerenciador de Tráfego do Azure | Sim
NIC múltipla | Sim
Endereço IP Reservado | Sim
IPv4 | Sim
Manter endereço IP de origem | Sim
Pontos de extremidade de serviço de rede virtual do Azure<br/> | Sim
Redes aceleradas | Não

## <a name="storage"></a>Armazenamento
**Componente** | **Com suporte**
--- | ---
Dados dinâmicos | O disco do sistema operacional deve ser um disco básico. <br/><br/>Os discos de Dados podem ser discos dinâmicos
Configuração de disco do Docker | Não
NFS do host | Sim para VMware<br/><br/> Não para servidores físicos
Host SAN iSCSI/FC) | Sim
Host vSAN | Sim para VMware<br/><br/> N/D para servidores físicos
MPIO (Múltiplos caminhos) do host | Sim, testado com Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
Volumes de host Virtual (VVols) | Sim para VMware<br/><br/> N/D para servidores físicos
VMDK do convidado/servidor | Sim
Disco de cluster compartilhado do convidado/servidor | Não
Disco criptografado do convidado/servidor | Não
NFS do convidado/servidor | Não
ISCSI de convidado/servidor | Para migração-Sim<br/>Para recuperação de desastre-não, o iSCSI fará o failback como um disco anexado à VM
SMB 3.0 do convidado/servidor | Não
RDM do convidado/servidor | Sim<br/><br/> N/D para servidores físicos
Disco do convidado/servidor > 1 TB | Sim, o disco deve ter mais de 1024 MB<br/><br/>Até 8.192 GB ao replicar para discos gerenciados (versão 9,26 em diante)<br></br> Até 4.095 GB ao replicar para contas de armazenamento
Disco do convidado/servidor com tamanho de setor lógico e físico de 4.000 cada | Não
Disco de convidado/servidor com tamanho de setor físico de 512 bytes e lógicos de 4K | Não
Volume do convidado/servidor com discos distribuídos >4 TB | Sim
Gerenciamento de volumes lógicos (LVM)| Provisionamento espesso-Sim <br></br> Provisionamento dinâmico-não
Convidado/servidor - espaços de armazenamento | Não
Adicionar/remover disco a quente por convidado/servidor | Não
Convidado/servidor - excluir disco | Sim
MPIO (Múltiplos caminhos) de convidado/servidor | Não
Partições do convidado/servidor GPT | Há suporte para cinco partições do [pacote cumulativo de atualizações 37](https://support.microsoft.com/help/4508614/) (versão 9,25 do serviço de mobilidade) em diante. Quatro eram suportados anteriormente.
ReFS | O sistema de arquivos resiliente tem suporte com o serviço de mobilidade versão 9,23 ou superior
Inicialização de EFI/servidor do convidado/UEFI | -Com suporte para Windows Server 2012 ou posterior, SLES 12 SP4 e RHEL 8,0 com agente de mobilidade versão 9,30 em diante<br/> -Não há suporte para o tipo de inicialização UEFI segura. [Saiba mais.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#on-premises-vs-azure-generation-2-vms)

## <a name="replication-channels"></a>Canais de replicação

|**Tipo de replicação**   |**Com suporte**  |
|---------|---------|
|Transferências de dados descarregadas (ODX)    |       Não  |
|Propagação offline        |   Não      |
| Azure Data Box | Não

## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Com suporte**
--- | ---
Armazenamento com redundância local | Sim
Armazenamento com redundância geográfica | Sim
Armazenamento com redundância geográfica com acesso de leitura | Sim
Armazenamento frio | Não
Armazenamento quente| Não
Blobs de bloco | Não
Criptografia em repouso (SSE)| Sim
Criptografia em repouso (CMK)| Sim (por meio do PowerShell AZ 3.3.0 Module em diante)
Armazenamento Premium | Sim
Serviço de importação/exportação | Não
Firewalls do armazenamento do Azure para VNets | Sim.<br/> Configurado na conta de armazenamento de armazenamento/cache de destino (usada para armazenar dados de replicação).
Contas de armazenamento v2 de uso geral (camadas quentes e frias) | Sim (os custos de transações são consideravelmente mais altos para v2 em comparação com v1)

## <a name="azure-compute"></a>Computação do Azure

**Recurso** | **Com suporte**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
HUB | Sim
Discos gerenciados | Sim

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

As VMs locais replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando Site Recovery executar uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verificar [sistemas operacionais com suporte](#replicated-machines) para computadores replicados. | A verificação falha se não tiver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falha se não tiver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 8.192 GB ao replicar para o disco gerenciado (versão 9,26 em diante)<br></br>Até 4.095 GB ao replicar para a conta de armazenamento| A verificação falha se não tiver suporte.
Adaptadores de Rede | Há suporte para vários adaptadores. |
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte.
Disco FC | Sem suporte. | A verificação falha se não tiver suporte.
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador. |
Nome da VM | De 1 a 63 caracteres.<br/><br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.

## <a name="resource-group-limits"></a>Limites de grupo de recursos

Para entender o número de máquinas virtuais que podem ser protegidas em um único grupo de recursos, consulte o artigo sobre [limites e cotas de assinatura](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits).

## <a name="churn-limits"></a>Limites de rotatividade

A tabela a seguir fornece os limites do Azure Site Recovery.
- Esses limites se baseiam em nossos testes, mas não cobrem todas as combinações de e/s de aplicativo possíveis.
- Os resultados reais podem variar dependendo da combinação de E/S do aplicativo.
- Para obter melhores resultados, é altamente recomendável que você execute a [ferramenta de planejador de implantações](site-recovery-deployment-planner.md)e execute testes de aplicativo extensivos usando failovers de teste para obter a imagem de desempenho real para seu aplicativo.

**Destino de replicação** | **Tamanho de E/S de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1\.684 GB por disco


**Variação de dados de origem** | **Limite máximo**
---|---
Variação de dados de pico entre todos os discos em uma VM | 54 MB/s
Variação máxima de dados por dia com suporte de um Servidor de Processo | 2 TB

- Esses são números médios, pressupondo uma sobreposição de E/S de 30%.
- O Site Recovery pode lidar com uma maior taxa de transferência com base na taxa de sobreposição, em tamanhos maiores de gravação e em comportamento de E/S de carga de trabalho real.
- Esses números pressupõem uma pendência típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados, e um ponto de recuperação é criado dentro de cinco minutos.

## <a name="vault-tasks"></a>Tarefas do Vault

**Ação** | **Com suporte**
--- | ---
Mover cofre entre grupos de recursos | Não
Mover o cofre dentro e entre assinaturas | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos | Não
Mova armazenamento, rede, VMs do Azure dentro e entre assinaturas. | Não


## <a name="obtain-latest-components"></a>Obter os componentes mais recentes

**Nome** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Instalado no local.<br/> Coordena as comunicações entre servidores VMware locais ou computadores físicos e o Azure. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de configuração.<br/> - [Saiba mais sobre como](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) atualizar para a versão mais recente.<br/> - [Saiba mais sobre como](vmware-azure-deploy-configuration-server.md) configurar o servidor de configuração.
Servidor de processo | Instalado por padrão no servidor de configuração.<br/> Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Azure.<br/> À medida que sua implantação cresce, você pode adicionar servidores de processo adicionais para lidar com volumes maiores de tráfego de replicação. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de processo.<br/> - [Saiba mais sobre como](vmware-azure-manage-process-server.md#upgrade-a-process-server) atualizar para a versão mais recente.<br/> - [Saiba mais sobre como](vmware-physical-large-deployment.md#set-up-a-process-server) configurar servidores de processo de expansão.
Serviço de Mobilidade | Instalado na VM VMware ou servidores físicos que você deseja replicar.<br/> Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure.| - [Saiba mais sobre](vmware-physical-mobility-service-overview.md) o serviço de mobilidade.<br/> - [Saiba mais sobre como](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) atualizar para a versão mais recente.<br/>



## <a name="next-steps"></a>Próximas etapas
[Saiba como](tutorial-prepare-azure.md) para preparar o Azure para recuperação de desastres de máquinas virtuais da VMware.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
