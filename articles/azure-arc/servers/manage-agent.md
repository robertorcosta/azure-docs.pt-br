---
title: Gerenciando o Azure ARC para servidores (versão prévia) Agent
description: Este artigo descreve as diferentes tarefas de gerenciamento que normalmente serão executadas durante o ciclo de vida do Azure ARC para servidores conectados ao agente do computador.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81308965"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gerenciando e mantendo o agente do computador conectado

Após a implantação inicial do Azure ARC para servidores (visualização) agente de máquina conectado para Windows ou Linux, talvez seja necessário reconfigurar o agente, atualizá-lo ou removê-lo do computador se ele tiver atingido o estágio de aposentadoria em seu ciclo de vida. Você pode gerenciar facilmente essas tarefas de manutenção de rotina, manualmente ou por meio de automação, o que reduz o erro operacional e as despesas.

## <a name="upgrading-agent"></a>Atualizando agente

O agente do Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manual ou automaticamente dependendo de suas necessidades. A tabela a seguir descreve os métodos com suporte para executar a atualização do agente.

| Sistema operacional | Método de atualização |
|------------------|----------------|
| Windows | Manualmente<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agente do Windows

Para atualizar o agente em um computador Windows para a versão mais recente, o agente está disponível na Microsoft Update e pode ser implantado usando o processo de gerenciamento de atualizações de software existente. Ele também pode ser executado manualmente do prompt de comando, de um script ou de outra solução de automação, ou do assistente de interface `AzureConnectedMachine.msi`do usuário executando. 

> [!NOTE]
> * Para atualizar o agente, você deve ter permissões de *administrador* .
> * Para atualizar manualmente, primeiro você deve baixar e copiar o pacote do instalador para uma pasta no servidor de destino ou de uma pasta de rede compartilhada. 

Se você não estiver familiarizado com as opções de linha de comando para pacotes Windows Installer, examine as opções de [linha de comando MSIExec padrão](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) e [Opções de linha de comando msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Para atualizar usando o assistente de instalação

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **AzureConnectedMachineAgent. msi** para iniciar o assistente de instalação.

O assistente de instalação descobre se existe uma versão anterior e executa automaticamente uma atualização do agente. Quando a atualização for concluída, o assistente de instalação será fechado automaticamente.

#### <a name="to-upgrade-from-the-command-line"></a>Para atualizar a partir da linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Para atualizar o agente silenciosamente e criar um arquivo de log da instalação `C:\Support\Logs` na pasta, execute o comando a seguir.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agente do Linux

Para atualizar o agente em um computador Linux para a versão mais recente, ele envolve dois comandos. Um comando para atualizar o índice do pacote local com a lista de pacotes disponíveis mais recentes dos repositórios e um comando para atualizar o pacote local. 

> [!NOTE]
> Para atualizar o agente, você deve ter permissões de acesso *raiz* ou uma conta que tenha direitos elevados usando o sudo.

#### <a name="upgrade-ubuntu"></a>Atualizar o Ubuntu

1. Para atualizar o índice do pacote local com as alterações mais recentes feitas nos repositórios, execute o seguinte comando:

    ```bash
    apt update
    ```

2. Para atualizar o sistema, execute o seguinte comando:

    ```bash
    apt upgrade
    ```

As ações do comando [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , como instalação e remoção de pacotes, são registradas no arquivo `/var/log/dpkg.log` de log.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Atualização do Red Hat/CentOS/Amazon Linux

1. Para atualizar o índice do pacote local com as alterações mais recentes feitas nos repositórios, execute o seguinte comando:

    ```bash
    yum check-update
    ```

2. Para atualizar o sistema, execute o seguinte comando:

    ```bash
    yum update
    ```

As ações do comando [yum](https://access.redhat.com/articles/yum-cheat-sheet) , como instalação e remoção de pacotes, são registradas no arquivo `/var/log/yum.log` de log. 

#### <a name="upgrade-suse-linux-enterprise"></a>Atualizar o SUSE Linux Enterprise

1. Para atualizar o índice do pacote local com as alterações mais recentes feitas nos repositórios, execute o seguinte comando:

    ```bash
    zypper refresh
    ```

2. Para atualizar o sistema, execute o seguinte comando:

    ```bash
    zypper update
    ```

As ações do comando [zypper](https://en.opensuse.org/Portal:Zypper) , como instalação e remoção de pacotes, são registradas no arquivo `/var/log/zypper.log` de log. 

## <a name="about-the-azcmagent-tool"></a>Sobre a ferramenta Azcmagent

A ferramenta Azcmagent (Azcmagent. exe) é usada para configurar o Azure ARC para servidores (versão prévia) conectada ao agente do computador durante a instalação ou modificar a configuração inicial do agente após a instalação. O Azcmagent. exe fornece parâmetros de linha de comando para personalizar o agente e exibir seu status:

* **Conectar** – para conectar o computador ao arco do Azure

* **Desconectar** – para desconectar o computador do arco do Azure

* **Reconectar** -para reconectar uma máquina desconectada ao arco do Azure

* **Show** -View status do agente e suas propriedades de configuração (nome do grupo de recursos, ID da assinatura, versão, etc.), que podem ajudar ao solucionar um problema com o agente.

* **-h ou--help** -mostra os parâmetros de linha de comando disponíveis

    Por exemplo, para ver a ajuda detalhada para o parâmetro **reconnect** , `azcmagent reconnect -h`digite. 

* **-v ou--Verbose** -habilitar log detalhado

Você pode executar uma **conexão**, **Desconectar**e **reconectar-** se manualmente enquanto estiver conectado interativamente, ou automatizar usando a mesma entidade de serviço usada para carregar vários agentes ou com um token de [acesso](../../active-directory/develop/access-tokens.md)da plataforma de identidade da Microsoft. Se você não usou uma entidade de serviço para registrar a máquina com o Azure ARC para servidores (versão prévia), consulte o [artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a seguir para criar uma entidade de serviço.

### <a name="connect"></a>Conectar

Esse parâmetro especifica um recurso em Azure Resource Manager que representa o computador é criado no Azure. O recurso está na assinatura e no grupo de recursos especificado, e os dados sobre a máquina são armazenados na região do Azure especificada `--location` pela configuração. O nome do recurso padrão será o nome do host deste computador, se não for especificado.

Um certificado correspondente à identidade atribuída pelo sistema da máquina é então baixado e armazenado localmente. Depois que essa etapa for concluída, o serviço de metadados do computador conectado do Azure e o agente de configuração do convidado começarão a sincronizar com o Azure ARC para servidores (versão prévia).

Para se conectar usando uma entidade de serviço, execute o seguinte comando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar usando um token de acesso, execute o seguinte comando:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar com suas credenciais de logon elevado (interativo), execute o seguinte comando:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Desconectar

Esse parâmetro especifica um recurso em Azure Resource Manager que representa o computador é excluído no Azure. Ele não exclui o agente do computador, isso deve ser feito como uma etapa separada. Depois que o computador for desconectado, se você quiser registrá-lo novamente com o Azure ARC para servidores (versão `azcmagent connect` prévia), use para que um novo recurso seja criado para ele no Azure.

Para se desconectar usando uma entidade de serviço, execute o seguinte comando:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para se desconectar usando um token de acesso, execute o seguinte comando:

`azcmagent disconnect --access-token <accessToken>`

Para se desconectar com suas credenciais de logon elevado (interativo), execute o seguinte comando:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Reconectar

Esse parâmetro reconecta o computador já registrado ou conectado ao Azure ARC para servidores (versão prévia). Isso pode ser necessário se o computador tiver sido desligado, pelo menos 45 dias, para que seu certificado expire. Esse parâmetro usa as opções de autenticação fornecidas para recuperar novas credenciais correspondentes ao recurso de Azure Resource Manager que representa esse computador.

Este comando requer privilégios mais altos do que a função de [integração do computador conectado do Azure](overview.md#required-permissions) .

Para se reconectar usando uma entidade de serviço, execute o seguinte comando:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para reconectar-se usando um token de acesso, execute o seguinte comando:

`azcmagent reconnect --access-token <accessToken>`

Para se reconectar com suas credenciais de logon elevado (interativo), execute o seguinte comando:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Remova o agente

Execute um dos métodos a seguir para desinstalar o agente de computador conectado do Windows ou Linux do computador. Remover o agente não registra o computador com Arc para servidores (visualização), esse é um processo separado que você executa quando não precisa mais gerenciar o computador no Azure.

### <a name="windows-agent"></a>Agente do Windows

Os dois métodos a seguir removem o agente, mas não removem a pasta *C:\Program Files\AzureConnectedMachineAgent* no computador.

#### <a name="uninstall-from-control-panel"></a>Desinstalar usando o painel de controle

1. Para desinstalar o agente do Windows do computador, faça o seguinte:

    a. Entre no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controle**, selecione **Programas e Recursos**.  
    c. Em **Programas e Recursos**, selecione **Agente do Azure Connected Machine**, **Desinstalar** e, em seguida, **Sim**.  

    >[!NOTE]
    > Execute também o assistente de instalação do agente clicando duas vezes no pacote do instalador **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar usando a linha de comando

Para desinstalar o agente manualmente do prompt de comando ou usar um método automatizado, como um script, você pode usar o exemplo a seguir. Primeiro, você precisa recuperar o código do produto, que é um GUID que é o identificador principal do pacote de aplicativos do sistema operacional. A desinstalação é executada usando a linha de comando msiexec. exe `msiexec /x {Product Code}`-.
    
1. Abra o Editor do Registro.

2. Na chave do Registro `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, procure e copie o GUID do código do produto.

3. Em seguida, você pode desinstalar o agente usando o msiexec usando os seguintes exemplos:

   * No tipo de linha de comando:

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

> [!NOTE]
> Para desinstalar o agente, você deve ter permissões de acesso *raiz* ou uma conta que tenha direitos elevados usando o sudo.

Para desinstalar o agente do Linux, o comando a ser usado depende do sistema operacional Linux.

- Para o Ubuntu, execute o seguinte comando:

    ```bash
    sudo apt purge azcmagent
    ```

- Para RHEL, CentOS e Amazon Linux, execute o seguinte comando:

    ```bash
    sudo yum remove azcmagent
    ```

- Para o SLES, execute o seguinte comando:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Cancelar registro da máquina

Se você estiver planejando parar de gerenciar o computador com serviços de suporte no Azure, execute as seguintes etapas para cancelar o registro da máquina com o Arc para servidores (versão prévia). Você pode executar essas etapas antes ou depois de remover o agente do computador conectado do computador.

1. Abra o Azure Arc para servidores (versão prévia) acessando o [portal do Azure](https://aka.ms/hybridmachineportal).

2. Selecione o computador na lista, selecione as reticências (**...**) e, em seguida, selecione **Excluir**.
