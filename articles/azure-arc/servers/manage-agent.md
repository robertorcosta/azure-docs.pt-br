---
title: Gerenciamento do azure Arc para servidor (visualização) agente
description: Este artigo descreve as diferentes tarefas de gerenciamento que você normalmente executará durante o ciclo de vida do Azure Arc para servidores conectado agente de máquina.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308965"
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

> [!NOTE]
> Para atualizar o agente, você deve ter permissões de acesso *raiz* ou com uma conta que tenha direitos elevados usando o Sudo.

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

## <a name="about-the-azcmagent-tool"></a>Sobre a ferramenta Azcmagent

A ferramenta Azcmagent (Azcmagent.exe) é usada para configurar o azure Arc para servidores (visualização) agente de máquina conectada durante a instalação ou modificar a configuração inicial do agente após a instalação. O Azcmagent.exe fornece parâmetros de linha de comando para personalizar o agente e visualizar seu status:

* **Conectar** - Para conectar a máquina ao Arco Azure

* **Desconectar** - Para desconectar a máquina do Arco Azure

* **Reconecte** - Para reconectar uma máquina desconectada ao Azure Arc

* **Mostrar** - Exibir o status do agente e suas propriedades de configuração (nome do Grupo de Recursos, ID de assinatura, versão, etc.), o que pode ajudar na solução de problemas com o agente.

* **-h ou --help** - Mostra parâmetros disponíveis de linha de comando

    Por exemplo, para ver ajuda detalhada para `azcmagent reconnect -h`o parâmetro **Reconectar,** digite . 

* **-v ou --verbose** - Habilitar o registro verboso

Você pode executar um **Connect,** **Desconectar**e **reconectar** manualmente enquanto estiver conectado interativamente ou automatizar usando o mesmo princípio de serviço que você usou para conectar vários agentes ou com um [token de acesso à](../../active-directory/develop/access-tokens.md)plataforma de identidade Microsoft . Se você não usou um diretor de serviço para registrar a máquina com o Azure Arc para servidores (visualização), consulte o [artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a seguir para criar um principal de serviço.

### <a name="connect"></a>Conectar

Este parâmetro especifica um recurso no Azure Resource Manager representando a máquina é criado no Azure. O recurso está no grupo de assinatura e recursos especificado, e os dados sobre `--location` a máquina são armazenados na região do Azure especificada pela configuração. O nome de recurso padrão é o nome de host desta máquina, se não especificado.

Um certificado correspondente à identidade atribuída ao sistema da máquina é então baixado e armazenado localmente. Uma vez concluída essa etapa, o Serviço de Metadados da Máquina Conectada do Azure e o Agente de Configuração de Hóspedes começam a sincronizar com o Azure Arc para servidores (visualização).

Para conectar usando um diretor de serviço, execute o seguinte comando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para conectar usando um token de acesso, execute o seguinte comando:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Para se conectar com suas credenciais de logon elevado (interativas), execute o seguinte comando:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Desconectar

Este parâmetro especifica que um recurso no Azure Resource Manager representando a máquina é excluído no Azure. Ele não exclui o agente da máquina, isso deve ser feito como um passo separado. Depois que a máquina for desconectada, se você quiser reregistrá-la `azcmagent connect` no Azure Arc para servidores (visualização), use para que um novo recurso seja criado para ela no Azure.

Para desconectar usando um diretor de serviço, execute o seguinte comando:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para desconectar usando um token de acesso, execute o seguinte comando:

`azcmagent disconnect --access-token <accessToken>`

Para desconectar-se com suas credenciais de logon elevado (interativas), execute o seguinte comando:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Reconectar

Este parâmetro reconecta a máquina já registrada ou conectada com o Azure Arc para servidores (visualização). Isso pode ser necessário se a máquina tiver sido desligada, pelo menos 45 dias, para que seu certificado expire. Este parâmetro usa as opções de autenticação fornecidas para recuperar novas credenciais correspondentes ao recurso Azure Resource Manager representando esta máquina.

Este comando requer privilégios maiores do que a função [Onboarding da máquina conectada do Azure.](overview.md#required-permissions)

Para reconectar usando um princípio de serviço, execute o seguinte comando:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Para reconectar usando um token de acesso, execute o seguinte comando:

`azcmagent reconnect --access-token <accessToken>`

Para se reconectar com suas credenciais de logon elevado (interativas), execute o seguinte comando:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Remova o agente

Execute um dos seguintes métodos para desinstalar o agente Windows ou Linux Connected Machine da máquina. A remoção do agente não desregistra a máquina com Arco para servidores (visualização), este é um processo separado que você executa quando não precisa mais gerenciar a máquina no Azure.

### <a name="windows-agent"></a>Agente do Windows

Ambos os métodos a seguir removem o agente, mas eles não removem a pasta *C:\Program Files\AzureConnectedMachineAgent* na máquina.

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

> [!NOTE]
> Para desinstalar o agente, você deve ter permissões de acesso *raiz* ou com uma conta que tenha direitos elevados usando o Sudo.

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

## <a name="unregister-machine"></a>Máquina de descadastrar

Se você estiver planejando parar de gerenciar a máquina com serviços de suporte no Azure, execute as seguintes etapas para descadastrar a máquina com Arc para servidores (visualização). Você pode executar essas etapas antes ou depois de ter removido o agente máquina conectada da máquina.

1. Abra o Azure Arc para servidores (versão prévia) acessando o [portal do Azure](https://aka.ms/hybridmachineportal).

2. Selecione o computador na lista, selecione as reticências (**...**) e, em seguida, selecione **Excluir**.
