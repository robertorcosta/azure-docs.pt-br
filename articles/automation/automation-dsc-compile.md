---
title: Configurações de compilação na Configuração do Estado de Automação do Azure
description: Este artigo descreve como compilar as configurações da DSC (Configuração de Estado Desejado) para Automação do Azure.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: 5462d0fae44217f6217d5be1b321df53c4706aaa
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430560"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilação das configurações DSC na Configuração do Estado de Automação do Azure

Você pode compilar configurações de DSC (configuração de estado desejado) de duas maneiras com a configuração de estado da automação do Azure: no Azure e no Windows PowerShell. A seguinte tabela ajuda você a determinar quando usar qual método com base nas características de cada um:

- Serviço de compilação de configuração de estado do Azure
  - Método para iniciantes com interface do usuário interativa
   - Acompanhar facilmente o estado do trabalho

- Windows PowerShell
  - Chamada do Windows PowerShell na estação de trabalho local ou serviço de compilação
  - Integrar com pipeline de teste de desenvolvimento
  - Fornecer valores de parâmetro complexos
  - Trabalhar com dados de nó e não nó em escala
  - Melhoria significativa no desempenho

Para obter detalhes de compilação, consulte [extensão de configuração de estado desejado com modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilando uma configuração DSC na configuração de estado do Azure

### <a name="portal"></a>Portal

1. Em sua conta de Automação, clique em **Configuração de estado (DSC)** .
1. Clique na guia **Configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a folha de **configuração de compilação** será aberta para que você possa fornecer valores de parâmetro.
1. A página **Trabalho de Compilação** é aberta para que você possa acompanhar o status do trabalho de compilação e as configurações de nó (documentos de configuração do MOF) que ele colocou no Servidor de Recepção de Configuração do Estado de Automação do Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Você pode usar [`Start-AzAutomationDscCompilationJob`](/powershell/module/az.automation/start-azautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para acompanhar seu status. Você pode usar esse objeto de trabalho de compilação com [`Get-AzAutomationDscCompilationJob`](/powershell/module/az.automation/get-azautomationdsccompilationjob)
para determinar o status do trabalho de compilação, e [`Get-AzAutomationDscCompilationJobOutput`](/powershell/module/az.automation/get-azautomationdscconfiguration)
para exibir seus fluxos (saída). O código de exemplo a seguir inicia a compilação da configuração **SampleConfig** , aguarda até a sua conclusão e exibe suas transmissões.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Declarar parâmetros básicos

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre como transmitir PSCredentials como parâmetros, confira [Ativos de credencial](#credential-assets) abaixo.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilar configurações que contêm recursos de composição na automação do Azure

O recurso de **recursos de composição** permite que você use configurações de DSC como recursos aninhados dentro de uma configuração. Isso permite que você aplique várias configurações para um único recurso. Consulte [recursos de composição: usando uma configuração DSC como um recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para saber mais sobre os recursos de composição.

> [!NOTE]
> Para configurações que contêm **recursos de composição** para serem compilados corretamente, primeiro você deve garantir que todos os recursos de DSC dos quais a composição depende sejam importados primeiro na automação do Azure.

Adicionar um **recurso de composição** de DSC não é diferente de adicionar qualquer módulo do PowerShell à automação do Azure.
A instrução passo a passo para esse processo está documentada no artigo [gerenciar módulos na automação do Azure](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gerenciar ConfigurationData ao compilar configurações na automação do Azure

**ConfigurationData** permite que você separe a configuração estrutural de qualquer configuração específica do ambiente usando a DSC do PowerShell. Veja [Separar “O que” de “Onde” na DSC do PowerShell](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> Você pode usar o **ConfigurationData** ao compilar na configuração de estado da automação do Azure usando Azure PowerShell, mas não no portal do Azure.

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

Você pode compilar a configuração de DSC anterior com o Windows PowerShell. O script a seguir adiciona duas configurações de nó ao serviço de pull de configuração de estado da automação do Azure: **ConfigurationDataSample. MyVM1** e **ConfigurationDataSample. MyVM3**:

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Trabalhar com ativos na automação do Azure durante a compilação

As referências de ativos são as mesmas na Configuração do Estado de Automação do Azure e runbooks. Para saber mais, consulte o seguinte:

- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

#### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na Automação do Azure podem fazer referência a ativos de credencial de automação usando `Get-AutomationPSCredential` experiência. Se uma configuração tem um parâmetro que tem um tipo **PSCredential**, em seguida, você pode usar o cmdlet `Get-AutomationPSCredential`, passando o nome de cadeia de caracteres de um ativo de credencial de Automação do Azure para o cmdlet para recuperar a credencial. Você pode usar esse objeto para o parâmetro que exige o objeto **PSCredential**. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e passado para a configuração. O exemplo a seguir mostra isso em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. No entanto, atualmente, você deve informar a DSC do PowerShell que as credenciais podem ser exportadas em texto sem formatação durante a geração do MOF da configuração de nó, pois a DSC do PowerShell não está ciente de que a Automação do Azure criptografará todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

Você pode informar ao DSC do PowerShell que é possível que as credenciais sejam emitidas em texto sem formatação no MOFs de configuração de nó gerado usando dados de configuração. Você deve transmitir `PSDscAllowPlainTextPassword = $true` por meio do **ConfigurationData** para o nome do bloco de cada nó que aparece na configuração DSC e que usa credenciais.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Após a conclusão da compilação, você poderá receber um erro informando: **O módulo 'Microsoft.PowerShell.Management' não foi importado porque o snap-in 'Microsoft.PowerShell.Management' já foi importado.** Esse aviso pode ser ignorado sem problemas.

## <a name="compiling-configurations-in-windows-powershell"></a>Compilando configurações no Windows PowerShell

Você também pode importar as configurações de nó (MOFs) que você compilou fora do Azure.
Isso inclui a compilação de uma estação de trabalho de desenvolvedor ou em um serviço como o [Azure DevOps](https://dev.azure.com).
Há várias vantagens para essa abordagem, incluindo desempenho e confiabilidade.
A compilação no Windows PowerShell também fornece a opção de assinar o conteúdo de configuração.
Uma configuração de nó assinada é verificada localmente em um nó gerenciado pelo agente de DSC, garantindo que a configuração aplicada ao nó seja proveniente de uma origem autorizada.

> [!NOTE]
> Um arquivo de configuração de nó não deve ultrapassar 1 MB, para poder ser importado na Automação do Azure.

Para obter mais informações sobre como assinar configurações de nó, consulte [melhorias no WMF 5.1 - como a configuração de entrada e o módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-a-configuration-in-windows-powershell"></a>Compilar uma configuração no Windows PowerShell

O processo para compilar configurações DSC no Windows PowerShell está incluído na documentação do DSC do PowerShell [gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Isso pode ser executado em uma estação de trabalho de desenvolvedor ou em um serviço de compilação, como o [Azure DevOps](https://dev.azure.com).

O arquivo MOF ou arquivos produzidos pela compilação da configuração podem então ser importados diretamente no serviço de configuração de estado do Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal do Azure

1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página **Configuração de estado (DSC)** , clique na guia **Configurações** e, em seguida, em **+ Adicionar**.
1. Na página **Importar**, clique no ícone de pasta próximo à caixa de texto **Arquivo de Configuração de Nó** para procurar um arquivo de configuração de nó (MOF) no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Insira um nome na caixa de texto **Nome da Configuração**. Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importar uma configuração de nó com Azure PowerShell

Você pode usar o cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó para sua conta de automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Introdução à configuração de estado da automação do Azure] (Automation-DSC-get-started.md.
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
