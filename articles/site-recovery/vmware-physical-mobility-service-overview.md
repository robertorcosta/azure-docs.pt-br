---
title: Sobre o serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos com Azure Site Recovery | Microsoft Docs
description: Conheça o agente de serviço de mobilidade para recuperação de desastres de VMware VMs e servidores físicos para o Azure usando o serviço de recuperação do site Do Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259775"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Sobre o serviço de Mobilidade para VMs do VMware e servidores físicos

Ao configurar a recuperação de desastres para máquinas virtuais VMware (VM) e servidores físicos usando [o Azure Site Recovery,](site-recovery-overview.md)você instala o serviço de mobilidade de recuperação do site em cada VMware vm e servidor físico no local. O serviço Mobility captura gravações de dados na máquina e as encaminha para o servidor do processo de Recuperação do Site. O serviço de mobilidade é instalado pelo software do agente de serviço mobility que você pode implantar usando os seguintes métodos:

- [Instalação push](#push-installation): Quando a proteção é ativada através do portal Azure, a Recuperação do Site instala o serviço de mobilidade no servidor.
- Instalação manual: Você pode instalar o serviço de mobilidade manualmente em cada máquina através da interface do [usuário (UI)](#install-the-mobility-service-using-ui) ou [do prompt de comando](#install-the-mobility-service-using-command-prompt).
- [Implantação automatizada](vmware-azure-mobility-install-configuration-mgr.md): Você pode automatizar a instalação do serviço mobility com ferramentas de implantação de software, como o Gerenciador de Configuração.

> [!NOTE]
> O serviço Mobility usa aproximadamente 6%-10% de memória em máquinas de origem para VMware VMware ou máquinas físicas.

## <a name="antivirus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que você deseja replicar estiverem executando o software antivírus, exclua a pasta de instalação do serviço Demobility _C:\ProgramData\ASR\agent_ de operações antivírus. Essa exclusão garante que a replicação funcione como esperado.

## <a name="push-installation"></a>Instalação de push

A instalação push é parte integrante do trabalho executado a partir do portal Azure para permitir a [replicação.](vmware-azure-enable-replication.md#enable-replication) Depois de escolher o conjunto de VMs que deseja proteger e habilitar a replicação, o servidor de configuração empurra o agente de serviço Mobility para os servidores, instala o agente e completa o registro do agente com o servidor de configuração.

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todos os [pré-requisitos de](vmware-azure-install-mobility-service.md) instalação de push sejam atendidos.
- Certifique-se de que todas as configurações do servidor atendam aos critérios da [matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).

O fluxo de trabalho de instalação de empurrar é descrito nas seguintes seções:

### <a name="mobility-service-agent-version-923-and-higher"></a>Agente de serviço de mobilidade versão 9.23 e superior

Para obter mais informações sobre a versão 9.23, consulte [Update Rollup 35 for Azure Site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery).

Durante a instalação do serviço de mobilidade, são realizadas as seguintes etapas:

1. O agente é empurrado para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a vários erros ambientais. Visite [nossa orientação](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação de impulso.
1. Depois que o agente é copiado com sucesso para o servidor, uma verificação pré-requisito é realizada no servidor.
   - Se todos os pré-requisitos forem atendidos, a instalação começa.
   - A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos.
1. Como parte da instalação do agente, o provedor DE VSS (Volume Shadow Copy Service, serviço de cópia de sombra de volume) para recuperação do site do Azure é instalado. O provedor VSS é usado para gerar pontos de recuperação consistentes com aplicativos. Se a instalação do provedor VSS falhar, esta etapa será ignorada e a instalação do agente continuará.
1. Se a instalação do agente for bem-sucedida, mas a instalação do provedor VSS falhar, o status do trabalho será marcado como **Aviso**. Isso não afeta a geração de pontos de recuperação consistente com acidentes.

    - Para gerar pontos de recuperação consistentes com aplicativos, consulte [nossa orientação](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) para concluir uma instalação manual do provedor DE RECUPERAÇÃO de Site VSS.
    - Se você não quiser gerar pontos de recuperação consistentes com aplicativos, [modifique a política](vmware-azure-set-up-replication.md#create-a-policy) de replicação para desativar pontos de recuperação consistentes com aplicativos.

### <a name="mobility-service-agent-version-922-and-below"></a>Agente de serviço de mobilidade versão 9.22 e abaixo

1. O agente é empurrado para a máquina de origem. Copiar o agente para a máquina de origem pode falhar devido a vários erros ambientais. Consulte [nossa orientação](vmware-azure-troubleshoot-push-install.md) para solucionar falhas de instalação de impulso.
1. Depois que o agente é copiado com sucesso para o servidor, uma verificação pré-requisito é realizada no servidor.
   - Se todos os pré-requisitos forem atendidos, a instalação começa.
   - A instalação falha se um ou mais dos [pré-requisitos](vmware-physical-azure-support-matrix.md) não forem atendidos.

1. Como parte da instalação do agente, o provedor DE VSS (Volume Shadow Copy Service, serviço de cópia de sombra de volume) para recuperação do site do Azure é instalado. O provedor VSS é usado para gerar pontos de recuperação consistentes com aplicativos.
   - Se a instalação do provedor VSS falhar, a instalação do agente falhará. Para evitar a falha na instalação do agente, use a [versão 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) ou superior para gerar pontos de recuperação consistentes com falhas e faça uma instalação manual do provedor VSS.

## <a name="install-the-mobility-service-using-ui"></a>Instale o serviço de mobilidade usando ui

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todas as configurações do servidor atendam aos critérios da [matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) do sistema operacional do servidor.

>[!IMPORTANT]
> Não use o método de instalação da UI se estiver replicando uma VM Azure Infrastructure as a Service (IaaS) de uma região Azure para outra. Use a instalação [do prompt de comando.](#install-the-mobility-service-using-command-prompt)

1. Copie o arquivo de instalação para a máquina e execute-o.
1. Na **Opção de Instalação**, selecione **Instalar o serviço de mobilidade**.
1. Escolha o local de instalação e selecione **Instalar**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Página da opção de instalação do serviço de mobilidade.":::

1. Monitore a instalação em **Progresso da Instalação**. Após a conclusão da instalação, selecione **Prossiga para a Configuração** para registrar o serviço no servidor de configuração.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Página de registro do serviço de mobilidade.":::

1. Em **Detalhes do servidor de configuração,** especifique o endereço IP e a senha que você configurou.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Página de registro do serviço de mobilidade.":::

1. Selecione **Registrar** para concluir o registro.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Página final de registro do serviço de mobilidade.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>Instale o serviço de mobilidade usando o prompt de comando

### <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que todas as configurações do servidor atendam aos critérios da [matriz de suporte para recuperação de desastres de VMware VMs e servidores físicos para o Azure](vmware-physical-azure-support-matrix.md).
- [Localize o instalador](#locate-installer-files) do sistema operacional do servidor.

### <a name="windows-machine"></a>Máquina de windows

- A partir de um prompt de comando, execute os seguintes comandos para copiar o instalador para uma pasta local, como _C:\Temp_, no servidor que você deseja proteger. Substitua o nome do arquivo do instalador pelo nome real do arquivo.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Execute este comando para instalar o agente.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Execute esses comandos para registrar o agente no servidor de configuração.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Configurações de instalação

Configuração | Detalhes
--- | ---
Sintaxe | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Logs de instalação | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
`/InstallLocation`| Parâmetro opcional. Especifica o local de instalação do serviço Mobility (qualquer pasta).
`/Platform` | Mandatory. Especifica a plataforma na qual o serviço de mobilidade está instalado: <br/> **VMware** para VMware VMs/servidores físicos. <br/> **Azure** para VMs Azure.<br/><br/> Se você estiver tratando as VMs do Azure como máquinas físicas, especifique **o VMware**.
`/Silent`| Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro

Configuração | Detalhes
--- | ---
Sintaxe | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Logs de configuração do agente | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Parâmetro obrigatório. `<CSIP>`especifica o endereço IP do servidor de configuração. Use qualquer endereço de IP válido.
`/PassphraseFilePath` |  Mandatory. Local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="linux-machine"></a>Máquina Linux

1. A partir de uma sessão de terminal, copie o instalador para uma pasta local, como _/tmp_ no servidor que você deseja proteger. Substitua o nome do arquivo do instalador pelo nome real do arquivo da sua distribuição Linux e execute os comandos.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Instale da seguinte maneira:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Após o término da instalação, o serviço de mobilidade deve ser registrado no servidor de configuração. Execute o seguinte comando para registrar o serviço de mobilidade com o servidor de configuração.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Configurações de instalação

Configuração | Detalhes
--- | ---
Sintaxe | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Parâmetro de instalação obrigatório. Especifica se o serviço Mobility (MS) ou o destino mestre (MT) deve ser instalado.
`-d` | Parâmetro opcional. Especifica o local de `/usr/local/ASR`instalação do serviço de mobilidade: .
`-v` | Mandatory. Especifica a plataforma na qual o serviço de mobilidade está instalado. <br/> **VMware** para VMware VMs/servidores físicos. <br/> **Azure** para VMs Azure.
`-q` | Opcional. Especifica se deve executar o instalador no modo silencioso.

#### <a name="registration-settings"></a>Configurações de registro

Configuração | Detalhes
--- | ---
Sintaxe | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Parâmetro obrigatório. `<CSIP>`especifica o endereço IP do servidor de configuração. Use qualquer endereço de IP válido.
`-P` |  Mandatory. Caminho de arquivo completo do arquivo no qual a frase secreta é salvo. Use qualquer pasta válida.

## <a name="azure-virtual-machine-agent"></a>Agente de Máquina Virtual do Azure

- **VMs do Windows**: a partir da versão 9.7.0.0 do serviço de mobilidade, o [agente de VM do Azure](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) é instalado pelo instalador do serviço de mobilidade. Isso garante que, quando a máquina falhar no Azure, a VM do Azure atende ao pré-requisito de instalação do agente para o uso de qualquer extensão vm.
- **VMs do Linux**: O [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) deve ser instalado manualmente na VM do Azure após o failover.

## <a name="locate-installer-files"></a>Localizar arquivos do instalador

No servidor de configuração vá para a pasta _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório_. Verifique qual instalador você precisa com base no sistema operacional. A tabela a seguir resume os arquivos do instalador para cada VMware VMe e sistema operacional de servidor físico. Antes de começar, você pode rever os [sistemas operacionais suportados.](vmware-physical-azure-support-matrix.md#replicated-machines)

> [!NOTE]
> Os nomes dos arquivos usam a sintaxe mostrada na tabela a seguir com _versão_ e _data_ como espaços reservados para os valores reais. Os nomes de arquivo reais serão semelhantes a esses exemplos:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Arquivo de instalador | Sistema operacional (somente 64 bits)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inclui SP2 e SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Servidor do Ubuntu Linux 16.04 LTS
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Próximas etapas

[Configurar instalação do Serviço de Mobilidade por push](vmware-azure-install-mobility-service.md).
