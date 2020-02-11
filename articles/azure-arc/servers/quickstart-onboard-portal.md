---
title: Conectar computadores híbridos ao Azure por meio do portal do Azure
description: Neste artigo, você aprenderá a instalar o agente e a conectar computadores ao Azure usando o Azure Arc para servidores (versão prévia) no portal do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 81083a9d94f782201a8eb765ac1f88093c0337c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024085"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Conectar computadores híbridos ao Azure por meio do portal do Azure

Você pode habilitar o Azure Arc para servidores (versão prévia) para um ou um pequeno número de computadores Windows ou Linux em seu ambiente executando um conjunto de etapas manualmente. Ou você pode usar um método automatizado executando um script de modelo que fornecemos. Esse script automatiza o download e a instalação de ambos os agentes.

Esse método exige que você tenha permissões de administrador no computador para instalar e configurar o agente. No Linux, usando a conta raiz, e no Windows, você é membro do grupo local de administradores.

Antes de começar, examine os [pré-requisitos](overview.md#prerequisites) e verifique se a sua assinatura e os recursos atendem aos requisitos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gerar o script de instalação no portal do Azure

O script usado para automatizar o download e a instalação e para estabelecer a conexão com o Azure Arc está disponível no portal do Azure. Para concluir o processo, faça o seguinte:

1. No navegador, acesse o [portal do Azure](https://aka.ms/hybridmachineportal).

1. Na página **Computadores – Azure Arc**, selecione **Adicionar** no canto superior esquerdo ou a opção **Criar computador – Azure Arc** na parte inferior do painel central. 

1. Na página **Selecionar um método**, selecione o bloco **Adicionar computadores usando o script interativo** e, em seguida, selecione **Gerar script**.

1. Na página **Gerar script**, selecione a assinatura e o grupo de recursos nos quais você deseja que o computador seja gerenciado no Azure. Selecione uma localização do Azure em que os metadados do computador serão armazenados.

    >[!NOTE]
    >O Azure Arc para servidores (versão prévia) só dá suporte às seguintes regiões:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >

1. Na página **Gerar script**, na lista suspensa **Sistema operacional**, selecione o sistema operacional no qual o script será executado.

1. Se o computador estiver se comunicando por meio de um servidor proxy para se conectar à Internet, selecione **Próximo: Servidor Proxy**. 
1. Na guia **Servidor proxy**, especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usará para se comunicar com o servidor proxy. Digite o valor no formato `http://<proxyURL>:<proxyport>`. 
1. Selecione **Examinar + gerar**.

1. Na guia **Examinar + gerar**, examine as informações de resumo e, em seguida, selecione **Baixar**. Se você ainda precisar fazer alterações, selecione **Anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalar e validar o agente no Windows

### <a name="install-manually"></a>Instalar manualmente
Instale o agente do Connected Machine manualmente executando o pacote *AzureConnectedMachineAgent.msi* do Windows Installer. 

> [!NOTE]
> * Para instalar ou desinstalar o agente, você precisa ter permissões de *Administrador*.
> * Primeiro, é necessário baixar e copiar o pacote do instalador para uma pasta no servidor de destino ou de uma pasta de rede compartilhada. Se você executar o pacote do instalador sem nenhuma opção, ele iniciará um assistente de instalação que você poderá seguir para instalar o agente de maneira interativa.

Se o computador precisar se comunicar por meio de um servidor proxy com o serviço, depois de instalar o agente, você precisará executar um comando que será descrito mais adiante neste artigo. Isso define a variável de ambiente `https_proxy` do sistema do servidor proxy.

A tabela a seguir destaca os parâmetros compatíveis com a instalação do agente por meio da linha de comando.

| Parâmetro | Descrição |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, insira `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Os arquivos do agente do Connected Machine são instalados em *C:\Program Files\AzureConnectedMachineAgent* por padrão. Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. O diretório de log é *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instalação com o método com script

1. Faça logon no servidor.

1. Abra um prompt de comando com privilégios elevados do PowerShell.

1. Vá para a pasta ou o compartilhamento para o qual você copiou o script e execute-o no servidor executando o script `./OnboardingScript.ps1`.

### <a name="configure-the-agent-proxy-setting"></a>Definir a configuração do proxy do agente

Para definir a variável de ambiente do servidor proxy, execute o seguinte comando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>O agente não dá suporte à definição da autenticação de proxy nesta versão prévia.
>

### <a name="configure-agent-communication"></a>Configurar a comunicação do agente

Depois de instalar o agente, você precisará configurá-lo para se comunicar com o serviço Azure Arc executando o seguinte comando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalar e validar o agente no Linux

O agente do Connected Machine para Linux é fornecido no formato de pacote preferencial para a distribuição (.RPM ou .DEB) hospedada no [repositório de pacotes](https://packages.microsoft.com/) da Microsoft. O [pacote de script do shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) executa as seguintes ações:

- Configura o computador host para baixar o pacote do agente em packages.microsoft.com.
- Instala o pacote do provedor de recursos híbrido.

Opcionalmente, você pode configurar o agente com as suas informações de proxy, incluindo o parâmetro `--proxy "{proxy-url}:{proxy-port}"`.

O script também contém a lógica para identificar as distribuições com e sem suporte e verifica as permissões necessárias para executar a instalação. 

O seguinte exemplo baixa o agente e o instala:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Para baixar e instalar o agente, incluindo o parâmetro `--proxy` para configurar o agente para se comunicar por meio do servidor proxy, execute os seguintes comandos:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Configurar a comunicação do agente

Depois de instalar o agente, configure-o para se comunicar com o serviço Azure Arc executando o seguinte comando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o Azure Arc

Depois de instalar o agente e configurá-lo para se conectar ao Azure Arc para servidores (versão prévia), acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma conexão de servidor bem-sucedida](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Limpar

Para desconectar um computador do Azure Arc para servidores (versão prévia), faça o seguinte:

1. Abra o Azure Arc para servidores (versão prévia) acessando o [portal do Azure](https://aka.ms/hybridmachineportal).

1. Selecione o computador na lista, selecione as reticências ( **...** ) e, em seguida, selecione **Excluir**.

1. Para desinstalar o agente do Windows do computador, faça o seguinte:

    a. Entre no computador com uma conta que tenha permissões de administrador.  
    b. No **Painel de Controle**, selecione **Programas e Recursos**.  
    c. Em **Programas e Recursos**, selecione **Agente do Azure Connected Machine**, **Desinstalar** e, em seguida, **Sim**.  

    >[!NOTE]
    > Execute também o assistente de instalação do agente clicando duas vezes no pacote do instalador **AzureConnectedMachineAgent.msi**.

    Caso deseje criar o script da desinstalação, use o exemplo a seguir, que recupera o código do produto e desinstala o agente usando a linha de comando Msiexec.exe, `msiexec /x {Product Code}`. Para fazer isso:  
    
    a. Abra o Editor do Registro.  
    b. Na chave do Registro `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, procure e copie o GUID do código do produto.  
    c. Em seguida, você poderá desinstalar o agente usando o Msiexec.

    O seguinte exemplo demonstra como desinstalar o agente:

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Para desinstalar o agente do Linux, execute o seguinte comando:

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atribuir uma política a computadores conectados](../../governance/policy/assign-policy-portal.md)
