---
title: Matriz de suporte para recuperação de vMware/desastre físico na recuperação do site do Azure
description: Resume o suporte para recuperação de desastres de VMware VMs e servidor físico para o Azure usando o Azure Site Recovery.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: fbd5d87b219cbb482569dc5e45adc9c81181670c
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672446"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure

Este artigo resume os componentes e configurações suportadas para recuperação de desastres de VMware VMware e servidores físicos para o Azure usando [o Azure Site Recovery](site-recovery-overview.md).

- [Saiba mais](vmware-azure-architecture.md) sobre a arquitetura de recuperação de desastres vmware/servidor físico VMware.
- Siga [nossos tutoriais](tutorial-prepare-azure.md) para testar a recuperação de desastres.

## <a name="deployment-scenarios"></a>Cenários de implantação

**Cenário** | **Detalhes**
--- | ---
Recuperação de desastres de VMware VMs | Replicação de VMs VMware locais para Azure. Você pode implantar este cenário no portal Azure ou usando [o PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperação de desastres de servidores físicos | Replicação de servidores físicos Windows/Linux locais para Azure. Implante esse cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
vCenter Server | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que você use um servidor vCenter em sua implantação de recuperação de desastres.
Hosts vSphere | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede que o servidor de processo. Por padrão, o servidor de processo é executado no servidor de configuração. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuração do Azure Site Recovery

O servidor de configuração é um computador local que executa componentes do Site Recovery, incluindo o servidor de configuração, servidor de processo e o servidor de destino mestre.

- Para VMware VMs, você define o servidor de configuração baixando um modelo OVF para criar uma VMware VM.
- Para servidores físicos, você configura a máquina do servidor de configuração manualmente.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Os discos incluem o disco do sistema operacional, disco de cache do servidor de processo e a unidade de retenção para failback.
Espaço livre em disco | 600 GB de espaço para o cache do servidor de processo.
Espaço livre em disco | 600 GB de espaço para a unidade de retenção.
Sistema operacional  | Windows Server 2012 R2 ou Windows Server 2016 com experiência em Desktop <br/><br> Se você planeja usar o Alvo Mestre incorporado deste aparelho para failback, certifique-se de que a versão do Sistema Operacional seja igual ou superior aos itens replicados.|
Localidade do sistema operacional | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Não é necessário para a configuração do servidor versão [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) ou posterior.
Funções do Windows Server | Não habilite serviços de domínio de diretório ativo; Serviços de Informação da Internet (IIS) ou Hiper-V.
Políticas de grupo| - Impedir o acesso ao prompt de comando. <br/> - Impedir o acesso às ferramentas de edição do registro. <br/> - Lógica de confiança para anexos de arquivo. <br/> - Ativar a execução do script. <br/> - [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Verifique se você:<br/><br/> - Não tenha um site padrão pré-existente <br/> - Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Não tem site/aplicativo preexistente ouvindo na porta 443<br/>
Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware)
Tipo de endereço IP | Estático
Portas | 443 usado para orquestração de canais de controle<br/>9443 para transporte de dados

## <a name="replicated-machines"></a>Computadores replicados

O Site Recovery dá suporte para replicação de qualquer carga de trabalho em execução em um computador com suporte.

> [!Note]
> A tabela a seguir lista o suporte para máquinas com inicialização do BIOS. Consulte a seção [Armazenamento](#storage) para obter suporte em máquinas baseadas em UEFI.

**Componente** | **Detalhes**
--- | ---
Configurações do computador | Os computadores que são replicados para o Azure precisam atender aos [requisitos do Azure](#azure-vm-requirements).
Carga de trabalho do computador | O Site Recovery dá suporte para replicação de qualquer carga de trabalho em execução em um computador com suporte. [Saiba mais](https://aka.ms/asr_workload).
Nome do computador | Certifique-se de que o nome de exibição da máquina não caia em [nomes de recursos reservados do Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name)<br/><br/> Nomes de volume lógicos não são sensíveis a maiúsculas e minúsculas. Certifique-se de que nenhum volume em um dispositivo tenha o mesmo nome. Ex: Volumes com nomes "voLUME1", "volume1" não podem ser protegidos através da Recuperação do Site do Azure.
Windows Server 2019 | Suportado a partir de [Update rollup 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço de mobilidade) em diante.
Windows Server 2016 64 bits | Suportado para server core, servidor com experiência de desktop.
Windows Server 2012 R2 / Windows Server 2012 |  Com suporte.
Windows Server 2008 R2 com SP1 em diante. |  Com suporte.<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço mobility, você precisa [de atualização de pilha de manutenção (SSU)](https://support.microsoft.com/help/4490628) e atualização [SHA-2](https://support.microsoft.com/help/4474419) instalada em máquinas que executam o Windows 2008 R2 com SP1 ou posterior. O SHA-1 não é suportado a partir de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e os requisitos do SHA-2.](https://aka.ms/SHA-2KB)
Windows Server 2008 com SP2 ou posterior (64 bits/32 bits) |  Suportado apenas para migração. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/><br/> A partir da versão [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço mobility, você precisa [de atualização de pilha de manutenção (SSU)](https://support.microsoft.com/help/4493730) e atualização [SHA-2](https://support.microsoft.com/help/4474419) instalada em máquinas SP2 do Windows 2008. O ISHA-1 não é suportado a partir de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e os requisitos do SHA-2.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Windows 10, Windows 8.1, Windows 8 |  Com suporte.
Windows 7 com SP1 de 64 bits | Suportado a partir de [Update rollup 36](https://support.microsoft.com/help/4503156) (versão 9.22 do serviço de mobilidade) em diante. </br></br> A partir das [9:30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) do agente de serviço slo, você precisa [de atualização de pilha de manutenção (SSU)](https://support.microsoft.com/help/4490628) e atualização [SHA-2](https://support.microsoft.com/help/4474419) instalada em máquinas Do Windows 7 SP1.  O SHA-1 não é suportado a partir de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não instalará/atualizará como esperado. Saiba mais sobre [a atualização e os requisitos do SHA-2.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Linux | Apenas o sistema de 64 bits é suportado. O sistema de 32 bits não é suportado.<br/><br/>Todos os servidores Linux devem ter [componentes LIS (Linux Integration Services, serviços de integração linux) instalados.](https://www.microsoft.com/download/details.aspx?id=55106) É necessário inicializar o servidor no Azure após o failover/failover do teste. Se os componentes LIS estiverem faltando, certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de ativar a replicação para que as máquinas sejam inicializar no Azure. <br/><br/> O Site Recovery orquestra o failover para executar servidores Linux no Azure. No entanto, os fornecedores de Linux podem limitar o suporte a apenas versões de distribuição que não atingiram o fim da vida útil.<br/><br/> Nas distribuições Linux, apenas os kernels de estoque que fazem parte da versão/atualização de versão secundária de distribuição têm suporte.<br/><br/> Não há suporte para atualização de computadores protegidos nas versões principais de distribuição do Linux. Para atualizar, desabilite a replicação, atualize o sistema operacional e, em seguida, habilite a replicação novamente.<br/><br/> [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre suporte para Linux e tecnologia de código aberto no Azure.
Linux Red Hat Enterprise | 5.2 a 5.11</b><br/> 6.1 a 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> Os servidores que executam o Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 não têm [componentes do Linux Integration Services (LIS) pré-instalados.](https://www.microsoft.com/download/details.aspx?id=55106) Certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de ativar a replicação para que as máquinas inicialiem no Azure.
Linux: CentOS | 5.2 a 5.11</b><br/> 6.1 a 6.10</b><br/> 7.0 a 7.6<br/> <br/> 8.0 a 8.1<br/><br/> Os servidores que executam o CentOS 5.2-5.11 & 6.1-6.10 não têm [componentes LIS (Linux Integration Services, serviços de integração linux) pré-instalados.](https://www.microsoft.com/download/details.aspx?id=55106) Certifique-se de instalar os [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de ativar a replicação para que as máquinas inicialiem no Azure.
Ubuntu | Servidor Ubuntu 14.04 LTS [(versões do kernel suportadas por revisão)](#ubuntu-kernel-versions)<br/><br/>Servidor Ubuntu 16.04 LTS [(versões do kernel suportadas por revisão)](#ubuntu-kernel-versions) </br> Servidor Ubuntu 18.04 LTS [(versões do kernel suportadas por revisão)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(versões de kernel suportadas por revisão)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(versões de kernel suportadas por revisão)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(versões de kernel suportadas por revisão)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Não é suportado o upgrade de máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para o SP4. Para atualizar, desative a replicação e reative após a atualização.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Executando o kernel compatível com red hat ou unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)

> [!Note]
> Para cada uma das versões do Windows, o Azure Site Recovery só suporta compilações de [Canal de Manutenção de Longo Prazo (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  Os lançamentos [semi-anuais do Canal](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) estão atualmente sem suporte no momento.

### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu

**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-genérico para 3.13.0-170-genérico,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-genérico para 4.4.0-148-genérico,<br/>4.15.0-1023-azure para 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-genérico para 4.4.0-171-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-74-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-genérico para 4.4.0-170-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-72-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-genérico para 4.4.0-166-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-66-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-genérico para 4.4.0-164-genérico,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-genérico para 4.15.0-64-genérico<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure para 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-genérico para 4.15.0-74-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico para 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1028-azure </br> 5.3.0-1007-azure para 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-genérico para 4.15.0-72-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-37-genérico </br> 5.3.0-19-genérico para 5.3.0-24-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-genérico para 4.15.0-66-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico a 5.0.0-32-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-genérico para 4.15.0-62-genérico </br> 4.18.0-13-genérico para 4.18.0-25-genérico </br> 5.0.0-15-genérico para 5.0.0-27-genérico </br> 4.15.0-1009-azure para 4.15.0-1037-azure </br> 4.18.0-1006-azure para 4.18.0-1025-azure </br> 5.0.0-1012-azure para 5.0.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Versões de Kernel do Debian


**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32][9.32 UR]| 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR] | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 a 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões de kernel com suporte

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.117-default</br></br>SP3 4.4.73-5-default para 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-azure para 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure para 4.12.14-6.23-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default para 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure para 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure para 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default para 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default para 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure para 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure para 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default para 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default para 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure para 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default para 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure para 4.12.14-6.9-azure |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 versões de kernel suportadas

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 e 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | Todos os [sUSE 15 e 15 núcleos](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) de estoque são suportados. </br></br> 4.12.14-5.5-azure para 4.12.14-8.22-azure

## <a name="linux-file-systemsguest-storage"></a>Sistemas de arquivos Linux/armazenamento convidado

**Componente** | **Com suporte**
--- | ---
Sistemas de arquivos | ext3, ext4, XFS, BTRFS (condições aplicáveis conforme esta tabela)
Gerenciador de volumes | - O LVM é suportado.<br/> - /boot on LVM é suportado a partir de [Update Rollup 31](https://support.microsoft.com/help/4478871/) (versão 9.20 do serviço Mobility) em diante. Não é suportado em versões anteriores do serviço Mobility.<br/> - Vários discos do sistema operacional não são suportados.
Dispositivos de armazenamento paravirtualizados | Não há suporte para dispositivos exportados por drivers paravirtualizados.
Dispositivos de E/S de bloqueio de várias filas | Sem suporte.
Servidores físicos com o controlador de armazenamento CCISS da HP | Sem suporte.
Convenção de nomenclatura de ponto de montagem/dispositivo | O nome do dispositivo ou o nome do ponto de montagem deve ser exclusivo.<br/> Certifique-se de que nenhum dispositivo/pontos de montagem tenha nomes sensíveis a maiúsculas e minúsculas. Por exemplo, dispositivos de nomeação para a mesma VM *do dispositivo1* e *do Dispositivo1* não são suportados.
Diretórios | Se você estiver executando uma versão do serviço Mobility antes da versão 9.20 (lançada no [Update Rollup 31),](https://support.microsoft.com/help/4478871/)então essas restrições se aplicam:<br/><br/> - Esses diretórios (se configurados como partições separadas/sistemas de arquivos) devem estar no mesmo disco do SISTEMA OPERACIONAL no servidor de origem: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - O diretório /boot deve estar em uma partição de disco e não ser um volume LVM.<br/><br/> A partir da versão 9.20 em diante, essas restrições não se aplicam. 
Diretório de inicialização | - Os discos de inicialização não devem estar no formato de partição GPT. Esta é uma limitação de arquitetura Azure. Os discos GPT são suportados como discos de dados.<br/><br/> Vários discos de inicialização em uma VM não são suportados<br/><br/> - /inicialização em um volume LVM em mais de um disco não é suportado.<br/> - Uma máquina sem um disco de inicialização não pode ser replicada.
Requisitos de espaço livre| 2 GB na /partição raiz <br/><br/> 250 MB na pasta de instalação
XFSv5 | Os recursos xFSv5 em sistemas de arquivos XFS, como o soma de verificação de metadados, são suportados (versão 9.10 do serviço de mobilidade em diante).<br/> Use o utilitário xfs_info para verificar o superbloco XFS da partição. Se `ftype` estiver definido como 1, os recursos XFSv5 estão em uso.
Btrfs | O BTRFS é suportado a partir do [Update Rollup 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço Mobility) em diante. O BTRFS não é suportado se:<br/><br/> - O subvolume do sistema de arquivos BTRFS é alterado após a habilitação da proteção.</br> - O sistema de arquivos BTRFS está espalhado por vários discos.</br> - O sistema de arquivos BTRFS suporta RAID.

## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco na VM replicada | Suportado na VM de origem antes do failover, diretamente nas propriedades vm. Não há necessidade de desativar/reativar a replicação.<br/><br/> Se você alterar a VM de origem após o failover, as alterações não serão capturadas.<br/><br/> Se você alterar o tamanho do disco na VM do Azure após o failover, quando você falhar, a Recuperação do Site criará uma nova VM com as atualizações.
Adicionar disco na VM replicada | Sem suporte.<br/> Desativar a replicação da VM, adicionar o disco e, em seguida, reativar a replicação.

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
Dados dinâmicos | O disco do SISTEMA OPERACIONAL deve ser um disco básico. <br/><br/>Os discos de Dados podem ser discos dinâmicos
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
ISCSI convidado/servidor | Para migração - Sim<br/>Para recuperação de desastres - Não, o iSCSI falhará como um disco conectado à VM
SMB 3.0 do convidado/servidor | Não
RDM do convidado/servidor | Sim<br/><br/> N/D para servidores físicos
Disco do convidado/servidor > 1 TB | Sim, o disco deve ser maior que 1024 MB<br/><br/>Até 8.192 GB ao replicar em discos gerenciados (versão 9.26 em diante)<br></br> Até 4.095 GB ao replicar contas de armazenamento
Disco do convidado/servidor com tamanho de setor lógico e físico de 4.000 cada | Não
Disco convidado/servidor com tamanho do setor físico lógico 4K e 512 bytes | Não
Volume do convidado/servidor com discos distribuídos >4 TB <br/><br/>Gerenciamento de volumes lógicos (LVM)| Sim
Convidado/servidor - espaços de armazenamento | Não
Adicionar/remover disco a quente por convidado/servidor | Não
Convidado/servidor - excluir disco | Sim
MPIO (Múltiplos caminhos) de convidado/servidor | Não
Partições GPT de hóspedes/servidor | Cinco partições são suportadas a partir do [Update Rollup 37](https://support.microsoft.com/help/4508614/) (versão 9.25 do serviço Mobility) em diante. Anteriormente, quatro foram apoiados.
ReFS | O Sistema de Arquivos Resiliente é suportado com a versão 9.23 ou superior do serviço mobility
Inicialização EFI/UEFI do servidor | - Suportado para Windows Server 2012 ou posterior, SLES 12 SP4 e RHEL 8.0 com agente de mobilidade versão 9.30 em diante<br/> - Não é suportado o tipo de inicialização UEFI segura.

## <a name="replication-channels"></a>Canais de replicação

|**Tipo de replicação**   |**Com suporte**  |
|---------|---------|
|Transferências de dados descarregadas (ODX)    |       Não  |
|Semeamento offline        |   Não      |
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
Criptografia em repouso (CMK)| Sim (via módulo PowerShell Az 3.3.0 em diante)
Armazenamento Premium | Sim
Serviço de importação/exportação | Não
Firewalls de armazenamento Azure para VNets | Sim.<br/> Configurado na conta de armazenamento/armazenamento de cache de destino (usada para armazenar dados de replicação).
Contas de armazenamento v2 de uso geral (níveis quentes e legais) | Sim (os custos de transação são substancialmente mais altos para V2 em comparação com V1)

## <a name="azure-compute"></a>Computação do Azure

**Recurso** | **Com suporte**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
HUB | Sim
Discos gerenciados | Sim

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

As VMs no local replicadas para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela. Quando a Recuperação do Site executa uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verificar [sistemas operacionais com suporte](#replicated-machines) para computadores replicados. | A verificação falha se não tiver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falha se não tiver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 8.192 GB ao replicar em disco gerenciado (versão 9.26 em diante)<br></br>Até 4.095 GB ao replicar na conta de armazenamento| A verificação falha se não tiver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte.
Disco FC | Sem suporte. | A verificação falha se não tiver suporte.
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador. |
Nome da VM | De 1 a 63 caracteres.<br/><br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.

## <a name="resource-group-limits"></a>Limites do grupo de recursos

Para entender o número de máquinas virtuais que podem ser protegidas em um único grupo de recursos, consulte o artigo sobre [limites de assinatura e cotas](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits).

## <a name="churn-limits"></a>Limites de churn

A tabela a seguir fornece os limites do Azure Site Recovery.
- Esses limites são baseados em nossos testes, mas não cobrem todas as combinações possíveis de I/O do aplicativo.
- Os resultados reais podem variar dependendo da combinação de E/S do aplicativo.
- Para obter melhores resultados, recomendamos fortemente que você execute a [ferramenta Deployment Planner](site-recovery-deployment-planner.md)e realize testes extensivos de aplicativos usando failovers de teste para obter a imagem de desempenho real para o seu aplicativo.

**Alvo de replicação** | **Tamanho de E/S de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de dados de disco de origem churn por dia**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1684 GB por disco


**Variação de dados de origem** | **Limite Máximo**
---|---
Variação de dados de pico entre todos os discos em uma VM | 54 MB/s
Variação máxima de dados por dia com suporte de um Servidor de Processo | 2 TB

- Esses são números médios, pressupondo uma sobreposição de E/S de 30%.
- O Site Recovery pode lidar com uma maior taxa de transferência com base na taxa de sobreposição, em tamanhos maiores de gravação e em comportamento de E/S de carga de trabalho real.
- Esses números assumem um atraso típico de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados, e um ponto de recuperação é criado dentro de cinco minutos.

## <a name="vault-tasks"></a>Tarefas do Vault

**Ação** | **Com suporte**
--- | ---
Mover cofre entre grupos de recursos | Não
Mova o cofre dentro e através de assinaturas | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos | Não
Mova armazenamento, rede, VMs do Azure dentro e entre as assinaturas. | Não


## <a name="obtain-latest-components"></a>Obter componentes mais recentes

**Nome** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Instalado no local.<br/> Coordena as comunicações entre servidores VMware ou máquinas físicas no local e o Azure. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de configuração.<br/> - [Saiba mais sobre](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a atualização para a versão mais recente.<br/> - [Saiba como](vmware-azure-deploy-configuration-server.md) configurar o servidor de configuração.
Servidor de processo | Instalado por padrão no servidor de configuração.<br/> Recebe dados de replicação, otimiza-os com cache, compactação e criptografia e envia-os para o Azure.<br/> À medida que sua implantação cresce, você pode adicionar servidores de processo adicionais para lidar com volumes maiores de tráfego de replicação. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de processos.<br/> - [Saiba mais sobre](vmware-azure-manage-process-server.md#upgrade-a-process-server) a atualização para a versão mais recente.<br/> - [Saiba como](vmware-physical-large-deployment.md#set-up-a-process-server) configurar servidores de processo de escala.
Serviço de Mobilidade | Instalado em VMware VM ou servidores físicos que você deseja replicar.<br/> Coordena a replicação entre servidores VMware/servidores físicos e Ozure.| - [Conheça](vmware-physical-mobility-service-overview.md) o serviço de Mobilidade.<br/> - [Saiba mais sobre](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) a atualização para a versão mais recente.<br/>



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
