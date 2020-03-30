---
title: Gerencie recursos remotos no local usando funções PowerShell
description: Aprenda a configurar conexões híbridas no Azure Relay para conectar um aplicativo de função PowerShell aos recursos locais, que podem ser usados para gerenciar remotamente o recurso local.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226929"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Gerenciamento de ambientes híbridos com PowerShell em funções azure e conexões híbridas de serviço de aplicativos

O recurso Conexões Híbridas do Serviço de Aplicativos Do Azure permite o acesso a recursos em outras redes. Você pode aprender mais sobre esse recurso na documentação [de Conexões Híbridas.](../app-service/app-service-hybrid-connections.md) Este artigo descreve como usar esse recurso para executar funções PowerShell que visam um servidor local. Esse servidor pode então ser usado para gerenciar todos os recursos no ambiente local a partir de uma função Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Configure um servidor on-premises para o powershell remoting

O script a seguir permite que o PowerShell seja remoting, e ele cria uma nova regra de firewall e um ouvinte https WinRM. Para fins de teste, é utilizado um certificado auto-assinado. Em um ambiente de produção, recomendamos que você use um certificado assinado.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Crie um aplicativo de função PowerShell no portal

O recurso App Service Hybrid Connections está disponível apenas em planos de preços básicos, padrão e isolados. Quando você criar o aplicativo de função com o PowerShell, crie ou selecione um desses planos.

1. No [portal Azure,](https://portal.azure.com)selecione **+ Crie um recurso** no menu à esquerda e, em seguida, **selecione O aplicativo Função**.

1. Para **o plano de hospedagem,** selecione o plano de serviço do **aplicativo**e selecione **o plano/localização do serviço de aplicativos.**

1. Selecione **Criar novo,** digite um nome **de plano de serviço de aplicativo,** escolha um **local** em uma [região](https://azure.microsoft.com/regions/) próxima a você ou perto de outros serviços que suas funções acessem e, em seguida, selecione O nível de **preços .**

1. Escolha o plano Padrão S1 e, em seguida, **selecione Aplicar**.

1. Selecione **OK** para criar o plano e, em seguida, configure as configurações restantes **do aplicativo de função** conforme especificado na tabela imediatamente após a seguinte captura de tela:

    ![Aplicativo de função PowerShell Core](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **Grupo de recursos** |  myResourceGroup | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. Você também pode usar o valor sugerido. |
    | **OS** | Sistema operacional preferido | Selecione Windows. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha powershell core. |
    | **Armazenamento** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes das contas de armazenamento devem ter de 3 a 24 caracteres de comprimento e podem conter apenas números e letras minúsculas. Você também pode usar uma conta existente.
    | **Application Insights** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Ao expandir essa configuração, você pode alterar o **nome do recurso Novo** ou escolher um **local** diferente em uma região de geografia [do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados. |

1. Depois que suas configurações forem validadas, selecione **Criar**.

1. Selecione o ícone **Notificação** no canto superior direito do portal e aguarde a mensagem "Implantação foi bem sucedida".

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Crie uma conexão híbrida para o aplicativo de função

As conexões híbridas são configuradas a partir da seção de rede do aplicativo de função:

1. Selecione a guia **Recursos** da plataforma no aplicativo de função e, em seguida, **selecione Rede**. 
   ![Visão geral do aplicativo para rede de plataformas](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selecione **Configure seus pontos finais de conexões híbridas**.
   ![Rede](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selecione **Adicionar conexão híbrida**.
   ![Conexão Híbrida](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Digite informações sobre a conexão híbrida como mostrado logo após a captura de tela a seguir. Você tem a opção de fazer com que a configuração **endpoint host** corresponda ao nome de host do servidor local para facilitar a relocção do servidor mais tarde quando estiver executando comandos remotos. A porta corresponde à porta padrão de serviço de gerenciamento remoto do Windows que foi definida no servidor anteriormente.
  ![Incluir Conexão Híbrida](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Nome da conexão híbrida**: ContosoHybridOnPremisesServer
    
    **Host Endpoint**: finanças1
    
    **Porto final:** 5986
    
    **Espaço de nome servicebus**: Criar novo
    
    **Localização**: Escolha um local disponível
    
    **Nome**: contosopowershellhybrid

5. Selecione **OK** para criar a conexão híbrida.

## <a name="download-and-install-the-hybrid-connection"></a>Baixe e instale a conexão híbrida

1. Selecione **O gerenciador de conexões Download** para salvar o arquivo .msi localmente em seu computador.
![Baixe o instalador](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Copie o arquivo .msi do seu computador local para o servidor local.
1. Execute o instalador do Hybrid Connection Manager para instalar o serviço no servidor local.
![Instalar conexão híbrida](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. A partir do portal, abra a conexão híbrida e copie a seqüência de conexão gateway para a área de transferência.
![Copiar seqüência de conexão híbrida](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Abra a i-I do Hybrid Connection Manager no servidor local.
![Open Hybrid Connection UI](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selecione o botão **Enter Manualmente** e cole a seqüência de conexão na área de transferência.
![Conexão de colar](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Reinicie o Gerenciador de Conexões Híbridas do PowerShell se ele não aparecer como conectado.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Crie uma configuração de aplicativo para a senha de uma conta de administrador

1. Selecione a guia **Recursos** da Plataforma no aplicativo de função.
1. Em **Configurações Gerais,** selecione **Configuração**.
![Selecione a configuração da plataforma](./media/functions-hybrid-powershell/select-configuration.png)  
1. Expandir **nova configuração de aplicativo** para criar uma nova configuração para a senha.
1. Nomeie a configuração _ContosoUserPassword_e digite a senha.
1. Selecione **OK** e, em seguida, salve para armazenar a senha no aplicativo da função.
![Adicionar configuração do aplicativo para senha](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Criar um gatilho http função para testar

1. Crie uma nova função de gatilho HTTP a partir do aplicativo de função.
![Criar novo gatilho HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Substitua o código PowerShell do modelo pelo seguinte código:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. Selecione **Salvar** e **Executar** para testar a função.
![Teste o aplicativo de função](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Gerenciamento de outros sistemas no local

Você pode usar o servidor on-premises conectado para se conectar a outros servidores e sistemas de gerenciamento no ambiente local. Isso permite que você gerencie suas operações de data center do Azure usando suas funções PowerShell. O script a seguir registra uma sessão de configuração do PowerShell que é executada as credenciais fornecidas. Essas credenciais devem ser para um administrador nos servidores remotos. Em seguida, você pode usar essa configuração para acessar outros pontos finais no servidor local ou datacenter.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Substitua as seguintes variáveis neste script pelos valores aplicáveis do seu ambiente:
* $HybridEndpoint
* $RemoteServer

Nos dois cenários anteriores, você pode conectar e gerenciar seus ambientes locais usando o PowerShell em Funções Azure e Conexões Híbridas. Nós encorajamos você a aprender mais sobre [Conexões Híbridas](../app-service/app-service-hybrid-connections.md) e [PowerShell em funções](./functions-reference-powershell.md).

Você também pode usar [redes virtuais](./functions-create-vnet.md) do Azure para se conectar ao seu ambiente local através das Funções Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como trabalhar com funções powershell](functions-reference-powershell.md)
