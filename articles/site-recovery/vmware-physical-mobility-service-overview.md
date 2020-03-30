---
title: Sobre o Serviço de Mobilidade para recuperação de desastre de VMs VMware e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre o agente do Serviço de Mobilidade para recuperação de desastre de VMs do VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256829"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMs do VMware e servidores físicos

Quando você configura a recuperação de desastre para VMs VMware e servidores físicos usando [Azure Site Recovery](site-recovery-overview.md), instale o Serviço Mobilidade de Recuperação do Site em cada VM VMware local e servidor físico.  O serviço Mobility captura gravações de dados na máquina e as encaminha para o servidor do processo de Recuperação do Site. Você pode implantar o Serviço de Mobilidade usando os seguintes métodos:

- [Instalação push](#push-installation): A Recuperação do Site instala o agente de mobilidade no servidor quando a proteção é ativada através do portal Azure.
- Instale manualmente: Você pode instalar o serviço de mobilidade manualmente em cada máquina através [de ui](#install-mobility-agent-through-ui) ou [prompt de comando](#install-mobility-agent-through-command-prompt).
- [Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Você pode automatizar a instalação com ferramentas de implantação de software, como o Gerenciador de Configuração.

> [!NOTE]
> O agente Mobility usa aproximadamente 6%-10% da memória em máquinas de origem para VMware VMware ou máquinas físicas.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em computadores replicados

Se as máquinas que você deseja replicar tiverem o software antivírus ativo em execução, exclua a pasta de instalação do serviço Mobility das operações de antivírus (*C:\ProgramData\ASR\agent*). Isso garante que a replicação funciona conforme o esperado.

## <a name="push-installation"></a>Instalação de push

A instalação push é parte integrante do trabalho "[Enable Replication](vmware-azure-enable-replication.md#enable-replication)" acionado no portal. Depois de escolher o conjunto de máquinas virtuais que deseja proteger e acionar "Ativar replicação", o servidor de configuração empurra o agente de mobilidade para os servidores, instala o agente e completa o registro do agente com o servidor de configuração. Para a conclusão bem sucedida desta operação,

- Certifique-se de que todos os [pré-requisitos de](vmware-azure-install-mobility-service.md) instalação de push sejam atendidos.
- Certifique-se de que todas as configurações dos servidores estão [a matriz de suporte do vmware para o cenário DR do Azure](vmware-physical-azure-support-matrix.md).

Os detalhes do fluxo de trabalho de instalação push foram descritos nas seções a seguir.

### <a name="from-923-version-onwards"></a>A partir de [9.23 versão](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) em diante

Durante a instalação push do agente de mobilidade, as seguintes etapas são realizadas

1. Empurra o agente para a máquina de origem. Copiar o agente na máquina de origem pode falhar devido a vários erros ambientais. Visite [nossa orientação](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação de impulso.
2. Depois que o agente é copiado com sucesso para o servidor, as verificações pré-requisitos são realizadas no servidor. A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos. Se todos os pré-requisitos forem atendidos, a instalação será acionada.
3. O provedor Azure Site Recovery VSS é instalado no servidor como parte da instalação do agente mobility. Este provedor é usado para gerar pontos consistentes do Aplicativo. Se a instalação do provedor VSS falhar, esta etapa será ignorada e a instalação do agente continuará.
4. Se a instalação do agente for bem-sucedida, mas a instalação do provedor VSS falhar, o status do trabalho será marcado como "Aviso". Isso não afeta a geração de pontos de consistência de colisão.

    a. Para gerar pontos consistentes de aplicação, consulte [nossa orientação](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para concluir a instalação do provedor DE RECUPERAÇÃO de Site VSS manualmente. </br>
    b.  Se você não desejar que pontos consistentes do aplicativo sejam gerados, [modifique a política de replicação](vmware-azure-set-up-replication.md#create-a-policy) para desativar pontos consistentes do aplicativo.

### <a name="before-922-versions"></a>Antes das versões 9.22

1. Empurra o agente para a máquina de origem. Copiar o agente na máquina de origem pode falhar devido a vários erros ambientais. Visite [nossa orientação](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação de impulso.
2. Depois que o agente é copiado com sucesso para o servidor, as verificações pré-requisitos são realizadas no servidor. A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos. Se todos os pré-requisitos forem atendidos, a instalação será acionada.
3. O provedor Azure Site Recovery VSS é instalado no servidor como parte da instalação do agente mobility. Este provedor é usado para gerar pontos consistentes do Aplicativo. Se a instalação do provedor VSS falhar, a instalação do agente falhará. Para evitar falhas na instalação do agente de mobilidade, use a [versão 9.23](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos consistentes de falha e instalar o provedor VSS manualmente.

## <a name="install-mobility-agent-through-ui"></a>Instale o agente de mobilidade através da UI

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações dos servidores estão [a matriz de suporte do vmware para o cenário DR do Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operacional do servidor.

>[!IMPORTANT]
> Se você estiver replicando o Azure IaaS VM de uma região azure para outra, não use este método. Use o método de instalação baseado em linha de comando.

1. Copie o arquivo de instalação para a máquina e execute-o.
2. Na **Opção de Instalação**, selecione **Instalar o serviço de mobilidade**.
3. Selecione o local de instalação > **instalar**.

    ![Página de opção de instalação do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Monitore a instalação em **Progresso da Instalação**. Após a conclusão da instalação, selecione **Prossiga para a Configuração** para registrar o serviço no servidor de configuração.

    ![Página de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Em **Detalhes do servidor de configuração,** especifique o endereço IP e a senha que você configurou.

    ![Página de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecione **Registrar** para concluir o registro.

    ![Página final de registro do Serviço de Mobilidade](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Instale o agente de mobilidade através do prompt de comando

### <a name="prerequisite"></a>Pré-requisito

- Certifique-se de que todas as configurações dos servidores estão [a matriz de suporte do vmware para o cenário DR do Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) com base no sistema operacional do servidor.

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

- Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Instale da seguinte maneira:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registre o agente no servidor de configuração.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgent.exe /Role \<MS/MT> \</InstallLocation Install Location> /Platform "VmWare" /Silent
Logs de instalação | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
/InstallLocation| Parâmetro opcional. Especifica o local de instalação do serviço Mobility (qualquer pasta).
/Platform | Mandatory. Especifica a plataforma onde o Serviço de Mobilidade está instalado. **VMware** para VMs do Mware/servidores físicos; **Azure** para VMs do Azure.<br/><br/> Se você estiver tratando as VMs do Azure como máquinas físicas, especifique **o VMware**.
/Silent| Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Configuração** | **Detalhes**
--- | ---
Uso | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath \<SenhaPhraseFilePath>
Logs de configuração do agente | Sob %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
/PassphraseFilePath |  Mandatory. Local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="on-a-linux-machine"></a>Em um computador Linux

1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Instale da seguinte maneira:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Após concluir a instalação, o Serviço de Mobilidade precisa ser registrado no servidor de configuração. Execute o seguinte comando para registrar o Serviço de Mobilidade com o servidor de configuração:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Configurações de instalação
**Configuração** | **Detalhes**
--- | ---
Uso | ./install -d \<Instalar local \<> -r MS/MT> -v VmWare -q
-r | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
-d | Parâmetro opcional. Especifica o local de instalação do serviço Mobility: / usr / local / ASR.
-v | Mandatory. Especifica a plataforma onde o Serviço de Mobilidade está instalado. **VMware** para VMs do Mware/servidores físicos; **Azure** para VMs do Azure.
-Q | Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro
**Configuração** | **Detalhes**
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP \<> -P PassphraseFilePath>
-i | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
-P |  Mandatory. Caminho de arquivo completo do arquivo no qual a frase secreta é salvo. Use qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: a partir da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isso garante que, quando um computador fizer failover no Azure, a VM do Azure atenderá ao pré-requisito da instalação do agente para usar qualquer extensão de VM.
- **VMs do Linux**: O [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente na VM do Azure após o failover.

## <a name="locate-installer-files"></a>Localizar arquivos do instalador

Vá para %ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório folder on configuration server. Verifique qual instalador você precisa com base no sistema operacional. A tabela a seguir resume os arquivos do instalador para cada VMware VMe e sistema operacional de servidor físico. Você pode revisar os [sistemas operacionais compatíveis](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.

**Arquivo de instalador** | **Sistema operacional (somente 64 bits)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Servidor do Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Próximas etapas

[Configurar instalação do Serviço de Mobilidade por push](vmware-azure-install-mobility-service.md).
