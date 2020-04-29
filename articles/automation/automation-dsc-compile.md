---
title: Compilar configurações DSC na configuração de estado da automação do Azure
description: Este artigo descreve como compilar as configurações da DSC (Configuração de Estado Desejado) para Automação do Azure.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: f7558745442ac26fc33a063ff66fe170d08487ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392082"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Compilar configurações DSC na configuração de estado da automação do Azure

Você pode compilar configurações de DSC (configuração de estado desejado) na configuração de estado de automação do Azure das seguintes maneiras:

- Serviço de compilação de configuração de estado do Azure
  - Método para iniciantes com interface do usuário interativa
   - Acompanhar facilmente o estado do trabalho

- Windows PowerShell
  - Chamada do Windows PowerShell na estação de trabalho local ou serviço de compilação
  - Integrar com pipeline de teste de desenvolvimento
  - Fornecer valores de parâmetro complexos
  - Trabalhar com dados de nó e não nó em escala
  - Melhoria significativa no desempenho

Você também pode usar modelos de Azure Resource Manager com a extensão de DSC (configuração de estado desejado) do Azure para enviar configurações por push para suas VMs do Azure. A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. Para obter detalhes de compilação usando modelos de Azure Resource Manager, consulte [extensão de configuração de estado desejado com modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilando uma configuração DSC na configuração de estado do Azure

### <a name="portal"></a>Portal

1. Na sua conta de automação, clique em **configuração de estado (DSC)**.
1. Clique na guia **Configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-lo. Se a configuração tiver parâmetros, a folha de **configuração de compilação** será aberta para que você possa fornecer valores de parâmetro.
1. A página trabalho de compilação é aberta para que você possa acompanhar o status do trabalho de compilação. Você também pode usar essa página para controlar as configurações de nó (documentos de configuração do MOF) colocadas no servidor de pull de configuração de estado da automação do Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Você pode usar [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`Retorna um objeto de trabalho de compilação que você pode usar para acompanhar o status do trabalho. Você pode usar esse objeto de trabalho de compilação com [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) para determinar o status do trabalho de compilação e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) para exibir seus fluxos (saída). O exemplo a seguir inicia a compilação da configuração SampleConfig, aguarda até que ela seja concluída e, em seguida, exibe seus fluxos.

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

O exemplo a seguir `FeatureName` usa `IsPresent` parâmetros e para determinar os valores das propriedades na configuração do nó **ParametersExample. Sample** , gerada durante a compilação.

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

Você pode compilar configurações DSC que usam parâmetros básicos no portal de configuração de estado da automação do Azure ou com Azure PowerShell.

#### <a name="portal"></a>Portal

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![Parâmetros de configuração de compilação](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

O PowerShell requer parâmetros em uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables), em que a chave corresponde ao nome do parâmetro e o valor é igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter informações sobre `PSCredential` como passar objetos como parâmetros, consulte [ativos de credencial](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilar configurações que contêm recursos de composição na automação do Azure

O recurso de **recursos de composição** permite que você use configurações de DSC como recursos aninhados dentro de uma configuração. Esse recurso permite que o aplicativo de várias configurações para um único recurso. Consulte [recursos de composição: usando uma configuração DSC como um recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para saber mais sobre os recursos de composição.

> [!NOTE]
> Para que as configurações que contêm recursos de composição sejam compiladas corretamente, você deve primeiro importar para a automação do Azure quaisquer recursos de DSC dos quais as composições dependem. Adicionar um recurso de composição de DSC não é diferente de adicionar nenhum módulo do PowerShell à automação do Azure. Esse processo está documentado em [gerenciar módulos na automação do Azure](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gerenciar ConfigurationData ao compilar configurações na automação do Azure

`ConfigurationData`é um parâmetro interno de DSC que permite separar a configuração estrutural de qualquer configuração específica do ambiente ao usar o DSC do PowerShell. Para obter mais informações, consulte separando ["o que" de "onde" na DSC do PowerShell](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Ao compilar na configuração de estado da automação do Azure, `ConfigurationData` você pode usar no Azure PowerShell, mas não no portal do Azure.

O exemplo de configuração DSC a `ConfigurationData` seguir usa `$ConfigurationData` por `$AllNodes` meio das palavras-chave e. Você também precisa do [módulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo.

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

Você pode compilar a configuração de DSC anterior com o Windows PowerShell. O script a seguir adiciona duas configurações de nó ao serviço de pull de configuração de estado da automação do Azure: **ConfigurationDataSample. MyVM1** e **ConfigurationDataSample. MyVM3**.

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

As referências de ativos são as mesmas tanto na configuração de estado da automação do Azure quanto nos runbooks. Para saber mais, consulte o seguinte:

- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

#### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na Automação do Azure podem fazer referência a ativos de credencial de automação usando `Get-AutomationPSCredential` experiência. Se uma configuração tiver um parâmetro que especifica um `PSCredential` objeto, use `Get-AutomationPSCredential` passando o nome da cadeia de caracteres de um ativo de credencial de automação do Azure para o cmdlet para recuperar a credencial. Em seguida, faça uso desse objeto para o parâmetro que requer `PSCredential` o objeto. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e passado para a configuração. O exemplo a seguir mostra esse cenário em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. No momento, você deve conceder permissão de DSC do PowerShell para gerar credenciais em texto sem formatação durante a geração de MOF de configuração de nó. O DSC do PowerShell não reconhece que a automação do Azure criptografa todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

Você pode informar ao DSC do PowerShell que é possível que as credenciais sejam emitidas em texto sem formatação no MOFs de configuração de nó gerado usando dados de configuração. Você deve passar `PSDscAllowPlainTextPassword = $true` via `ConfigurationData` para cada nome de bloco de nó que aparece na configuração DSC e usa credenciais.

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

Você pode compilar a configuração da DSC acima com o PowerShell. O código do PowerShell a seguir adiciona duas configurações de nó ao servidor de pull de configuração de estado da automação do Azure: **CredentialSample. MyVM1** e **CredentialSample. MyVM2**.

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
> Quando a compilação for concluída, você poderá receber a mensagem `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` de erro você pode ignorar essa mensagem com segurança.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Compilando sua configuração DSC no Windows PowerShell

Você também pode importar configurações de nó (Arquivos MOF) que foram compiladas fora do Azure. A importação inclui a compilação de uma estação de trabalho de desenvolvedor ou em um serviço como o [Azure DevOps](https://dev.azure.com). Essa abordagem tem várias vantagens, incluindo desempenho e confiabilidade.

A compilação no Windows PowerShell também fornece a opção de assinar o conteúdo de configuração. O agente DSC verifica uma configuração de nó assinada localmente em um nó gerenciado. A verificação garante que a configuração aplicada ao nó venha de uma fonte autorizada.

> [!NOTE]
> Um arquivo de configuração de nó não deve ter mais de 1 MB para permitir que a automação do Azure o importe.

Para obter mais informações sobre a assinatura de configurações de nó, consulte [melhorias no WMF 5,1-como assinar a configuração e o módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-the-dsc-configuration"></a>Compilar a configuração DSC

O processo para compilar configurações DSC no Windows PowerShell está incluído na documentação do DSC do PowerShell [gravar, compilar e aplicar uma configuração](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Você pode executar esse processo em uma estação de trabalho de desenvolvedor ou em um serviço de compilação, como o [Azure DevOps](https://dev.azure.com). Em seguida, você pode importar os arquivos MOF produzidos compilando a configuração no serviço de configuração de estado do Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal do Azure

1. Na sua conta de automação, clique em **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página configuração de estado (DSC), clique na guia **configurações** e, em seguida, clique em **Adicionar**.
1. Na página importar, clique no ícone de pasta ao lado do campo **arquivo de configuração de nó** para procurar um arquivo MOF de configuração de nó no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Insira um nome no campo **nome da configuração** . Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importar uma configuração de nó com Azure PowerShell

Você pode usar o cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó para sua conta de automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, consulte [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua em máquinas virtuais usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
