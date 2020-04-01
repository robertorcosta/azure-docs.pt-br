---
title: Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure
description: Como configurar máquinas para gerenciamento com a configuração do estado de automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 554a4c64700bb189b4b9f085bd7c259312a36b4b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410946"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Por que gerenciar máquinas com a Configuração do Estação de Automação do Azure?

A Configuração do Estado de Automação do Azure é um serviço de gerenciamento de configuração para os álos dsc (Desired State Configuration, configuração do estado desejado) em qualquer datacenter na nuvem ou no local. Ele é acessado no portal Azure selecionando **configuração de Estado (DSC)** em **Gerenciamento de Configuração**. 

Este serviço permite escalabilidade em milhares de máquinas de forma rápida e fácil a partir de um local central e seguro. Você pode facilmente a bordo de máquinas, atribuir-lhes configurações declarativas e visualizar relatórios mostrando a conformidade de cada máquina com o estado desejado que você especificar.

O serviço de configuração do estado de automação do Azure é para dSC quais são os runbooks do Azure Automation para o scripting PowerShell. Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do PowerShell, ela também ajuda a gerenciar configurações do DSC. Para saber mais sobre os benefícios de usar a Configuração de Estado de Automação do Azure, consulte [Visão geral da Configuração de Estado de Automação do Azure](automation-dsc-overview.md).

A Configuração do Estado de Automação do Azure pode ser usada para gerenciar uma variedade de máquinas:

- Máquinas Virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Máquinas Windows físicas/virtuais no local ou em uma nuvem diferente do Azure (incluindo instâncias aWS EC2)
- Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Se você não estiver pronto para gerenciar a configuração da máquina a partir da nuvem, você pode usar a configuração do estado de automação do Azure como um ponto final somente para relatórios. Esse recurso permite definir (empurrar) configurações através do DSC e visualizar detalhes de relatórios no Azure Automation.

> [!NOTE]
> O gerenciamento de VMs do Azure com a configuração do estado de automação do Azure está incluído sem custo adicional se a versão instalada da extensão de configuração do estado desejado do Azure VM for maior que 2,70. Para obter mais informações, consulte [**a página de preços de automação**](https://azure.microsoft.com/pricing/details/automation/).

As seções a seguir deste artigo descrevem como você pode embarcar nas máquinas listadas acima para a Configuração do Estado de Automação do Azure.

## <a name="onboarding-azure-vms"></a>Onboarding Azure VMs

A configuração do Estado de Automação do Azure permite que você abordo facilmente as VMs do Azure para gerenciamento de configuração, usando o portal Azure, os modelos do Azure Resource Manager ou o PowerShell. Sob o capô, e sem que um administrador tenha que se controlar em uma VM, a extensão Azure VM Desired State Configuration registra a VM com a Configuração do Estado de Automação do Azure. Uma vez que a extensão do Azure é executada de forma assíncrona, as etapas para acompanhar seu progresso ou soluciona-lo são fornecidas na seção de onboarding de [máquina virtual Azure](#troubleshooting-azure-virtual-machine-onboarding) de solução de problemas deste artigo.

> [!NOTE]
>A implantação do DSC em um nó Linux usa a pasta **/tmp.** Módulos como `nxautomation` são temporariamente baixados para verificação antes de instalá-los em seus locais apropriados. Para garantir que os módulos se instalem corretamente, o agente Log Analytics para Linux precisa de permissões de leitura/gravação na pasta **/tmp.**<br><br>
>O agente log analytics para `omsagent` Linux é executado como usuário. Para conceder >permissão de gravação ao `omsagent` usuário, execute o comando `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-a-vm-using-azure-portal"></a>A bordo de uma VM usando o portal Azure

Para embarcar em uma configuração do Estado de automação do Azure para azure através do [portal Azure](https://portal.azure.com/):

1. Navegue até a conta Azure Automation na qual as VMs a bordo. 

2. Na página Configuração do estado, selecione **Add**a guia **'''''''''Configuração** de estado', selecione a guia '''''''Adicionar'.

3. Escolha uma VM a bordo.

4. Se a máquina não tiver a extensão de estado desejada powerShell instalada e o estado de alimentação estiver em execução, clique **em Conectar**.

5. Em **Registro,** insira os [valores do Gerenciador de Configuração Local do PowerShell DSC necessários](/powershell/scripting/dsc/managing-nodes/metaConfig) para o seu caso de uso. Opcionalmente, você pode inserir uma configuração de nó para atribuir à VM.

![integração](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>A bordo de uma VM usando modelos do Azure Resource Manager

Você pode implantar e embarcar uma configuração de estado de automação VM para Azure usando modelos do Azure Resource Manager. Consulte [o servidor gerenciado pelo serviço desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para um modelo de exemplo que abordo uma Configuração de Estado de Automação de Automação existente para o Azure. Se você estiver gerenciando um conjunto de escala de VM, veja o modelo de exemplo na [configuração de conjunto de escala de VM gerenciada pelo Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Máquinas a bordo usando PowerShell

Você pode usar o [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) no PowerShell para acessar VMs para configuração do estado de automação do Azure. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para máquinas que executam o Windows, pois aciona apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registre VMs em assinaturas do Azure

A melhor maneira de registrar VMs de outras assinaturas do Azure é usar a extensão DSC em um modelo de implantação do Azure Resource Manager. Exemplos são fornecidos na [extensão De configuração de estado desejado com modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para encontrar a chave de registro e a URL de registro para usar como parâmetros no modelo, consulte o [Onboarding com segurança usando](#onboarding-securely-using-registration) a seção de registro neste artigo.

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Onboarding de máquinas Windows físicas/virtuais no local ou em uma nuvem diferente do Azure (incluindo instâncias aWS EC2)

Você pode a bordo de servidores Windows executando no local ou em outros ambientes de nuvem para a configuração do estado de automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) esteja instalada nas máquinas para a bordo do Azure Automation State Configuration. Além disso, o WMF 5 deve ser instalado no computador que você está usando para a operação de onboarding.
1. Siga as instruções na seção [Gerando metaconfigurações DSC](#generating-dsc-metaconfigurations) para criar uma pasta contendo as metaconfigurações dsc necessárias. 
1. Use o cmdlet a seguir para aplicar as metaconfigurações do PowerShell DSC remotamente às máquinas que você deseja a bordo. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não puder aplicar as metaconfigurações do PowerShell DSC remotamente, copie a pasta **de metaconfigurações** para as máquinas que você está embarcando. Em seguida, `Set-DscLocalConfigurationManager` adicione código para chamar localmente nas máquinas.
1. Usando o portal Azure ou cmdlets, verifique se as máquinas a bordo aparecem como um nó de configuração do Estado registrado em sua conta de Automação Azure.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Onboarding de máquinas Linux físicas/virtuais no local, ou em uma nuvem diferente do Azure

Você pode a bordo de servidores Linux executando no local ou em outros ambientes de nuvem para a configuração do estado de automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) esteja instalada nas máquinas a bordo da configuração do estado de automação do Azure.
2. Se o [Gerenciador de configuração local do PowerShell DSC for compatível](/powershell/scripting/dsc/managing-nodes/metaConfig4) com o seu estojo de uso, você deseja a bordo de máquinas para que ambos puxem e reportem à configuração do estado de automação do Azure:

   - Em cada máquina Linux a bordo da configuração `Register.py` do estado de automação do Azure, use-o a bordo usando os padrões do PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para encontrar a chave de registro e url de registro para sua conta de Automação, consulte o [Onboarding com segurança usando](#onboarding-securely-using-registration) a seção de registro deste artigo.

3. Se os padrões do PowerShell DSC Local Configuration Manager (LCM) não corresponderem ao seu estojo de uso ou se você quiser a bordo de máquinas que só se reportam à Configuração do Estado de Automação do Azure, siga as etapas 4-7. Caso contrário, vá diretamente para a etapa 7.

4. Siga as instruções na [seção De metaconfigurações DSC geradoras](#generating-dsc-metaconfigurations) para produzir uma pasta contendo as metaconfigurações DSC necessárias.

5. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) esteja instalada na máquina que está sendo usada para onboarding.

6. Adicione o código a seguir para aplicar as metaconfigurações do PowerShell DSC remotamente às máquinas que você deseja a bordo.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se você não puder aplicar as metaconfigurações do PowerShell DSC remotamente, copie as metaconfigurações correspondentes às máquinas remotas da pasta descrita na etapa 4 para as máquinas Linux.

8. Adicione código `Set-DscLocalConfigurationManager.py` para chamar localmente em cada máquina Linux a bordo da configuração do estado de automação do Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Usando o portal Azure ou cmdlets, certifique-se de que as máquinas a bordo agora apareçam como nódulos DSC registrados em sua conta azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Gerando metaconfigurações DSC

Para embarcar em qualquer máquina para a configuração do estado de automação do Azure, você pode gerar uma [metaconfiguração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Essa configuração diz ao agente DSC para puxar e/ou reportar à configuração do estado de automação do Azure. Você pode gerar uma metaconfiguração DSC para configuração do estado de automação do Azure usando uma configuração PowerShell DSC ou os cmdlets PowerShell de automação do Azure.

> [!NOTE]
> Metaconfigurações DSC contêm os segredos necessários para carregar um computador em uma conta de Automação para gerenciamento. Certifique-se de proteger corretamente quaisquer metaconfigurações de DSC que criar, ou exclua-as imediatamente após o uso.

O suporte proxy para metaconfigurações é controlado pelo LCM, que é o mecanismo DSC do Windows PowerShell. O LCM é executado em todos os nós de destino e é responsável por chamar os recursos de configuração incluídos em um script de metaconfiguração DSC. Você pode incluir suporte proxy em uma metaconfiguração, incluindo definições da URL `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`proxy `ReportServerWeb` e da credencial proxy conforme necessário no , e blocos. Consulte [Configuração do Gerenciador de Configuração Local](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Gerar metaconfigurações de DSC usando uma configuração DSC

1. Abra o VSCode (ou seu editor favorito) como administrador em uma máquina em seu ambiente local. O computador também deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Compie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para dar início à criação de metaconfigurações.

    > [!NOTE]
    > Os nomes de configuração do nó de configuração do estado são sensíveis a maiúsculas e minúsculas no portal Azure. Se o caso for incompatível, o nó não aparecerá sob a guia **Nodes.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Preencha a chave de registro e a URL para sua conta de Automação, bem como os nomes dos computadores a carregar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registro e url de registro para sua conta de Automação, consulte o [Onboarding com segurança usando a](#onboarding-securely-using-registration) seção de registro.

1. Se você quiser que as máquinas reportem as informações de status do DSC à `ReportOnly` configuração do estado de automação do Azure, mas não à configuração de puxar ou módulos PowerShell, defina o parâmetro como verdadeiro.

1. Se `ReportOnly` não estiver definido, as máquinas reportam as informações de status do DSC para a configuração do estado de automação do Azure e para a configuração de puxar ou módulos PowerShell. Defina os parâmetros `ResourceRepositoryWeb`de `ReportServerWeb` acordo com os `ConfigurationRepositoryWeb`blocos e blocos.

1. Execute o script. Agora você deve ter uma pasta de diretório de trabalho chamada **DscMetaConfigs**, contendo as metaconfigurações do PowerShell DSC para as máquinas a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar metaconfigurações De DSC usando cmdlets de automação do Azure

Se os padrões do PowerShell DSC LCM corresponderem ao seu estojo de uso e você quiser que as máquinas de bordo puxem e reportem para a configuração do Estado de Automação do Azure, você poderá gerar as metaconfigurações necessárias do DSC mais simplesmente usando os cmdlets de Automação do Azure.

1. Abra o console PowerShell ou VSCode como administrador em uma máquina em seu ambiente local.
2. Conecte-se ao Azure Resource Manager usando `Connect-AzAccount`
3. Baixe as metaconfigurações do PowerShell DSC para as máquinas que deseja embarcar a partir da conta de Automação na qual você está configurando nós.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora você deve ter uma pasta chamada **DscMetaConfigs**, contendo as metaconfigurações do PowerShell DSC para as máquinas a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Onboarding com segurança usando o registro

As máquinas podem embarcar com segurança em uma conta do Azure Automation através do protocolo de registro WMF 5 DSC. Este protocolo permite que um nó DSC seja autenticado em um servidor de puxar ou relatar O DSC do PowerShell, incluindo a configuração do estado de automação do Azure. O nó registra-se com o servidor na URL de registro e autentica usando uma chave de registro. Durante o registro, o servidor de puxar/relatório DSC e dSC negocia um certificado exclusivo para o nó a ser usado para autenticação no pós-registro do servidor. Esse processo evita que os nódulos a bordo se representem uns aos outros, por exemplo, se um nó estiver comprometido e se comportar maliciosamente. Após o registro, a chave de registro não é usada para autenticação novamente e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro da Configuração do Estado a partir das **Chaves** nas **Configurações de Conta**no portal do Azure. 

![Chaves de automação do Azure e a URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registro é o campo URL na página Chaves.
- A chave de registro é o valor do campo de chave de **acesso primário** ou do campo de chave de acesso **secundário** na página Chaves. Qualquer chave pode ser usada.

Para maior segurança, você pode regenerar as chaves de acesso primárias e secundárias de uma conta de Automação a qualquer momento na página Chaves. A regeneração das chaves impede que registros futuros de nó usem chaves anteriores.

## <a name="re-registering-a-node"></a>Recadastramento de um nó

Depois de registrar uma máquina como um nó DSC na configuração do estado de automação do Azure, existem várias razões pelas quais você pode precisar reregistrar esse nó no futuro.

- **Renovação do certificado.** Para versões do Windows Server antes do Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo de autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não poderá `Unresponsive`se comunicar com a Azure Automation e estiver marcado . Atualmente, o protocolo de registro do PowerShell DSC não pode renovar automaticamente os certificados quando eles estão próximos do vencimento, e você deve reregistrar os nós após um ano. Antes de recadastrar, certifique-se de que cada nó esteja executando o WMF 5 RTM. 

    O recadastramento realizado 90 dias ou menos a partir do prazo de validade do certificado, ou em qualquer momento após o prazo de validade do certificado, resulta em um novo certificado sendo gerado e utilizado. Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.

- **Alterações nos valores de LCM DSC.** Talvez seja necessário alterar os [valores do PowerShell DSC LCM definidos](/powershell/scripting/dsc/managing-nodes/metaConfig4) durante o registro inicial do nó, por exemplo, `ConfigurationMode`. Atualmente, você só pode alterar esses valores de agente DSC através do re-registro. A única exceção é o valor de configuração do nó atribuído ao nó. Você pode alterar isso diretamente no Azure Automation DSC.

Você pode reregistrar um nó da mesma forma que registrou o nó inicialmente, usando qualquer um dos métodos de onboarding descritos neste documento. Você não precisa cancelar o registro de um nó da configuração do estado de automação do Azure antes de reregistrá-lo.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solução de problemas de integração de máquina virtual do Azure

O Configuração do Estado de Automação do Azure permite a fácil integração de VMs do Microsoft Azure ao gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com a Configuração do Estado de Automação do Azure. Uma vez que a extensão Azure VM Desired State Configuration é executada de forma assíncrona, acompanhar seu progresso e solucionar problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de embarcar em um Azure Windows VM para a configuração do Estado de Automação do Azure que use a extensão de configuração de estado desejado do Azure VM pode levar até uma hora para a Azure Automation mostrar como registrado. Esse atraso deve-se à instalação do WMF 5 na VM pela extensão De configuração do estado desejado do Azure VM, que é necessária para embarcar na Configuração do Estado de Automação VM para Azure.

Para solucionar problemas ou visualizar o status da extensão de configuração do estado desejado do Azure VM:

1. No portal Azure, navegue até a VM sendo a bordo.
2. Clique **em Extensões** em **Configurações**. 
3. Agora selecione **DSC** ou **DSCForLinux,** dependendo do seu sistema operacional. 
4. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

Para obter mais informações sobre solução de [problemas, consulte Problemas de solução de problemas com o DSC (Desired State Configuration, configuração do estado desejado da automação do Azure).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Como começar com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilando configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter referência de cmdlet PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/az.automation#automation).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de usar a configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [Exemplo de uso: implantação contínua para máquinas virtuais usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
