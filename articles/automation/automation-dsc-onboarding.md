---
title: Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure
description: Como configurar computadores para gerenciamento com a configuração de estado de automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457732"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Por que gerenciar máquinas com a Configuração do Estação de Automação do Azure?

A configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ele é acessado no portal do Azure selecionando **configuração de estado (DSC)** em **Gerenciamento de configuração**. 

Esse serviço permite a escalabilidade entre milhares de computadores de forma rápida e fácil a partir de um local central e seguro. Você pode facilmente integrar computadores, atribuir a eles configurações declarativas e exibir relatórios mostrando a conformidade de cada computador com o estado desejado especificado.

O serviço de configuração de estado da automação do Azure é para DSC quais runbooks de automação do Azure são para scripts do PowerShell. Em outras palavras, da mesma forma que a Automação do Azure ajuda você a gerenciar os scripts do PowerShell, ela também ajuda a gerenciar configurações do DSC. Para saber mais sobre os benefícios de usar a Configuração de Estado de Automação do Azure, consulte [Visão geral da Configuração de Estado de Automação do Azure](automation-dsc-overview.md).

A Configuração do Estado de Automação do Azure pode ser usada para gerenciar uma variedade de máquinas:

- Máquinas Virtuais do Azure
- Máquinas virtuais do Azure (clássico)
- Máquinas físicas/virtuais do Windows locais ou em uma nuvem diferente do Azure (incluindo instâncias AWS EC2)
- Máquinas físicas/virtuais locais do Linux, no Azure, ou em uma nuvem diferente do Azure

Se você não estiver pronto para gerenciar a configuração da máquina na nuvem, poderá usar a configuração de estado da automação do Azure como um ponto de extremidade somente de relatório. Esse recurso permite definir (enviar por push) configurações por meio de DSC e exibir detalhes de relatório na automação do Azure.

> [!NOTE]
> O gerenciamento de VMs do Azure com a configuração de estado de automação do Azure é incluído sem custo adicional se a versão da extensão de configuração de estado desejado da VM do Azure for maior que 2,70. Para obter mais informações, consulte a [**página de preços de automação**](https://azure.microsoft.com/pricing/details/automation/).

As seções a seguir deste artigo descrevem como você pode integrar as máquinas listadas acima para a configuração de estado da automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Integração de VMs do Azure

A configuração de estado da automação do Azure permite que você integre VMs do Azure com facilidade para gerenciamento de configuração, usando o portal do Azure, os modelos de Azure Resource Manager ou o PowerShell. Nos bastidores, e sem que um administrador tenha de ser remoto em uma VM, a extensão de configuração de estado desejado da VM do Azure registra a VM com a configuração de estado da automação do Azure. Como a extensão do Azure é executada de forma assíncrona, as etapas para acompanhar seu progresso ou solucionar problemas são fornecidas na seção [solução de problemas de integração de máquina virtual do Azure](#troubleshooting-azure-virtual-machine-onboarding) deste artigo.

> [!NOTE]
>A implantação da DSC em um nó do Linux usa a pasta **/tmp** Módulos como `nxautomation` são baixados temporariamente para verificação antes de serem instalados em seus locais apropriados. Para garantir que os módulos sejam instalados corretamente, o agente do Log Analytics para Linux precisa de permissões de leitura/gravação na pasta **/tmp**<br><br>
>O agente do Log Analytics para Linux é executado `omsagent` como o usuário. Para conceder >permissão de gravação para `omsagent` o usuário, execute o `setfacl -m u:omsagent:rwx /tmp`comando.

### <a name="onboard-a-vm-using-azure-portal"></a>Carregar uma VM usando portal do Azure

Para carregar uma VM do Azure para a configuração de estado da automação do Azure por meio do [portal do Azure](https://portal.azure.com/):

1. Navegue até a conta de automação do Azure na qual carregar as VMs. 

2. Na página configuração de estado, selecione a guia **nós** e clique em **Adicionar**.

3. Escolha uma VM para carregar.

4. Se o computador não tiver a extensão de estado desejado do PowerShell instalada e o estado de energia estiver em execução, clique em **conectar**.

5. Em **registro**, insira os [valores de Configuration Manager local do DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) que são necessários para seu caso de uso. Opcionalmente, você pode inserir uma configuração de nó para atribuir à VM.

![integração](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Carregar uma VM usando modelos de Azure Resource Manager

Você pode implantar e carregar uma VM para a configuração de estado da automação do Azure usando modelos de Azure Resource Manager. Consulte [servidor gerenciado pelo serviço de configuração de estado desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para obter um modelo de exemplo que integra uma VM existente à configuração de estado de automação do Azure. Se você estiver gerenciando um conjunto de dimensionamento de máquinas virtuais, consulte o modelo de exemplo em [configuração do conjunto de dimensionamento de máquinas virtuais gerenciada pela automação do Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Carregar computadores usando o PowerShell

Você pode usar o cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) no PowerShell para carregar VMs para a configuração de estado da automação do Azure. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para computadores que executam o Windows, pois ele dispara apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrar VMs em assinaturas do Azure

A melhor maneira de registrar VMs de outras assinaturas do Azure é usar a extensão de DSC em um modelo de implantação de Azure Resource Manager. Os exemplos são fornecidos na [extensão de configuração de estado desejado com modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para localizar a chave de registro e a URL de registro para usar como parâmetros no modelo, consulte a seção [integração segura usando o registro](#onboarding-securely-using-registration) neste artigo.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Integração de máquinas físicas/virtuais do Windows

Você pode integrar servidores Windows em execução localmente ou em outros ambientes de nuvem (incluindo instâncias AWS EC2) à configuração de estado da automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) esteja instalada nos computadores para carregar a configuração de estado da automação do Azure. Além disso, o WMF 5 deve ser instalado no computador que você está usando para a operação de integração.
1. Siga as instruções na seção [gerando metaconfigurações DSC](#generating-dsc-metaconfigurations) para criar uma pasta que contém as metaconfigurações de DSC necessárias. 
1. Use o cmdlet a seguir para aplicar as metaconfigurações de DSC do PowerShell remotamente aos computadores que você deseja carregar. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta **metaconfigurações** para os computadores que você está integrando. Em seguida, adicione o código para chamar [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nos computadores.
1. Usando o portal do Azure ou cmdlets, verifique se os computadores a serem integrados aparecem como nós de configuração de estado registrados em sua conta de automação do Azure.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Integração de máquinas físicas/virtuais Linux

Você pode integrar servidores Linux em execução local ou em outros ambientes de nuvem à configuração de estado da automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Verifique se a versão mais recente da [configuração de estado desejado do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nos computadores para carregar a configuração de estado da automação do Azure.
2. Se os [padrões de Configuration Manager local do DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponderem ao seu caso de uso e você quiser carregar computadores para que eles sejam extraídos e relatem à configuração de estado da automação do Azure:

   - Em cada computador Linux para carregar a configuração de estado da automação do Azure `Register.py` , use para carregar usando os padrões de Configuration Manager local do DSC do PowerShell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para localizar a chave de registro e a URL de registro para sua conta de automação, consulte a seção [integração segura usando o registro](#onboarding-securely-using-registration) deste artigo.

3. Se os padrões do LCM (Configuration Manager local) do DSC do PowerShell não corresponderem ao seu caso de uso ou se você quiser carregar computadores que só relatam à configuração do estado de automação do Azure, siga as etapas 4-7. Caso contrário, vá diretamente para a etapa 7.

4. Siga as instruções na seção [gerando metaconfigurações de DSC](#generating-dsc-metaconfigurations) para produzir uma pasta que contém as metaconfigurações de DSC necessárias.

5. Verifique se a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) está instalada no computador que está sendo usado para integração.

6. Adicione o código da seguinte maneira para aplicar as metaconfigurações de DSC do PowerShell remotamente aos computadores que você deseja carregar.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie as metaconfigurações correspondentes às máquinas remotas da pasta descrita na etapa 4 para as máquinas Linux.

8. Adicione o código para `Set-DscLocalConfigurationManager.py` chamar localmente em cada computador Linux para carregar a configuração de estado da automação do Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Usando o portal do Azure ou cmdlets, certifique-se de que os computadores para carregar agora aparecem como nós de DSC registrados em sua conta de automação do Azure.

## <a name="generating-dsc-metaconfigurations"></a>Gerando metaconfigurações DSC

Para carregar qualquer máquina para a configuração de estado da automação do Azure, você pode gerar uma [metaconfiguração de DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Essa configuração informa o agente DSC para efetuar pull de e/ou relatório para a configuração de estado da automação do Azure. Você pode gerar uma metaconfiguração de DSC para a configuração de estado de automação do Azure usando uma configuração DSC do PowerShell ou os cmdlets do PowerShell de automação do Azure.

> [!NOTE]
> Metaconfigurações DSC contêm os segredos necessários para carregar um computador em uma conta de Automação para gerenciamento. Certifique-se de proteger corretamente quaisquer metaconfigurações de DSC que criar, ou exclua-as imediatamente após o uso.

O suporte de proxy para metaconfigurações é controlado pelo LCM, que é o mecanismo de DSC do Windows PowerShell. O LCM é executado em todos os nós de destino e é responsável por chamar os recursos de configuração que estão incluídos em um script de metaconfiguração do DSC. Você pode incluir suporte a proxy em uma metaconfiguração, incluindo definições da URL do proxy e da credencial de proxy conforme `ConfigurationRepositoryWeb`necessário `ResourceRepositoryWeb`nos blocos `ReportServerWeb` , e. Consulte [Configurando o Configuration Manager local](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Gerar metaconfigurações de DSC usando uma configuração DSC

1. Abra VSCode (ou seu editor favorito) como administrador em um computador em seu ambiente local. O computador também deve ter a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) instalada.
1. Compie o script a seguir localmente. Esse script contém uma configuração DSC do PowerShell para criar metaconfigurações e um comando para dar início à criação de metaconfigurações.

    > [!NOTE]
    > Nomes de configuração de nó de configuração de estado diferenciam maiúsculas de minúsculas no portal do Azure. Se o caso for incompatível, o nó não aparecerá na guia **nós** .

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

1. Preencha a chave de registro e a URL para sua conta de Automação, bem como os nomes dos computadores a carregar. Todos os outros parâmetros são opcionais. Para localizar a chave de registro e a URL de registro para sua conta de automação, consulte a seção [integração segura usando o registro](#onboarding-securely-using-registration) .

1. Se você quiser que os computadores relatem informações de status de DSC para a configuração de estado de automação do Azure, mas não `ReportOnly` para configuração de pull ou módulos do PowerShell, defina o parâmetro como true.

1. Se `ReportOnly` não estiver definido, os computadores relatarão informações de status de DSC para configuração de estado de automação do Azure e configuração de pull ou módulos do PowerShell. Defina os `ConfigurationRepositoryWeb`parâmetros adequadamente nos blocos `ResourceRepositoryWeb`, e `ReportServerWeb` .

1. Execute o script. Agora você deve ter uma pasta de diretório de trabalho chamada **DscMetaConfigs**, que contém as metaconfigurações de DSC do PowerShell para que as máquinas sejam integradas (como um administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar metaconfigurações de DSC usando cmdlets de automação do Azure

Se os padrões de LCM de DSC do PowerShell corresponderem ao seu caso de uso e você quiser integrar computadores tanto para a configuração de estado de resumir quanto para a automação do Azure, poderá gerar as metaconfigurações de DSC necessárias mais simplesmente usando os cmdlets de automação do Azure.

1. Abra o console do PowerShell ou VSCode como administrador em um computador em seu ambiente local.
2. Conecte-se a Azure Resource Manager usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Baixe as metaconfigurações de DSC do PowerShell para os computadores que você deseja carregar da conta de automação na qual você está configurando nós.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Agora você deve ter uma pasta **DscMetaConfigs** que contém as metaconfigurações de DSC do PowerShell para que os computadores sejam integrados (como um administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Integração com segurança usando o registro

Os computadores podem ser integrados com segurança a uma conta de automação do Azure por meio do protocolo de registro do WMF 5 DSC. Esse protocolo permite que um nó DSC autentique em um servidor de pull ou de relatório DSC do PowerShell, incluindo a configuração de estado da automação do Azure. O nó registra com o servidor na URL de registro e se autentica usando uma chave de registro. Durante o registro, o nó DSC e o servidor de pull/relatório de DSC negociam um certificado exclusivo para o nó a ser usado para autenticação no registro posterior do servidor. Esse processo impede que nós integrados se representem um do outro, por exemplo, se um nó estiver comprometido e se comportando de forma mal-intencionada. Após o registro, a chave de registro não é usada novamente para autenticação e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro da Configuração do Estado a partir das **Chaves** nas **Configurações de Conta**no portal do Azure. 

![Chaves de automação do Azure e a URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registro é o campo URL na página chaves.
- Chave de registro é o valor do campo **chave de acesso primário** ou o campo **chave de acesso secundária** na página chaves. Qualquer chave pode ser usada.

Para maior segurança, você pode regenerar as chaves de acesso primária e secundária de uma conta de automação a qualquer momento na página chaves. A regeneração de chave impede que os registros de nó futuros usem as chaves anteriores.

## <a name="re-registering-a-node"></a>Registrando novamente um nó

Depois de registrar um computador como um nó DSC na configuração de estado da automação do Azure, há várias razões pelas quais você pode precisar registrar novamente esse nó no futuro.

- **Renovação de certificado.** Para versões do Windows Server anteriores ao Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo para autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não poderá se comunicar com a automação do Azure e `Unresponsive`será marcado. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente os certificados quando eles estão se aproximando da expiração e você deve registrar novamente os nós após a hora de um ano. Antes de registrar novamente, verifique se cada nó está executando o WMF 5 RTM. 

    O novo registro realizado em 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, resulta em uma nova geração e uso do certificado. Uma resolução para esse problema está incluída no Windows Server 2019 e posterior.

- **Alterações nos valores de LCM do DSC.** Talvez seja necessário alterar [os valores do LCM do PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) definidos durante o registro inicial do nó, por `ConfigurationMode`exemplo,. No momento, você só pode alterar esses valores do agente DSC por meio do novo registro. A única exceção é o valor de configuração de nó atribuído ao nó. Você pode alterar isso no Azure DSC de Automação diretamente.

Você pode registrar novamente um nó da mesma forma como registrou o nó inicialmente, usando qualquer um dos métodos de integração descritos neste documento. Você não precisa cancelar o registro de um nó da configuração de estado da automação do Azure antes de registrá-lo novamente.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Solução de problemas de integração de máquina virtual do Azure

O Configuração do Estado de Automação do Azure permite a fácil integração de VMs do Microsoft Azure ao gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com a Configuração do Estado de Automação do Azure. Como a extensão de configuração de estado desejado da VM do Azure é executada de forma assíncrona, acompanhar seu progresso e solucionar problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de integração de uma VM Windows do Azure à configuração de estado de automação do Azure que usa a extensão de configuração de estado desejado da VM do Azure pode levar até uma hora para a automação do Azure mostrá-la como registrado. Esse atraso ocorre devido à instalação do WMF 5 na VM pela extensão de configuração de estado desejado da VM do Azure, que é necessária para integrar a configuração de estado da VM para a automação do Azure.

Para solucionar problemas ou exibir o status da extensão de configuração de estado desejado da VM do Azure:

1. Na portal do Azure, navegue até a VM que está sendo integrada.
2. Clique em **extensões** em **configurações**. 
3. Agora, selecione **DSC** ou **DSCForLinux**, dependendo do seu sistema operacional. 
4. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

Para obter mais informações sobre solução de problemas, consulte [Solucionando problemas com a configuração de estado desejado (DSC) da automação do Azure](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, consulte [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para obter um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [exemplo de uso: implantação contínua em máquinas virtuais usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
