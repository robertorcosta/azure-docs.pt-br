---
title: Gerenciamento do azure Arc para servidor (visualização) agente
description: Este artigo descreve as diferentes tarefas de gerenciamento que você normalmente executará durante o ciclo de vida do Azure Arc para servidores conectado agente de máquina.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 758e6123fd09df1e3f8b2e883a729b9fec4328d1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367285"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gerenciamento e manutenção do agente de máquina conectada

Após a implantação inicial do Azure Arc para servidores (visualização) agente de máquina conectada para Windows ou Linux, você pode precisar reconfigurar o agente, atualizá-lo ou removê-lo do computador se ele tiver atingido a fase de aposentadoria em seu ciclo de vida. Você pode gerenciar facilmente essas tarefas de manutenção de rotina, manualmente ou por meio de automação, o que reduz o erro operacional e as despesas.

## <a name="upgrading-agent"></a>Agente de atualização

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo de suas necessidades. A tabela a seguir descreve os métodos suportados para realizar a atualização do agente.

| Sistema operacional | Método de atualização |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agente do Windows

Para atualizar o agente em uma máquina Windows para a versão mais recente, o agente está disponível no Microsoft Update e pode ser implantado usando o processo de gerenciamento de atualizações de software existente. Ele também pode ser executado manualmente a partir do Prompt de comando, a partir `AzureConnectedMachine.msi`de um script ou outra solução de automação, ou do assistente de interface do usuário executando . 

> [!NOTE]
> * Para atualizar o agente, você deve ter permissões *do administrador.*
> * Para atualizar manualmente, você deve primeiro baixar e copiar o pacote Installer para uma pasta no servidor de destino ou de uma pasta de rede compartilhada. 

Se você não estiver familiarizado com as opções de linha de comando para pacotes do Windows Installer, [revise as opções padrão de linha de comando msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e [as opções de linha de comando Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o Assistente de configuração

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **AzureConnectedMachineAgent.msi** para iniciar o assistente de configuração.

O Assistente de configuração descobre se existe uma versão anterior e, em seguida, executa automaticamente uma atualização do agente. Quando a atualização é concluída, o Assistente de configuração fecha automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para atualizar o agente silenciosamente e criar um `C:\Support\Logs` arquivo de log de configuração na pasta, execute o seguinte comando.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente do Linux

Para atualizar o agente em uma máquina Linux para a versão mais recente, envolve dois comandos. Um comando para atualizar o índice de pacotelocal com a lista dos pacotes disponíveis mais recentes dos repositórios e um comando para atualizar o pacote local. 

#### <a name="upgrade-ubuntu"></a>Atualizar o Ubuntu

1. Para atualizar o índice de pacote local com as últimas alterações feitas nos repositórios, execute o seguinte comando:

    ```bash
    apt update
    ```

2. Para atualizar seu sistema, execute o seguinte comando:

    ```bash
    apt upgrade
    ```

Ações do comando [apt,](https://help.ubuntu.com/lts/serverguide/apt.html) como instalação e remoção de `/var/log/dpkg.log` pacotes, estão registradas no arquivo de log.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Atualizar Red Hat/CentOS/Amazon Linux

1. Para atualizar o índice de pacote local com as últimas alterações feitas nos repositórios, execute o seguinte comando:

    ```bash
    yum check-update
    ```

2. Para atualizar seu sistema, execute o seguinte comando:

    ```bash
    yum update
    ```

Ações do comando [yum,](https://access.redhat.com/articles/yum-cheat-sheet) como instalação e remoção de `/var/log/yum.log` pacotes, estão registradas no arquivo de log. 

#### <a name="upgrade-suse-linux-enterprise"></a>Atualizar SUSE Linux Enterprise

1. Para atualizar o índice de pacote local com as últimas alterações feitas nos repositórios, execute o seguinte comando:

    ```bash
    zypper refresh
    ```

2. Para atualizar seu sistema, execute o seguinte comando:

    ```bash
    zypper update
    ```

Ações do comando [zypper,](https://en.opensuse.org/Portal:Zypper) como instalação e remoção de `/var/log/zypper.log` pacotes, estão registradas no arquivo de log. 

## <a name="remove-the-agent"></a>Remova o agente

Use um dos seguintes procedimentos para desinstalar o agente Windows ou Linux usando o assistente de linha de comando ou configuração descrito nesta seção. Antes de desinstalar o agente, primeiro desconecte a máquina do Azure Arc para servidores (visualização) completando estas etapas: 

1. Abra o Azure Arc para servidores (versão prévia) acessando o [portal do Azure](https://aka.ms/hybridmachineportal).

2. Selecione o computador na lista, selecione as reticências (**...**) e, em seguida, selecione **Excluir**.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar usando o painel de controle

1. Para desinstalar o agente do Windows do computador, faça o seguinte:

    a. Entre no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controle**, selecione **Programas e Recursos**.  
    c. Em **Programas e Recursos**, selecione **Agente do Azure Connected Machine**, **Desinstalar** e, em seguida, **Sim**.  

    >[!NOTE]
    > Execute também o assistente de instalação do agente clicando duas vezes no pacote do instalador **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar usando a linha de comando

Para desinstalar o agente manualmente a partir do Prompt de comando ou para usar um método automatizado, como um script, você pode usar o seguinte exemplo. Primeiro você precisa recuperar o código do produto, que é um GUID que é o principal identificador do pacote de aplicativos, do sistema operacional. A desinstalação é realizada usando a linha de `msiexec /x {Product Code}`comando Msiexec.exe - .
    
1. Abra o Editor do Registro.

2. Na chave do Registro `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, procure e copie o GUID do código do produto.

3. Em seguida, você pode desinstalar o agente usando o Msiexec usando os seguintes exemplos:

   * Do tipo de linha de comando:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Você pode executar as mesmas etapas usando o PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agente do Linux

Para desinstalar o agente Linux, o comando a ser usado depende do sistema operacional Linux.

- Para o Ubuntu, execute o seguinte comando:

    ```bash
    sudo apt purge azcmagent
    ```

- Para RHEL, CentOS e Amazon Linux, execute o seguinte comando:

    ```bash
    sudo yum remove azcmagent
    ```

- Para SLES, execute o seguinte comando:

    ```bash
    sudo zypper remove azcmagent
    ```
