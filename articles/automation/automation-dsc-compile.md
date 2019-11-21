---
title: Configurações de compilação na Configuração do Estado de Automação do Azure
description: Este artigo descreve como compilar as configurações da DSC (Configuração de Estado Desejado) para Automação do Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d3cca9d8954d9ac158d88b393c46672da3faa19
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231710"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilação das configurações DSC na Configuração do Estado de Automação do Azure

You can compile Desired State Configuration (DSC) configurations in two ways with Azure Automation State Configuration: in Azure and in Windows PowerShell. A seguinte tabela ajuda você a determinar quando usar qual método com base nas características de cada um:

- Azure State Configuration compilation service
  - Beginner method with interactive user interface
   - Acompanhar facilmente o estado do trabalho

- Windows PowerShell
  - Call from Windows PowerShell on local workstation or build service
  - Integrate with development test pipeline
  - Provide complex parameter values
  - Work with node and non-node data at scale
  - Significant performance improvement

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compiling a DSC Configuration in Azure State Configuration

### <a name="portal"></a>Portal

1. Em sua conta de Automação, clique em **Configuração de estado (DSC)** .
1. Clique na guia **Configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a folha **Compilar Configuração** será aberta para que você possa fornecer os valores de parâmetro. Consulte a seguinte seção de [**Parâmetros Básicos**](#basic-parameters) para obter mais detalhes sobre os parâmetros.
1. A página **Trabalho de Compilação** é aberta para que você possa acompanhar o status do trabalho de compilação e as configurações de nó (documentos de configuração do MOF) que ele colocou no Servidor de Recepção de Configuração do Estado de Automação do Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Você pode usar [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para acompanhar seu status. Você pode usar esse objeto de trabalho de compilação com [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
para determinar o status do trabalho de compilação, e [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
para exibir seus fluxos (saída). O código de exemplo a seguir inicia a compilação da configuração **SampleConfig** , aguarda até a sua conclusão e exibe suas transmissões.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Parâmetros básicos

A declaração de parâmetro nas configurações DSC, incluindo tipos de parâmetros e propriedades, funciona da mesma maneira que os runbooks da Automação do Azure. Veja [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros de runbook.

O exemplo a seguir usa dois parâmetros denominados **FeatureName** e **IsPresent** para determinar os valores das propriedades na configuração de nó **ParametersExample.sample** gerada durante a compilação.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

É possível compilar as Configurações DSC que usam parâmetros básicos no portal de Configuração do Estado de Automação do Azure ou com o Azure PowerShell:

#### <a name="portal"></a>Portal

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![Parâmetros de configuração de compilação](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

O PowerShell requer parâmetros em uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) em que a chave corresponda ao nome do parâmetro e o valor seja igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre como transmitir PSCredentials como parâmetros, confira [Ativos de credencial](#credential-assets) abaixo.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compiling configurations in Azure Automation that contain Composite Resources

**Recursos de Composição** permitem que você use as configurações DSC como recursos aninhados dentro de uma configuração. Isso permite que você aplique várias configurações para um único recurso. Consulte [recursos de composição: utilizando uma configuração DSC como um recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para saber mais sobre **Recursos de Composição**.

> [!NOTE]
> In order for configurations containing **Composite Resources** to compile correctly, you must first ensure that any DSC Resources that the composite relies on are first imported in to Azure Automation.

Adding a DSC **Composite Resource** is no different than adding any PowerShell module to Azure Automation.
The step by step instruction for this process is documented in the article [Manage Modules in Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Managing ConfigurationData when compiling configuration in Azure Automation

**ConfigurationData** permite que você separe a configuração estrutural de qualquer configuração específica do ambiente usando a DSC do PowerShell. Veja [Separar “O que” de “Onde” na DSC do PowerShell](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> You can use **ConfigurationData** when compiling in Azure Automation State Configuration using Azure PowerShell but not in the Azure portal.

A seguinte configuração de exemplo DSC usa **ConfigurationData** por meio das palavras-chave **$ConfigurationData** e **$AllNodes**. Também é necessário o[**módulo** xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

You can compile the preceding DSC configuration with Windows PowerShell. The following script adds two node configurations to the Azure Automation State Configuration Pull Service: **ConfigurationDataSample.MyVM1** and **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Working with Assets in Azure Automation during compilation

As referências de ativos são as mesmas na Configuração do Estado de Automação do Azure e runbooks. Para saber mais, confira o seguinte:

- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

#### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na Automação do Azure podem fazer referência a ativos de credencial de automação usando `Get-AutomationPSCredential` experiência. Se uma configuração tem um parâmetro que tem um tipo **PSCredential**, em seguida, você pode usar o cmdlet `Get-AutomationPSCredential`, passando o nome de cadeia de caracteres de um ativo de credencial de Automação do Azure para o cmdlet para recuperar a credencial. Você pode usar esse objeto para o parâmetro que exige o objeto **PSCredential**. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e passado para a configuração. O exemplo a seguir mostra isso em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. No entanto, atualmente, você deve informar a DSC do PowerShell que as credenciais podem ser exportadas em texto sem formatação durante a geração do MOF da configuração de nó, pois a DSC do PowerShell não está ciente de que a Automação do Azure criptografará todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

You can tell PowerShell DSC that it is okay for credentials to be outputted in plain text in the generated node configuration MOFs using Configuration Data. Você deve transmitir `PSDscAllowPlainTextPassword = $true` por meio do **ConfigurationData** para o nome do bloco de cada nó que aparece na configuração DSC e que usa credenciais.

O exemplo a seguir mostra uma configuração DSC que usa um ativo de credencial da Automação.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Você pode compilar a configuração da DSC acima com o PowerShell. O PowerShell abaixo adiciona duas configurações de nó ao Servidor de Pull da Configuração do Estado de Automação do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Após a conclusão da compilação, você poderá receber um erro informando: **O módulo 'Microsoft.PowerShell.Management' não foi importado porque o snap-in 'Microsoft.PowerShell.Management' já foi importado.** Esse aviso pode ser ignorado sem problemas.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compiling configurations in Windows PowerShell and publishing to Azure Automation

Você também pode importar as configurações de nó (MOFs) que você compilou fora do Azure.
This includes compiling from a developer workstation or in a service such as [Azure DevOps](https://dev.azure.com).
There are multiple advantages to this approach including performance and reliability.
Compiling in Windows PowerShell also provides the option to sign configuration content.
Uma configuração de nó assinada é verificada localmente em um nó gerenciado pelo agente de DSC, garantindo que a configuração aplicada ao nó seja proveniente de uma origem autorizada.

> [!NOTE]
> Um arquivo de configuração de nó não deve ultrapassar 1 MB, para poder ser importado na Automação do Azure.

Para obter mais informações sobre como assinar configurações de nó, consulte [melhorias no WMF 5.1 - como a configuração de entrada e o módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compiling a configuration in Windows PowerShell

The process to compile DSC configurations in Windows PowerShell is included in the PowerShell DSC documentation [Write, Compile, and Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
This can be executed from a developer workstation or within a build service such as [Azure DevOps](https://dev.azure.com).

The MOF file or files produced by compiling the configuration can then be imported directly in to the Azure State Configuration service.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no Portal do Azure

1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página **Configuração de estado (DSC)** , clique na guia **Configurações** e, em seguida, em **+ Adicionar**.
1. Na página **Importar**, clique no ícone de pasta próximo à caixa de texto **Arquivo de Configuração de Nó** para procurar um arquivo de configuração de nó (MOF) no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Insira um nome na caixa de texto **Nome da Configuração**. Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importing a node configuration with Azure PowerShell

Use o cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) para importar uma configuração de nó para a conta de automação.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
