---
title: Compilar configurações de DSC na configuração do estado de automação do Azure
description: Este artigo descreve como compilar as configurações da DSC (Configuração de Estado Desejado) para Automação do Azure.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: f7558745442ac26fc33a063ff66fe170d08487ac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392082"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Compilar configurações de DSC na configuração do estado de automação do Azure

Você pode compilar configurações de Configuração de Estado Desejado (DSC) na configuração do estado de automação do Azure das seguintes maneiras:

- Serviço de compilação de configuração do estado do Azure
  - Método iniciante com interface de usuário interativa
   - Acompanhar facilmente o estado do trabalho

- Windows PowerShell
  - Chamada do Windows PowerShell na estação de trabalho local ou serviço de construção
  - Integre-se ao pipeline de teste de desenvolvimento
  - Fornecer valores parâmetros complexos
  - Trabalhe com dados de nó e não nó em escala
  - Melhora significativa do desempenho

Você também pode usar os modelos do Azure Resource Manager com a extensão DSC (Desired State Configuration, configuração do estado desejado) do Azure para empurrar configurações para suas VMs do Azure. A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. Para obter detalhes de compilação usando os modelos do Azure Resource Manager, consulte [a extensão de configuração de estado desejado com os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilando uma configuração dSC na configuração do estado do Azure

### <a name="portal"></a>Portal

1. Em sua conta de automação, clique em **Configuração de Estado (DSC)**.
1. Clique na guia **Configurações** e, em seguida, clique no nome da configuração para compilar.
1. Clique em **Compilar**.
1. Se a configuração não tiver parâmetros, você deve confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a lâmina **De compilação de configuração** será aberta para que você possa fornecer valores de parâmetros.
1. A página De trabalho de compilação é aberta para que você possa acompanhar o status do trabalho de compilação. Você também pode usar esta página para rastrear as configurações de nó (documentos de configuração MOF) colocadas no servidor de tração do Estado de Automação do Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Você pode usar [start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) para começar a compilar com o Windows PowerShell. O código de amostra a seguir começa a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`retorna um objeto de trabalho de compilação que você pode usar para rastrear o status do trabalho. Em seguida, você pode usar este objeto de trabalho de compilação com [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) para determinar o status do trabalho de compilação e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) para exibir seus fluxos (saída). A amostra a seguir inicia a compilação da configuração SampleConfig, espera até que ela seja concluída e, em seguida, exibe seus fluxos.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Declare parâmetros básicos

A declaração de parâmetro nas configurações DSC, incluindo tipos de parâmetros e propriedades, funciona da mesma maneira que os runbooks da Automação do Azure. Veja [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros de runbook.

O exemplo `FeatureName` a `IsPresent` seguir usa e parâmetros para determinar os valores das propriedades na configuração do nó **ParametersExample.sample,** gerada durante a compilação.

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

Você pode compilar configurações de DSC que usam parâmetros básicos no portal de configuração do Estado de Automação do Azure ou com o Azure PowerShell.

#### <a name="portal"></a>Portal

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![Parâmetros de configuração de compilação](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

O PowerShell requer parâmetros em uma [tabela hash,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)onde a chave corresponde ao nome do parâmetro e o valor é igual ao valor do parâmetro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Para obter `PSCredential` informações sobre a passagem de objetos como parâmetros, consulte [ativos de credencial](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilar configurações contendo recursos compostos no Azure Automation

O recurso **Recursos Compostos** permite que você use configurações DSC como recursos aninhados dentro de uma configuração. Esse recurso permite a aplicação de várias configurações a um único recurso. Consulte [recursos compostos: Usando uma configuração dSC como um recurso](/powershell/scripting/dsc/resources/authoringresourcecomposite) para aprender mais sobre recursos compostos.

> [!NOTE]
> Para que as configurações que contenham recursos compostos sejam compiladas corretamente, você deve primeiro importar para a Automação Azure quaisquer recursos DSC que os compósitos dependam. Adicionar um recurso composto DSC não é diferente de adicionar qualquer módulo PowerShell ao Azure Automation. Esse processo é documentado em [Manage Modules in Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gerenciar configuraçãoData ao compilar configurações na Automação do Azure

`ConfigurationData`é um parâmetro DSC incorporado que permite separar a configuração estrutural de qualquer configuração específica do ambiente ao usar o PowerShell DSC. Para obter mais informações, consulte [Separar "O quê" de "Onde" no PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Ao compilar na configuração do estado `ConfigurationData` de automação do Azure, você pode usar no Azure PowerShell, mas não no portal Azure.

O exemplo a seguir `ConfigurationData` a `$ConfigurationData` configuração dSC usa através das palavras-chave e `$AllNodes` palavras-chave. Você também precisa do [módulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo.

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

Você pode compilar a configuração DSC anterior com o Windows PowerShell. O script a seguir adiciona duas configurações de nó ao serviço de tração do estado de automação do Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Trabalhe com ativos na Azure Automation durante a compilação

As referências de ativos são as mesmas tanto na configuração do estado de automação do Azure quanto nos runbooks. Para saber mais, consulte o seguinte:

- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

#### <a name="credential-assets"></a>Ativos de credencial

Configurações de DSC na Automação do Azure podem fazer referência a ativos de credencial de automação usando `Get-AutomationPSCredential` experiência. Se uma configuração tiver um `PSCredential` parâmetro que `Get-AutomationPSCredential` especifique um objeto, use passando o nome da seqüência de um recurso de credencial do Azure Automation para o cmdlet para recuperar a credencial. Em seguida, faça uso desse objeto `PSCredential` para o parâmetro que requer o objeto. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e passado para a configuração. O exemplo abaixo mostra esse cenário em ação.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. Atualmente, você deve dar permissão ao PowerShell DSC para obter credenciais em texto simples durante a geração MOF de configuração de nó. O PowerShell DSC não está ciente de que o Azure Automation criptografa todo o arquivo MOF após sua geração através de um trabalho de compilação.

Você pode dizer ao PowerShell DSC que está tudo bem para que as credenciais sejam desemeadas em texto simples na configuração de nó gerado MOFs usando dados de configuração. Você deve `PSDscAllowPlainTextPassword = $true` `ConfigurationData` passar por cada nome de bloco de nó que aparece na configuração do DSC e usa credenciais.

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

Você pode compilar a configuração da DSC acima com o PowerShell. O seguinte código PowerShell adiciona duas configurações de nó ao servidor de tração do Estado de Automação do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

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
> Quando a compilação estiver concluída, `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` você poderá receber a mensagem de erro Você pode ignorar esta mensagem com segurança.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Compilando sua configuração de DSC no Windows PowerShell

Você também pode importar configurações de nó (arquivos MOF) que foram compiladas fora do Azure. A importação inclui compilação de uma estação de trabalho de desenvolvedor ou em um serviço como [Ozure DevOps](https://dev.azure.com). Essa abordagem tem múltiplas vantagens, incluindo desempenho e confiabilidade.

Compilar no Windows PowerShell também oferece a opção de assinar conteúdo de configuração. O agente DSC verifica uma configuração de nó assinado localmente em um nó gerenciado. A verificação garante que a configuração aplicada ao nó vem de uma fonte autorizada.

> [!NOTE]
> Um arquivo de configuração de nó não deve ser maior que 1 MB para permitir que o Azure Automation o importe.

Para obter mais informações sobre a assinatura de configurações de nós, consulte [Melhorias no WMF 5.1 - Como assinar configuração e módulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-the-dsc-configuration"></a>Compilar a configuração dSC

O processo para compilar configurações de DSC no Windows PowerShell está incluído na documentação do PowerShell DSC [Write, Compile e Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Você pode executar esse processo a partir de uma estação de trabalho de desenvolvedor ou dentro de um serviço de compilação, como [o Azure DevOps](https://dev.azure.com). Em seguida, você pode importar os arquivos MOF produzidos compilando a configuração no serviço de configuração do estado do Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal Azure

1. Em sua conta de automação, clique em **Configuração de Estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página Configuração estado (DSC), clique na guia **Configurações** e clique em **Adicionar**.
1. Na página Importar, clique no ícone da pasta ao lado do campo **Arquivo de configuração de** nó para procurar um arquivo MOF de configuração de nó no computador local.

   ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)

1. Digite um nome no campo **Nome de configuração.** Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
1. Clique em **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importe uma configuração de nó com o Azure PowerShell

Você pode usar o [cmdlet Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) para importar uma configuração de nó em sua conta de automação.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Como começar com a configuração do estado de automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre a compilação das configurações do DSC para que você possa atribuí-las a nós de destino, consulte [Compilando configurações na configuração do estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte os preços de [configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso da configuração do estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua em máquinas virtuais usando a configuração do estado de automação do Azure e chocolatey](automation-dsc-cd-chocolatey.md).
