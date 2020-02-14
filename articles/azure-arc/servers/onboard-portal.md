---
title: Conectar máquinas híbridas ao Azure por meio do portal do Azure
description: Neste artigo, você aprende a instalar o agente e a conectar computadores ao Azure usando o Azure ARC para servidores (versão prévia) do portal do Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 12fc29cf12fba6325af3197e727d94b3073ef2ff
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192305"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Conectar máquinas híbridas ao Azure por meio do portal do Azure

Você pode habilitar o Azure ARC para servidores (versão prévia) para um ou um pequeno número de computadores Windows ou Linux em seu ambiente executando um conjunto de etapas manualmente. Ou você pode usar um método automatizado executando um script de modelo que fornecemos. Esse script automatiza o download e a instalação de ambos os agentes.

Esse método requer que você tenha permissões de administrador no computador para instalar e configurar o agente. No Linux, usando a conta raiz e no Windows, você é membro do grupo Administradores local.

Antes de começar, certifique-se de revisar os [pré-requisitos](overview.md#prerequisites) e verificar se sua assinatura e seus recursos atendem aos requisitos.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gerar o script de instalação do portal do Azure

O script para automatizar o download e a instalação, e para estabelecer a conexão com o Arc do Azure, está disponível na portal do Azure. Para concluir o processo, faça o seguinte:

1. No navegador, vá para a [portal do Azure](https://aka.ms/hybridmachineportal).

1. Na página **computadores – Arc do Azure** , selecione **Adicionar**, na parte superior esquerda ou a opção **criar máquina – arco do Azure** na parte inferior do painel central. 

1. Na página **selecionar um método** , selecione o bloco **Adicionar computadores usando o script interativo** e, em seguida, selecione **gerar script**.

1. Na página **gerar script** , selecione a assinatura e o grupo de recursos em que você deseja que o computador seja gerenciado no Azure. Selecione um local do Azure onde os metadados do computador serão armazenados.

    >[!NOTE]
    >O arco do Azure para servidores (visualização) dá suporte apenas às seguintes regiões:
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >
    >Examine considerações adicionais ao selecionar uma região [aqui](overview.md#supported-regions) no artigo de visão geral.

1. Na página **gerar script** , na lista suspensa **sistema operacional** , selecione o sistema operacional no qual o script será executado.

1. Se a máquina estiver se comunicando por meio de um servidor proxy para se conectar à Internet, selecione **Avançar: servidor proxy**. 
1. Na guia **servidor proxy** , especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usará para se comunicar com o servidor proxy. Digite o valor no formato `http://<proxyURL>:<proxyport>`. 
1. Selecione **revisão + gerar**.

1. Na guia **revisar + gerar** , examine as informações de resumo e, em seguida, selecione **baixar**. Se você ainda precisar fazer alterações, selecione **anterior**.

## <a name="install-and-validate-the-agent-on-windows"></a>Instalar e validar o agente no Windows

### <a name="install-manually"></a>Instalar manualmente
Você pode instalar o agente do computador conectado manualmente executando o pacote de Windows Installer *AzureConnectedMachineAgent. msi*. 

> [!NOTE]
> * Para instalar ou desinstalar o agente, você deve ter permissões de *administrador* .
> * Você deve primeiro baixar e copiar o pacote do instalador para uma pasta no servidor de destino ou de uma pasta de rede compartilhada. Se você executar o pacote do instalador sem nenhuma opção, ele iniciará um assistente de instalação que você pode seguir para instalar o agente interativamente.

Se o computador precisar se comunicar por meio de um servidor proxy para o serviço, depois de instalar o agente, você precisará executar um comando descrito posteriormente neste artigo. Isso define a variável de ambiente do sistema do servidor proxy `https_proxy`.

A tabela a seguir destaca os parâmetros compatíveis com a instalação do agente por meio da linha de comando.

| Parâmetro | DESCRIÇÃO |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, insira `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Os arquivos para o agente do computador conectado são instalados em *C:\Program Files\AzureConnectedMachineAgent* por padrão. Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações detalhadas sobre o erro. O diretório de log é *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Instalar com o método com script

1. Faça logon no servidor.

1. Abra um prompt de comando do PowerShell com privilégios elevados.

1. Altere para a pasta ou o compartilhamento para o qual você copiou o script e execute-o no servidor executando o script `./OnboardingScript.ps1`.

### <a name="configure-the-agent-proxy-setting"></a>Definir a configuração de proxy do agente

Para definir a variável de ambiente do servidor proxy, execute o seguinte comando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>O agente não dá suporte à configuração de autenticação de proxy nesta visualização.
>

### <a name="configure-agent-communication"></a>Configurar a comunicação do agente

Depois de instalar o agente, você precisa configurar o agente para se comunicar com o serviço de arco do Azure executando o seguinte comando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Instalar e validar o agente no Linux

O agente do computador conectado para Linux é fornecido no formato de pacote preferencial para a distribuição (. RPM ou. DEB) hospedado no [repositório de pacotes](https://packages.microsoft.com/)da Microsoft. O [pacote de script do shell `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) executa as seguintes ações:

- Configura o computador host para baixar o pacote do agente do packages.microsoft.com.
- Instala o pacote do provedor de recursos híbrido.

Opcionalmente, você pode configurar o agente com suas informações de proxy, incluindo o parâmetro `--proxy "{proxy-url}:{proxy-port}"`.

O script também contém a lógica para identificar as distribuições com e sem suporte e verifica as permissões necessárias para executar a instalação. 

O exemplo a seguir baixa o agente e o instala:

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

Depois de instalar o agente, configure-o para se comunicar com o serviço Arc do Azure executando o seguinte comando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o arco do Azure

Depois de instalar o agente e configurá-lo para se conectar ao arco do Azure para servidores (versão prévia), vá para o portal do Azure para verificar se o servidor foi conectado com êxito. Exiba seus computadores na [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma conexão de servidor bem-sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Limpar

Para desconectar um computador do arco do Azure para servidores (versão prévia), faça o seguinte:

1. Abra o arco do Azure para servidores (versão prévia) acessando o [portal do Azure](https://aka.ms/hybridmachineportal).

1. Selecione o computador na lista, selecione as reticências ( **...** ) e, em seguida, selecione **excluir**.

1. Para desinstalar o agente do Windows do computador, faça o seguinte:

    a. Entre no computador com uma conta que tenha permissões de administrador.  
    b. No **painel de controle**, selecione **programas e recursos**.  
    c. Em **programas e recursos**, selecione **agente do computador conectado do Azure**, selecione **desinstalar**e, em seguida, selecione **Sim**.  

    >[!NOTE]
    > Você também pode executar o assistente de instalação do agente clicando duas vezes no pacote do instalador **AzureConnectedMachineAgent. msi** .

    Se quiser fazer o script de remoção do agente, você pode usar o exemplo a seguir, que recupera o código do produto e desinstala o agente usando a linha de comando msiexec. exe-`msiexec /x {Product Code}`. Para fazer isso:  
    
    a. Abra o Editor do Registro.  
    b. Em chave do registro `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, procure e copie o GUID do código do produto.  
    c. Em seguida, você pode desinstalar o agente usando o msiexec.

    O exemplo a seguir demonstra como desinstalar o agente:

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

- Saiba como gerenciar seu computador usando [Azure Policy](../../governance/policy/overview.md), para tarefas como [configuração de convidado](../../governance/policy/concepts/guest-configuration.md)de VM, verificar se o computador está relatando para o espaço de trabalho esperado log Analytics, habilitar o monitoramento com o [Azure monitor com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)e muito mais.

- Saiba mais sobre o [agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja monitorar proativamente o sistema operacional e as cargas de trabalho em execução no computador, gerenciá-lo usando runbooks de automação ou soluções como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-intro.md).