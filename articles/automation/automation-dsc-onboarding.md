---
title: Habilitar máquinas para gerenciamento pela configuração de estado da automação do Azure
description: Como configurar computadores para gerenciamento com a configuração de estado de automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 52cd72d1144fa2acad993e927d49545d645d596f
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993744"
---
# <a name="enable-machines-for-management-by-azure-automation-state-configuration"></a>Habilitar máquinas para gerenciamento pela configuração de estado da automação do Azure

Este tópico descreve como você pode configurar seus computadores para gerenciamento com a configuração de estado da automação do Azure. Para obter detalhes desse serviço, consulte [visão geral da configuração do estado de automação do Azure](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Habilitar VMs do Azure

A configuração de estado da automação do Azure permite que você habilite facilmente as VMs do Azure para gerenciamento de configuração, usando o portal do Azure, os modelos de Azure Resource Manager ou o PowerShell. Nos bastidores, e sem que um administrador tenha de ser remoto em uma VM, a extensão de configuração de estado desejado da VM do Azure registra a VM com a configuração de estado da automação do Azure. Como a extensão do Azure é executada de forma assíncrona, as etapas para acompanhar seu progresso ou solucionar problemas são fornecidas em [solucionar problemas de configuração de VM para configuração de estado](#troubleshoot-vm-setup-for-state-configuration).

> [!NOTE]
>A implantação da DSC em um nó do Linux usa a pasta **/tmp** Módulos como `nxautomation` são baixados temporariamente para verificação antes de serem instalados em seus locais apropriados. Para garantir que os módulos sejam instalados corretamente, o agente do Log Analytics para Linux precisa de permissões de leitura/gravação na pasta **/tmp**<br><br>
>O agente do Log Analytics para Linux é executado `omsagent` como o usuário. Para conceder >permissão de gravação para `omsagent` o usuário, execute o `setfacl -m u:omsagent:rwx /tmp`comando.

### <a name="enable-a-vm-using-azure-portal"></a>Habilitar uma VM usando portal do Azure

Para habilitar uma VM do Azure para estado de configuração por meio do [portal do Azure](https://portal.azure.com/):

1. Navegue até a conta de automação do Azure na qual habilitar as VMs. 

2. Na página configuração de estado, selecione a guia **nós** e clique em **Adicionar**.

3. Escolha uma VM para habilitar.

4. Se o computador não tiver a extensão de estado desejado do PowerShell instalada e o estado de energia estiver em execução, clique em **conectar**.

5. Em **registro**, insira os [valores de Configuration Manager local do DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) que são necessários para seu caso de uso. Opcionalmente, você pode inserir uma configuração de nó para atribuir à VM.

![Habilitando a VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Habilitar uma VM usando modelos de Azure Resource Manager

Você pode instalar e habilitar uma VM para configuração de estado usando modelos de Azure Resource Manager. Consulte [servidor gerenciado pelo serviço de configuração de estado desejado](https://azure.microsoft.com/resources/templates/101-automation-configuration/) para obter um modelo de exemplo que HABILITA uma VM existente para configuração de estado. Se você estiver gerenciando um conjunto de dimensionamento de máquinas virtuais, consulte o modelo de exemplo em [configuração do conjunto de dimensionamento de máquinas virtuais gerenciada pela automação do Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Habilitar computadores usando o PowerShell

Você pode usar o cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) no PowerShell para habilitar as VMs para a configuração de estado. 

> [!NOTE]
>O `Register-AzAutomationDscNode` cmdlet é implementado atualmente apenas para computadores que executam o Windows, pois ele dispara apenas a extensão do Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrar VMs em assinaturas do Azure

A melhor maneira de registrar VMs de outras assinaturas do Azure é usar a extensão de DSC em um modelo de implantação de Azure Resource Manager. Os exemplos são fornecidos na [extensão de configuração de estado desejado com modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Para localizar a chave de registro e a URL de registro para usar como parâmetros no modelo, consulte [habilitar máquinas com segurança usando o registro](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Habilitar computadores físicos/virtuais do Windows

Você pode habilitar os servidores Windows em execução localmente ou em outros ambientes de nuvem (incluindo instâncias de EC2 AWS) para a configuração de estado da automação do Azure. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) esteja instalada nos computadores para habilitar a configuração de estado. Além disso, o WMF 5 deve ser instalado no computador que você está usando para habilitar os computadores.
1. Siga as instruções em [gerar metaconfigurações de DSC](#generate-dsc-metaconfigurations) para criar uma pasta que contém as metaconfigurações de DSC necessárias. 
1. Use o cmdlet a seguir para aplicar as metaconfigurações do DSC do PowerShell remotamente aos computadores a serem habilitados. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie a pasta **metaconfigurações** para os computadores que você está habilitando. Em seguida, adicione o código para chamar [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nos computadores.
1. Usando o portal do Azure ou cmdlets, verifique se os computadores aparecem como nós de configuração de estado registrados em sua conta de automação do Azure.

## <a name="enable-physicalvirtual-linux-machines"></a>Habilitar computadores Linux físicos/virtuais

Você pode habilitar os servidores Linux em execução local ou em outros ambientes de nuvem para a configuração de estado. Os servidores devem ter [acesso de saída ao Azure](automation-dsc-overview.md#network-planning).

1. Verifique se a versão mais recente da [configuração de estado desejado do PowerShell para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) está instalada nos computadores para habilitar a configuração de estado.
2. Se os [padrões de Configuration Manager local do DSC do PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) corresponderem ao seu caso de uso e você quiser habilitar computadores para que eles sejam pull e relatem à configuração de Estado:

   - Em cada computador Linux para habilitar o, `Register.py` use para habilitar a máquina com os padrões de Configuration Manager de DSC local do PowerShell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Para localizar a chave de registro e a URL de registro para sua conta de automação, consulte [habilitar computadores com segurança usando o registro](#enable-machines-securely-using-registration).

3. Se os padrões do LCM (Configuration Manager local) do DSC do PowerShell não corresponderem ao seu caso de uso ou se você quiser habilitar computadores que só se reportam à configuração de estado da automação do Azure, siga as etapas 4-7. Caso contrário, vá diretamente para a etapa 7.

4. Siga as instruções na seção [gerar metaconfigurações de DSC](#generate-dsc-metaconfigurations) para produzir uma pasta que contém as metaconfigurações de DSC necessárias.

5. Certifique-se de que a versão mais recente do [WMF 5](https://aka.ms/wmf5latest) esteja instalada no computador que está sendo usado para habilitar seus computadores para configuração de estado.

6. Adicione o código da seguinte maneira para aplicar as metaconfigurações de DSC do PowerShell remotamente aos computadores a serem habilitados.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se você não puder aplicar as metaconfigurações do DSC do PowerShell remotamente, copie as metaconfigurações correspondentes às máquinas remotas da pasta descrita na etapa 4 para as máquinas Linux.

8. Adicione o código para `Set-DscLocalConfigurationManager.py` chamar localmente em cada computador Linux para habilitar a configuração de estado.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Usando o portal do Azure ou cmdlets, verifique se os computadores a serem habilitados agora aparecem como nós DSC registrados em sua conta de automação do Azure.

## <a name="generate-dsc-metaconfigurations"></a>Gerar metaconfigurações de DSC

Para habilitar qualquer computador para configuração de estado, você pode gerar uma [metaconfiguração de DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Essa configuração informa o agente DSC para efetuar pull de e/ou relatório para a configuração de estado da automação do Azure. Você pode gerar uma metaconfiguração de DSC para a configuração de estado de automação do Azure usando uma configuração DSC do PowerShell ou os cmdlets do PowerShell de automação do Azure.

> [!NOTE]
> Metaconfigurações de DSC contêm os segredos necessários para habilitar um computador em uma conta de automação para gerenciamento. Certifique-se de proteger corretamente quaisquer metaconfigurações de DSC que criar, ou exclua-as imediatamente após o uso.

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

1. Preencha a chave de registro e a URL para sua conta de automação, bem como os nomes dos computadores a serem habilitados. Todos os outros parâmetros são opcionais. Para localizar a chave de registro e a URL de registro para sua conta de automação, consulte [habilitar computadores com segurança usando o registro](#enable-machines-securely-using-registration).

1. Se você quiser que os computadores relatem informações de status de DSC para a configuração de estado de automação do Azure, mas não `ReportOnly` para configuração de pull ou módulos do PowerShell, defina o parâmetro como true.

1. Se `ReportOnly` não estiver definido, os computadores relatarão informações de status de DSC para configuração de estado de automação do Azure e configuração de pull ou módulos do PowerShell. Defina os `ConfigurationRepositoryWeb`parâmetros adequadamente nos blocos `ResourceRepositoryWeb`, e `ReportServerWeb` .

1. Execute o script. Agora você deve ter uma pasta de diretório de trabalho chamada **DscMetaConfigs**, que contém as metaconfigurações de DSC do PowerShell para as máquinas a serem habilitadas (como um administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Gerar metaconfigurações de DSC usando cmdlets de automação do Azure

Se os padrões de LCM do PowerShell DSC corresponderem ao seu caso de uso e você quiser habilitar computadores para a configuração de envio e relatório para a automação do Azure, você poderá gerar as metaconfigurações de DSC necessárias mais simplesmente usando os cmdlets de automação do Azure.

1. Abra o console do PowerShell ou VSCode como administrador em um computador em seu ambiente local.
2. Conecte-se a Azure Resource Manager usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Baixe as metaconfigurações de DSC do PowerShell para os computadores que você deseja habilitar da conta de automação na qual você está configurando nós.

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

1. Agora você deve ter uma pasta **DscMetaConfigs** que contém as metaconfigurações de DSC do PowerShell para que os computadores habilitem (como um administrador).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Habilitar computadores com segurança usando o registro

Você pode habilitar computadores com segurança para uma conta de automação do Azure por meio do protocolo de registro de DSC do WMF 5. Esse protocolo permite que um nó DSC autentique em um servidor de pull ou de relatório DSC do PowerShell, incluindo a configuração de estado da automação do Azure. O nó registra com o servidor na URL de registro e se autentica usando uma chave de registro. Durante o registro, o nó DSC e o servidor de pull/relatório de DSC negociam um certificado exclusivo para o nó a ser usado para autenticação no registro posterior do servidor. Esse processo impede que os nós habilitados determinem um ao outro, por exemplo, se um nó estiver comprometido e se comportando de forma mal-intencionada. Após o registro, a chave de registro não é usada novamente para autenticação e é excluída do nó.

Você pode obter as informações necessárias para o protocolo de registro da Configuração do Estado a partir das **Chaves** nas **Configurações de Conta**no portal do Azure. 

![Chaves de automação do Azure e a URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL de registro é o campo URL na página chaves.
- Chave de registro é o valor do campo **chave de acesso primário** ou o campo **chave de acesso secundária** na página chaves. Qualquer chave pode ser usada.

Para maior segurança, você pode regenerar as chaves de acesso primária e secundária de uma conta de automação a qualquer momento na página chaves. A regeneração de chave impede que os registros de nó futuros usem as chaves anteriores.

## <a name="re-register-a-node"></a>Registrar novamente um nó

Depois de registrar um computador como um nó DSC na configuração de estado da automação do Azure, há várias razões pelas quais você pode precisar registrar novamente esse nó no futuro.

- **Renovação de certificado.** Para versões do Windows Server anteriores ao Windows Server 2019, cada nó negocia automaticamente um certificado exclusivo para autenticação que expira após um ano. Se um certificado expirar sem renovação, o nó não poderá se comunicar com a automação do Azure e `Unresponsive`será marcado. Atualmente, o protocolo de registro DSC do PowerShell não pode renovar automaticamente os certificados quando eles estão se aproximando da expiração e você deve registrar novamente os nós após a hora de um ano. Antes de registrar novamente, verifique se cada nó está executando o WMF 5 RTM. 

    O novo registro realizado em 90 dias ou menos do tempo de expiração do certificado, ou a qualquer momento após o tempo de expiração do certificado, resulta em uma nova geração e uso do certificado. Uma resolução para esse problema está incluída no Windows Server 2019 e posterior.

- **Alterações nos valores de LCM do DSC.** Talvez seja necessário alterar [os valores do LCM do PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) definidos durante o registro inicial do nó, por `ConfigurationMode`exemplo,. No momento, você só pode alterar esses valores do agente DSC por meio do novo registro. A única exceção é o valor de configuração de nó atribuído ao nó. Você pode alterar isso no Azure DSC de Automação diretamente.

Você pode registrar novamente um nó da mesma forma como registrou o nó inicialmente, usando qualquer um dos métodos descritos neste documento. Você não precisa cancelar o registro de um nó da configuração de estado da automação do Azure antes de registrá-lo novamente.

## <a name="troubleshoot-vm-setup-for-state-configuration"></a>Solucionar problemas de configuração de VM para o estado

A configuração de estado permite que você habilite facilmente VMs do Windows do Azure para gerenciamento de configuração. Nos bastidores, a extensão de Configuração de Estado Desejado da VM do Azure é usada para registrar a VM com a Configuração do Estado de Automação do Azure. Como a extensão de configuração de estado desejado da VM do Azure é executada de forma assíncrona, acompanhar seu progresso e solucionar problemas de sua execução pode ser importante.

> [!NOTE]
> Qualquer método de habilitar VMs do Windows do Azure para configuração de estado que usa a extensão de configuração de estado desejado da VM do Azure pode levar até uma hora para a automação do Azure mostrar as VMs como registradas. Esse atraso ocorre devido à instalação do WMF 5 na VM pela extensão de configuração de estado desejado da VM do Azure, que é necessária para habilitar as VMs para a configuração de estado.

Para solucionar problemas ou exibir o status da extensão de configuração de estado desejado da VM do Azure:

1. Na portal do Azure, navegue até a VM que está sendo habilitada.
2. Clique em **extensões** em **configurações**. 
3. Agora, selecione **DSC** ou **DSCForLinux**, dependendo do seu sistema operacional. 
4. Para obter mais detalhes, você pode clicar em **Exibir status detalhado**.

Para obter mais informações sobre solução de problemas, consulte [solucionar problemas de configuração de estado da automação do Azure](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para obter um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [exemplo de uso: implantação contínua em máquinas virtuais usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
