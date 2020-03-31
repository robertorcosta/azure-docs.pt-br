---
title: Automatize serviço de mobilidade para recuperação de desastres de instalação no Azure Site Recovery
description: Como instalar automaticamente o Serviço de Mobilidade para recuperação de desastres do vMware /servidor físico com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252220"
---
# <a name="automate-mobility-service-installation"></a>Instalação do Serviço de Mobilidade Automatizada

Este artigo descreve como automatizar a instalação e as atualizações para o agente do Serviço de Mobilidade no [Azure Site Recovery](site-recovery-overview.md).

Quando você implanta a Recuperação do Site para recuperação de desastres de VMware VMware e servidores físicos no Azure, você instala o agente do Serviço de Mobilidade em cada máquina que deseja replicar. O Serviço de Mobilidade captura os dados gravados na máquina e os encaminha para o servidor de processo de recuperação do site para replicação. Você pode implantar o Serviço de Mobilidade de algumas maneiras:

- **Instalação push**: Deixe o Site Recovery instalar o agente de serviço mobility quando você habilitar a replicação de uma máquina no portal Azure.
- **Instalação manual**: Instale o serviço de mobilidade manualmente em cada máquina. [Saiba mais](vmware-physical-mobility-service-overview.md) sobre a instalação manual e push.
- **Implantação automatizada**: Automatize a instalação com ferramentas de implantação de software, como o Microsoft Endpoint Configuration Manager ou ferramentas de terceiros, como o JetPatch.

A instalação e atualização automatizadas fornece uma solução se:

- Sua organização não permite a instalação push em servidores protegidos.
- A política da sua empresa exige que as senhas sejam alteradas periodicamente. Você tem que especificar uma senha para a instalação push.
- Sua política de segurança não permite adicionar exceções de firewall para máquinas específicas.
- Você está agindo como um provedor de serviços de hospedagem e não quer fornecer credenciais de máquina sustivas que são necessárias para a instalação de push com a Recuperação do Site.
- Você precisa dimensionar instalações de agentes para muitos servidores simultaneamente.
- Você deseja agendar instalações e upgrades durante janelas de manutenção planejadas.

## <a name="prerequisites"></a>Pré-requisitos

Para automatizar a instalação, você precisa dos seguintes itens:

- Uma solução de instalação de software implantada, como [O Gerenciador de Configuração](/configmgr/) ou [jetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Implantação de pré-requisitos em vigor no [Azure](tutorial-prepare-azure.md) e no local para recuperação de desastres vMware ou para recuperação de [desastres](vmware-azure-tutorial-prepare-on-premises.md) [de servidor físico.](physical-azure-disaster-recovery.md) Revise os [requisitos de suporte](vmware-physical-azure-support-matrix.md) para recuperação de desastres.

## <a name="prepare-for-automated-deployment"></a>Prepare-se para implantação automatizada

A tabela a seguir resume ferramentas e processos para automatizar a implantação do Mobility Service.

**Ferramenta** | **Detalhes** | **Instruções**
--- | --- | ---
**Gerenciador de Configurações** | 1. Verifique se você tem os [pré-requisitos](#prerequisites) listados acima. <br/><br/> 2. Implante a recuperação de desastres configurando o ambiente de origem, incluindo o download de um arquivo OVA para implantar o servidor de configuração de recuperação do site como um VMware VMm usando um modelo OVF.<br/><br/> 3. Você registra o servidor de configuração com o serviço de recuperação de site, configura o ambiente Azure de destino e configura uma política de replicação.<br/><br/> 4. Para implantação automatizada do Mobility Service, você cria um compartilhamento de rede contendo a senha do servidor de configuração e os arquivos de instalação do Mobility Service.<br/><br/> 5. Você cria um pacote de Gerenciador de Configuração contendo a instalação ou atualizações e se prepara para a implantação do Mobility Service.<br/><br/> 6. Você pode então habilitar a replicação para o Azure para as máquinas que têm o Serviço de Mobilidade instalado. | [Automatize com o Gerenciador de Configuração](#automate-with-configuration-manager)
**JetPatch** | 1. Verifique se você tem os [pré-requisitos](#prerequisites) listados acima. <br/><br/> 2. Implante a recuperação de desastres configurando o ambiente de origem, incluindo o download e a implantação do JetPatch Agent Manager para a recuperação do site do Azure em seu ambiente de recuperação de site, usando um modelo OVF.<br/><br/> 3. Você registra o servidor de configuração com recuperação do site, configura o ambiente Azure de destino e configura uma política de replicação.<br/><br/> 4. Para implantação automatizada, inicialize e complete a configuração jetpatch agent manager.<br/><br/> 5. No JetPatch, você pode criar uma política de recuperação de site para automatizar a implantação e a atualização do agente do Serviço de Mobilidade. <br/><br/> 6. Você pode então habilitar a replicação para o Azure para as máquinas que têm o Serviço de Mobilidade instalado. | [Automatize com jetpatch agent manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Instalação do agente de solução de problemas no JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatize com o Gerenciador de Configuração

### <a name="prepare-the-installation-files"></a>Prepare os arquivos de instalação

1. Certifique-se de ter os pré-requisitos no lugar.
1. Crie um compartilhamento de arquivos de rede seguro (compartilhamento SMB) que pode ser acessado pela máquina executando o servidor de configuração.
1. No Gerenciador de Configuração, [categorize os servidores](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) nos quais deseja instalar ou atualize o Serviço de Mobilidade. Uma coleção deve conter todos os servidores Windows, o outro todos os servidores Linux.
1. No compartilhamento de rede, crie uma pasta:

   - Para instalação em máquinas Windows, crie uma pasta chamada _MobSvcWindows_.
   - Para instalação em máquinas Linux, crie uma pasta chamada _MobSvcLinux_.

1. Conecte-se ao computador do servidor de configuração.
1. Na máquina do servidor de configuração, abra um prompt de comando administrativo.
1. Para gerar o arquivo de senha, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Copie o arquivo _MobSvc.passphrase_ para a pasta Windows e a pasta Linux.
1. Para navegar até a pasta que contém os arquivos de instalação, execute este comando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Copie esses arquivos de instalação para o compartilhamento de rede:

   - Para windows, copie _o Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ para _o MobSvcWindows_.
   - Para Linux, copie os seguintes arquivos para _MobSvcLinux_:
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Conforme descrito nos procedimentos a seguir, copie o código para as pastas Windows ou Linux. Estamos assumindo que:

   - O endereço IP do `192.168.3.121`servidor de configuração é .
   - O compartilhamento de `\\ContosoSecureFS\MobilityServiceInstallers`arquivos de rede segura é .

### <a name="copy-code-to-the-windows-folder"></a>Copiar código para a pasta do Windows

Copiar o seguinte código:

- Salve o código na pasta _MobSvcWindows_ como _install.bat_.
- Substitua `[CSIP]` os espaços reservados neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script suporta novas instalações do agente do Serviço de Mobilidade e atualizações para agentes que já estão instalados.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Copiar código para a pasta Linux

Copiar o seguinte código:

- Salve o código na pasta _MobSvcLinux_ como _install_linux.sh_.
- Substitua `[CSIP]` os espaços reservados neste script pelos valores reais do endereço IP do seu servidor de configuração.
- O script suporta novas instalações do agente do Serviço de Mobilidade e atualizações para agentes que já estão instalados.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Criar um pacote

1. Faça login no console do Gerenciador de configuração e vá para**os pacotes de****gerenciamento de aplicativos da** >  **biblioteca** > de software .
1. Clique com o botão direito do mouse **Pacotes** > **Criar pacote**.
1. Forneça detalhes do pacote, incluindo nome, descrição, fabricante, idioma e versão.
1. Selecione **Este pacote contém arquivos de origem**.
1. Clique **em Procurar**e selecione o compartilhamento de rede e a pasta que contém o instalador relevante _(MobSvcWindows_ ou _MobSvcLinux)._ Em seguida, selecione **Next**.

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Em **Escolher o tipo de programa que deseja criar** página, selecione Programa **Padrão** > **Seguinte**.

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Em **Especificar informações sobre esta** página padrão do programa, especifique os seguintes valores:

    **Parâmetro** | **Valor do Windows** | **Valor linux**
    --- | --- | ---
    **Nome** | Instalar o Serviço de Mobilidade do Microsoft Azure (Windows) | Instale o Microsoft Azure Mobility Service (Linux).
    **Linha de comando** | install.bat | ./install_linux.sh
    **Programa pode ser executado** | Se um usuário fez logon ou não | Se um usuário fez logon ou não
    **Outros parâmetros** | Usar a configuração padrão | Usar a configuração padrão

   ![Captura de tela do Assistente para Criar Pacote e Programa](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Em **Especificar os requisitos para este programa padrão,** faça as seguintes tarefas:

   - Para máquinas Windows, selecione **Este programa pode ser executado apenas em plataformas especificadas**. Em seguida, selecione os [sistemas operacionais Windows suportados](vmware-physical-azure-support-matrix.md#replicated-machines) e selecione **Next**.
   - Para máquinas Linux, selecione **Este programa pode ser executado em qualquer plataforma**. Em seguida, selecione **Next**.

1. Conclua o assistente.

### <a name="deploy-the-package"></a>Implantar o pacote

1. No console Gerenciador de configuração, clique com o botão direito do mouse no pacote e **selecione Distribuir conteúdo**.

   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selecione os pontos de distribuição para os quais os pacotes devem ser copiados. [Saiba mais](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Conclua o assistente. Em seguida, o pacote começa a ser replicado para os pontos de distribuição especificados.
1. Após o término da distribuição do pacote, clique com o botão direito do mouse no pacote > **Deploy**.

   ![Captura de tela do console do Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selecione a coleção de dispositivos Windows ou Linux que você criou anteriormente.
1. Na **página Especificar a** página de destino de conteúdo, selecione **Pontos de distribuição**.
1. Em **Especificar configurações para controlar como este software é implantado,** defina **Propósito** **como Obrigatório**.

   ![Captura de tela do Assistente de Implantação de Software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. **Em Especificar o cronograma para esta implantação,** configure um cronograma. [Saiba mais](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - O Serviço de Mobilidade é instalado de acordo com o cronograma especificado.
   - Para evitar reinicializações desnecessárias, agende a instalação do pacote durante a janela de manutenção mensal ou a janela de atualizações de software.

1. Na página **Pontos de distribuição,** configure as configurações e termine o assistente.
1. Monitore o progresso da implantação no console do Gerenciador de configuração. Vá para **o Monitoramento** > **de Implantações** > _\<seu nome\>de pacote_.

### <a name="uninstall-the-mobility-service"></a>Desinstale o Serviço de Mobilidade

Você pode criar pacotes de Gerenciador de configuração para desinstalar o Serviço de Mobilidade. Por exemplo, o script a seguir desinstala o Serviço de Mobilidade:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Próximas etapas

[Habilite a replicação](vmware-azure-enable-replication.md) de VMs.
