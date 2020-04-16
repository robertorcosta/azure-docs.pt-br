---
title: Máquinas a bordo para gerenciamento pela configuração do estado de automação do Azure
description: Este artigo descreve como configurar máquinas para gerenciamento com a configuração do Estado de Automação do Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406192"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Máquinas a bordo para gerenciamento pela configuração do estado de automação do Azure

A Configuração do Estado de Automação do Azure é um serviço de gerenciamento de configuração para os álos dsc (Desired State Configuration, configuração do estado desejado) em datacenters em nuvem ou no local. Você pode acessar o serviço no portal Azure selecionando **Configuração de Estado (DSC)** em **Gerenciamento de Configuração**. 

Com a configuração do Estado de Automação do Azure, você pode alcançar escalabilidade em milhares de máquinas de forma rápida e fácil a partir de um local central e seguro. Você pode facilmente a bordo de máquinas, atribuir-lhes configurações declarativas e visualizar relatórios que mostram a conformidade de cada máquina com o estado que você especificou.

O serviço de configuração do estado de automação do Azure é para dSC quais são os runbooks do Azure Automation para o scripting PowerShell. Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do PowerShell, ela também ajuda a gerenciar configurações do DSC. Para saber mais sobre os benefícios de usar a Configuração de Estado de Automação do Azure, consulte [Visão geral da Configuração de Estado de Automação do Azure](automation-dsc-overview.md).

Você pode usar a configuração do estado de automação do Azure para gerenciar uma variedade de máquinas:

- VMs (máquinas virtuais) do Azure
- Máquinas virtuais do Azure (clássico)
- Máquinas Windows físicas/virtuais no local ou em uma nuvem diferente do Azure (incluindo as instâncias da Nuvem de Computação Elástica [AWS] Da Amazon Web Services [AWS])
- Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Se você não estiver pronto para gerenciar a configuração da máquina a partir da nuvem, você pode usar a configuração do estado de automação do Azure como um ponto final somente para relatórios. Esse recurso permite definir ou empurrar configurações através do DSC e visualizar detalhes de relatórios no Azure Automation.

> [!NOTE]
> O gerenciamento de VMs do Azure com a configuração do estado de automação do Azure está incluído sem custo adicional se a extensão de configuração do estado desejado do Azure VM é a versão 2.70 ou posterior. Para obter mais informações, consulte [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/).

As seções a seguir deste artigo descrevem como você pode embarcar as máquinas listadas anteriormente para a configuração do estado de automação do Azure.

## <a name="onboard-azure-vms"></a>Integrar VMs do Azure

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ para o Trabalhador do Runbook Híbrido, consulte [Instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [módulos Update Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

Como a extensão do Azure é executada de forma assíncrona, fornecemos etapas para acompanhar seu progresso ou soluciona-lo na seção de onboarding de [máquina virtual Azure](#troubleshoot-azure-virtual-machine-onboarding) de solução de problemas deste artigo.

> [!NOTE]
> A implantação do DSC em um nó Linux usa a pasta */tmp.* Módulos como `nxautomation` são temporariamente baixados para verificação antes de serem instalados. Para garantir que os módulos sejam instalados corretamente, o agente Log Analytics para Linux precisa de permissões de leitura/gravação na pasta */tmp.*<br><br>
> O agente log analytics para Linux é executado como usuário *omsagent.* Para conceder permissão de gravação ao `setfacl -m u:omsagent:rwx /tmp`usuário *omsagent,* execute o comando .

### <a name="onboard-vms-by-using-the-azure-portal"></a>VMs a bordo usando o portal Azure

Para embarcar as VMs do Azure para configuração do estado de automação do Azure através do [portal Azure](https://portal.azure.com/):

1. Vá para a conta azure Automação na qual deseja embarcar em VMs. 

1. Na **página Configuração do estado,** selecione a **Add**guia **''''''''''Configuração'**

1. Escolha uma VM a bordo.

1. Se a máquina não tiver a extensão Estado Desejado PowerShell instalada e o estado de alimentação estiver em execução, selecione **Conectar**.

1. Em **Registro,** digite os [valores do Gerenciador de Configuração Local (LCM) do PowerShell DSC necessários](/powershell/scripting/dsc/managing-nodes/metaConfig) para o seu caso de uso. Opcionalmente, você pode inserir uma configuração de nó para atribuir à VM.

![O painel de registro da VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>VMs a bordo usando modelos do Azure Resource Manager

Você pode implantar e embarcar uma configuração de estado de automação VM para Azure usando modelos do Azure Resource Manager. Para um modelo de exemplo que abordo uma Configuração de estado de automação de automação existente para o Azure, consulte [Servidor gerenciado pelo serviço de configuração do estado desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/). Se você estiver gerenciando um conjunto de escala de máquina virtual, veja o modelo de exemplo na [configuração do conjunto de escala de máquina virtual gerenciada pelo Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-vms-by-using-powershell"></a>VMs a bordo usando PowerShell

Você pode usar o [cmdlet Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) no PowerShell para acessar VMs para configuração do estado de automação do Azure. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para máquinas que executam o Windows, porque o cmdlet aciona apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registre VMs em assinaturas do Azure

A melhor maneira de registrar VMs de outras assinaturas do Azure é usar a extensão DSC em um modelo de implantação do Azure Resource Manager. Exemplos são fornecidos na [extensão De configuração de estado desejado com modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para encontrar a chave de registro e a URL de registro para usar como parâmetros no modelo, consulte o [Onboard com segurança usando a](#onboard-securely-by-using-registration) seção de registro neste artigo.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Máquinas Windows físicas/virtuais a bordo no local ou em uma nuvem diferente do Azure

Você pode a bordo de servidores Windows que estão executando no local ou em outros ambientes na nuvem, incluindo instâncias AWS EC2, para a configuração do estado de automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [Windows Management Framework 5](https://aka.ms/wmf5latest) esteja instalada nas máquinas a serem instaladas no Azure Automation State Configuration. Além disso, o Windows Management Framework 5 deve ser instalado no computador que você está usando para a operação de onboarding.
1. Para criar uma pasta contendo as metaconfigurações dsc necessárias, siga as instruções na seção [Gerar metaconfigurações DSC.](#generate-dsc-metaconfigurations) 
1. Para aplicar as metaconfigurações do PowerShell DSC remotamente às máquinas que deseja a bordo, use o seguinte cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não puder aplicar as metaconfigurações do PowerShell DSC remotamente, copie a pasta **de metaconfigurações** para as máquinas que você está embarcando. Em seguida, adicione código para chamar [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nas máquinas.

1. No portal do Azure ou usando cmdlets, verifique se as máquinas a bordo aparecem como nós de configuração do Estado registrados em sua conta de Automação Do Azure.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Máquinas Linux físicas/virtuais a bordo no local ou em uma nuvem diferente do Azure

Você pode a bordo de servidores Linux executando no local ou em outros ambientes de nuvem para a configuração do estado de automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) esteja instalada nas máquinas a serem instaladas no Azure Automation State Configuration.
1. Se o [padrão Do PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponder ao seu estojo de uso e você quiser abordar máquinas para que ambos puxem e reportem à Configuração do Estado de Automação do Azure, faça o seguinte:

   a. Em cada máquina Linux a bordo da configuração `Register.py` do estado de automação do Azure, use-as a bordo usando os padrões do PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Para encontrar a chave de registro e url de registro para sua conta de Automação, consulte o [Onboard com segurança usando a](#onboard-securely-by-using-registration) seção de registro deste artigo.  
   c. Pule para o passo 4.
   
1. Se os padrões do PowerShell DSC LCM não corresponderem ao seu caso de uso ou se você quiser a bordo de máquinas que se reportam apenas à configuração do Estado de Automação do Azure, siga as etapas a d. Caso contrário, proceda diretamente para o passo d.

    a. Para produzir uma pasta contendo as metaconfigurações dsc necessárias, siga as instruções na seção [Gerar metaconfigurações DSC.](#generate-dsc-metaconfigurations)

    b. Certifique-se de que a versão mais recente do [Windows Management Framework 5](https://aka.ms/wmf5latest) esteja instalada na máquina que está sendo usada para o onboarding.

    c. Para aplicar as metaconfigurações do PowerShell DSC remotamente às máquinas que você deseja a bordo, adicione o seguinte código:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Se você não puder aplicar as metaconfigurações do PowerShell DSC remotamente, copie as metaconfigurações correspondentes às máquinas remotas da pasta descrita na etapa 3a para as máquinas Linux.

1. Adicione código para chamar *Set-DscLocalConfigurationManager.py* localmente em cada máquina Linux para ser a bordo da Configuração do Estado de Automação do Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Usando o portal Azure ou cmdlets, certifique-se de que as máquinas a bordo agora apareçam como nódulos DSC registrados em sua conta azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Gerar metaconfigurações DSC

Para embarcar em qualquer máquina para a configuração do estado de automação do Azure, você pode gerar uma [metaconfiguração DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Essa configuração diz ao agente DSC para puxar ou reportar à configuração do estado de automação do Azure. Você pode gerar uma metaconfiguração DSC para configuração do estado de automação do Azure usando uma configuração powershell DSC ou os cmdlets PowerShell de automação do Azure.

> [!NOTE]
> As metaconfigurações dsc contêm os segredos que você precisa para embarcar uma máquina em uma conta de Automação para gerenciamento. Certifique-se de proteger adequadamente quaisquer metaconfigurações dsc que você crie ou excluí-las após o uso.

O suporte proxy para metaconfigurações é controlado pelo LCM, que é o mecanismo DSC do Windows PowerShell. O LCM é executado em todos os nós de destino e é responsável por chamar os recursos de configuração incluídos em um script de metaconfiguração DSC. 

Você pode incluir suporte proxy em uma metaconfiguração, incluindo definições da URL `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`proxy `ReportServerWeb` e da credencial proxy conforme necessário no , e blocos. Consulte [Configurar o gerenciador de configuração local](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Gerar metaconfigurações de DSC usando uma configuração DSC

1. Abra o Visual Studio Code (ou seu editor favorito) como administrador em uma máquina em seu ambiente local. A máquina deve ter a versão mais recente do [Windows Management Framework 5](https://aka.ms/wmf5latest) instalada.
1. Compie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para dar início à criação de metaconfigurações.

    > [!NOTE]
    > Os nomes de configuração do nó de configuração do estado são sensíveis a maiúsculas e minúsculas no portal Azure. Se o caso for incompatível, o nó não aparecerá sob a guia **Nodes.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Preencha a chave de registro e a URL para sua conta de Automação, bem como os nomes dos computadores a carregar. Todos os outros parâmetros são opcionais. Para encontrar a chave de registro e a URL de registro da sua conta de Automação, consulte o [Onboard com segurança usando a](#onboard-securely-by-using-registration) seção de registro.

1. Se você quiser que as máquinas reportem as informações de status do DSC à `ReportOnly` configuração do estado de automação do Azure, mas não à configuração de puxar ou módulos PowerShell, defina o parâmetro como *verdadeiro*.

1. Se `ReportOnly` não estiver definido, as máquinas reportam as informações de status do DSC para a configuração do estado de automação do Azure e para a configuração de puxar ou módulos PowerShell. Defina os parâmetros `ResourceRepositoryWeb`de `ReportServerWeb` acordo com os `ConfigurationRepositoryWeb`blocos e blocos.

1. Execute o script. Agora você deve ter uma pasta de diretório de trabalho chamada *DscMetaConfigs*, que contém as metaconfigurações do PowerShell DSC para as máquinas a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Gerar metaconfigurações de DSC usando cmdlets de automação do Azure

Se o padrão do PowerShell DSC LCM corresponder ao seu estojo de uso e você quiser que as máquinas de bordo puxem e reportem à Configuração do Estado de Automação do Azure, você poderá gerar as metaconfigurações necessárias do DSC mais simplesmente usando os cmdlets de Automação Do Azure.

1. Abra o console PowerShell ou visual Studio Code como administrador em uma máquina em seu ambiente local.
1. Conecte-se ao Azure Resource Manager usando [o Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. A partir da conta de Automação na qual você está configurando os nós, baixe as metaconfigurações do PowerShell DSC para as máquinas que deseja embarcar.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora você deve ter uma pasta *DscMetaConfigs* que contém as metaconfigurações do PowerShell DSC para as máquinas que você deseja a bordo (como administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>A bordo com segurança usando o registro

As máquinas podem ser embarcadas com segurança em uma conta do Azure Automation através do protocolo de registro DSC do Windows Management Framework 5. Este protocolo permite que um nó DSC seja autenticado em um servidor de puxar ou relatar O DSC do PowerShell, incluindo a configuração do estado de automação do Azure. O nó registra-se com o servidor na URL de registro e autentica usando uma chave de registro. 

Durante o registro, o nó DSC e o servidor de retirada/relatório DSC negociam um certificado exclusivo para o nó a ser usado para autenticação no pós-registro do servidor. Esse processo evita que os nódulos a bordo se representem um ao outro (por exemplo, se um nó estiver comprometido e se comportar maliciosamente). 

Após o registro, a chave de registro não é reutilizada para autenticação e é excluída do nó.

Para obter as informações necessárias para o protocolo de registro de configuração do Estado, acesse o portal do Azure e, em **Configurações de conta,** selecione **Chaves**. 

![Chaves de automação do Azure e URL no painel Chaves](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **URL de registro**: O valor na caixa **URL.**
- **Tecla de registro**: O valor na caixa **de tecla de acesso primário** ou na caixa de **tecla de acesso secundário.** Você pode usar qualquer chave.

Para maior segurança, você pode regenerar as chaves de acesso primárias e secundárias de uma conta de Automação a qualquer momento no painel **Chaves.** A regeneração das chaves impede que registros futuros de nó usem chaves anteriores.

## <a name="re-register-a-node"></a>Reregistre um nó

Depois de registrar uma máquina como um nó DSC na configuração do estado de automação do Azure, talvez seja necessário registrar esse nó no futuro por qualquer uma das seguintes razões:

- **Renovação de certificado**: Para versões do Windows Server anteriores ao Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo de autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não poderá se comunicar com o Azure Automation e está marcado *como Não Responsivo*. 

  Atualmente, o protocolo de registro do PowerShell DSC não pode renovar automaticamente os certificados quando eles estão próximos do vencimento, e você deve reregistrar os nós após um ano. Antes de reregistrar, certifique-se de que cada nó está executando o Windows Management Framework 5 RTM. 

    O recadastramento realizado 90 ou menos dias a partir do prazo de validade do certificado, ou em qualquer momento após o prazo de validade do certificado, resulta em um novo certificado sendo gerado e usado. Uma resolução para este problema está incluída no Windows Server 2019 e posteriormente.

- **Alterações nos valores de LCM DSC**: Você pode precisar alterar [os valores do PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) que foram definidos durante o registro inicial do nó (por exemplo, `ConfigurationMode`). Atualmente, você pode alterar esses valores de agente DSC apenas através do re-registro. A única exceção é o valor de configuração do nó atribuído ao nó. Você pode alterar esse valor no Azure Automation DSC diretamente.

Você pode reregistrar um nó assim como você registrou inicialmente, usando qualquer um dos métodos de onboarding descritos neste documento. Você não precisa cancelar o registro de um nó da configuração do estado de automação do Azure antes de registrá-lo.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Solucionar problemas de onboarding de máquina virtual do Azure

O Configuração do Estado de Automação do Azure permite a fácil integração de VMs do Microsoft Azure ao gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com a Configuração do Estado de Automação do Azure. Como a extensão Azure VM Desired State Configuration é executada de forma assíncrona, acompanhar seu progresso e solucionar problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de embarcar em um Azure Windows VM para a configuração do Estado de Automação do Azure que use a extensão de configuração de estado desejado do Azure VM pode levar até uma hora para a Azure Automation mostrar como registrado. Esse atraso resulta da instalação do Windows Management Framework 5 na VM pela extensão Azure VM Desired State Configuration, que é necessária para embarcar na configuração do Estado de Automação VM para Azure.

Para solucionar problemas ou visualizar o status da extensão de configuração do estado desejado do Azure VM:

1. No portal Azure, vá para a VM que está sendo a bordo.
2. Em **Configurações**, selecione **Extensões**. 
3. Selecione **DSC** ou **DSCForLinux,** dependendo do seu sistema operacional. 
4. Para obter mais informações sobre a extensão, **selecione Exibir status detalhado**.

Para obter mais informações sobre solução de [problemas, consulte Problemas de solução de problemas com o DSC (Desired State Configuration, configuração do estado desejado da automação do Azure).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Comece com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilar configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para um exemplo de usar a configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua em máquinas virtuais usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
