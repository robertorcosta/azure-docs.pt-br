---
title: Gerenciar módulos na Automação do Azure
description: Este artigo descreve como gerenciar módulos na automação do Azure.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d036733c023417af3ef038bb9abc278ec91e665c
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508948"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na Automação do Azure

A automação do Azure permite que você importe módulos do PowerShell para habilitar cmdlets em runbooks e recursos de DSC em configurações de DSC. Os módulos usados na automação do Azure incluem:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Outros módulos do PowerShell
* Módulo `Orchestrator.AssetManagement.Cmdlets` interno
* Módulos do Python 2
* Módulos personalizados que você cria 

Quando você cria uma conta de automação, a automação do Azure importa alguns módulos por padrão. Consulte [módulos padrão](#default-modules).

Quando a automação do Azure executa trabalhos de compilação de runbook e DSC, ele carrega os módulos em áreas restritas em que os runbooks podem ser executados e as configurações de DSC podem ser compiladas. A automação também coloca automaticamente todos os recursos de DSC em módulos no servidor de pull de DSC. Os computadores podem extrair os recursos quando aplicam as configurações de DSC.

>[!NOTE]
>Certifique-se de importar apenas os módulos de que seus runbooks e configurações DSC realmente precisam. Não é recomendável importar o módulo AZ raiz, pois ele inclui muitos outros módulos que talvez não sejam necessários, o que pode causar problemas de desempenho. Importe módulos individuais, como AZ. Compute, em vez disso.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos que a automação do Azure importa por padrão quando você cria sua conta de automação. A automação pode importar versões mais recentes desses módulos. No entanto, não é possível remover a versão original da sua conta de automação, mesmo que você exclua uma versão mais recente. Observe que esses módulos padrão incluem vários módulos AzureRM. 

A automação do Azure não importa o módulo AZ raiz automaticamente para contas de automação novas ou existentes. Para obter mais informações sobre como trabalhar com esses módulos, consulte [migrando para módulos AZ](#migrating-to-az-modules).

> [!NOTE]
> Não recomendamos a alteração de módulos e runbooks em contas de automação que contenham a [solução iniciar/parar VMs fora do horário comercial na automação do Azure](../automation-solution-vm-management.md).

|Nome do módulo|Versão|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator. AssetManagement. cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-module-cmdlets"></a>Cmdlets do módulo AZ

Para AZ. Automation, a maioria dos cmdlets tem os mesmos nomes dos módulos AzureRM, exceto que o prefixo AzureRm foi alterado para AZ. Para obter uma lista de módulos AZ que não seguem essa Convenção de nomenclatura, consulte [lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Cmdlets internos

A tabela a seguir define os cmdlets internos com suporte `Orchestrator.AssetManagement.Cmdlets` no módulo. Use esses cmdlets em seus runbooks e configurações DSC para interagir com os ativos do Azure na conta de automação. Os cmdlets são projetados para serem usados em vez de Azure PowerShell cmdlets para recuperar segredos de variáveis criptografadas, credenciais e conexões criptografadas. 

>[!NOTE]
>Os cmdlets internos só estão disponíveis quando você está executando runbooks no ambiente de área restrita do Azure ou em um Hybrid Runbook Worker do Windows. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Observe que os cmdlets internos diferem na nomenclatura dos cmdlets AZ e AzureRM. Os nomes de cmdlets internos não contêm palavras como "Azure" ou "AZ" no substantivo, mas usam a palavra "Automation". Recomendamos seu uso sobre o uso de cmdlets AZ ou AzureRM durante a execução do runbook em uma área restrita do Azure ou em um trabalhador híbrido do Windows. Eles exigem menos parâmetros e são executados no contexto do trabalho que já está em execução.

Recomendamos que você use os cmdlets AZ ou AzureRM para manipular recursos de automação do Azure fora do contexto de um runbook. 

## <a name="orchestratorassetmanagementcmdlets-module"></a>Módulo Orchestrator. AssetManagement. cmdlets

A automação do Azure dá `Orchestrator.AssetManagement.Cmdlets` suporte ao módulo interno para o agente de log Analytics para Windows, instalado por padrão. O `Get-AutomationPSCredential` cmdlet neste módulo é comumente usado em runbooks para recuperar um `PSCredential` objeto, que é esperado pela maioria dos cmdlets do PowerShell que funcionam com credenciais. Para saber mais sobre o uso de credenciais na automação do Azure, confira [ativos de credencial na automação do Azure](credentials.md).

## <a name="python-modules"></a>Módulos do Python

Você pode criar runbooks do Python 2 na automação do Azure. Para obter informações sobre o módulo python, consulte [gerenciar pacotes python 2 na automação do Azure](../python-packages.md).

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

### <a name="migration-considerations"></a>Considerações sobre migração

Esta seção inclui considerações a serem levadas em conta ao migrar para os módulos AZ na automação do Azure. Consulte também [migrar Azure PowerShell de AzureRM para AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Uso de módulos AzureRM e módulos AZ na mesma conta de automação

 Não recomendamos a execução de módulos AzureRM e módulos AZ na mesma conta de automação. Quando você tiver certeza de que deseja migrar de AzureRM para AZ, é melhor confirmar completamente uma migração completa. O motivo mais importante para isso é que a automação do Azure geralmente reutiliza as áreas restritas na conta de automação para economizar nos tempos de inicialização. Se você não fizer uma migração completa de módulo, poderá iniciar um trabalho usando apenas módulos AzureRM e, em seguida, iniciar outro trabalho usando apenas módulos AZ. A área restrita logo falha e você recebe um erro fatal informando que os módulos não são compatíveis. Essa situação resulta em falhas que ocorrem aleatoriamente para qualquer determinado runbook ou configuração. 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>Importação de módulos AZ para a sessão do PowerShell

Importar um módulo AZ para sua conta de automação não importa automaticamente o módulo para a sessão do PowerShell que os runbooks usam. Os módulos são importados na sessão do PowerShell nas seguintes situações:

* Quando um runbook invoca um cmdlet de um módulo
* Quando um runbook importa o módulo explicitamente com o cmdlet [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
* Quando um runbook importa outro módulo dependente

#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Teste para seus runbooks e configurações DSC antes da migração de módulo

Certifique-se de testar todos os runbooks e configurações DSC cuidadosamente em uma conta de automação separada antes de migrar para os módulos AZ. 

#### <a name="updates-for-tutorial-runbooks"></a>Atualizações para runbooks do tutorial 

Quando você cria uma nova conta de automação, mesmo após a migração para os módulos AZ, a automação do Azure instala os módulos AzureRM por padrão. Você ainda pode atualizar os runbooks do tutorial com os cmdlets AzureRM. No entanto, você não deve executar esses runbooks.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Parar e cancelar o agendamento de todos os runbooks que usam módulos AzureRM

Para garantir que você não execute quaisquer runbooks existentes ou configurações DSC que usam módulos AzureRM, você deve parar e cancelar o agendamento de todos os runbooks e configurações afetados. Primeiro, certifique-se de examinar cada runbook ou configuração DSC e suas agendas separadamente para garantir que você possa reagendar o item no futuro, se necessário. 

Quando estiver pronto para remover suas agendas, você poderá usar o portal do Azure ou o cmdlet [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . Consulte [removendo uma agenda](schedules.md#removing-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Remover os módulos AzureRM

É possível remover os módulos AzureRM antes de importar os módulos AZ. No entanto, a exclusão dos módulos AzureRM pode interromper a sincronização do controle do código-fonte e causar a falha de qualquer script que ainda esteja agendado. Se você decidir remover os módulos, consulte [desinstalar o AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Importar os módulos Az

Esta seção informa como importar os módulos AZ no portal do Azure. Lembre-se de importar apenas os módulos AZ que você precisa, não o módulo AZ. Automation inteiro. Como [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos AZ, certifique-se de importar esse módulo antes de qualquer outro.

1. Na sua conta de automação, selecione **módulos** em **recursos compartilhados**. 
2. Clique em **Procurar na Galeria** para abrir a página Procurar na Galeria.  
3. Na barra de pesquisa, insira o nome do módulo, por exemplo `Az.Accounts`,. 
4. Na página módulo do PowerShell, clique em **importar** para importar o módulo para sua conta de automação.

    ![Importar módulos para a conta de automação](../media/modules/import-module.png)

Esse processo de importação também pode ser feito por meio do [Galeria do PowerShell](https://www.powershellgallery.com) pesquisando o módulo a ser importado. Depois de encontrar o módulo, selecione-o, escolha a guia **automação do Azure** e clique em **implantar na automação do Azure**.

![Importar módulos diretamente da galeria](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testar seus runbooks

Depois de importar os módulos AZ para a conta de automação, você pode começar a editar seus runbooks e configurações DSC para usar os novos módulos. Uma maneira de testar a modificação de um runbook para usar os novos cmdlets é usando `Enable-AzureRmAlias -Scope Process` no início do runbook. Ao adicionar esse comando ao seu runbook, o script pode ser executado sem alterações. 

## <a name="authoring-modules"></a>Módulos de criação

Recomendamos que você siga as considerações nesta seção ao criar um módulo do PowerShell para uso na automação do Azure.

### <a name="version-folder"></a>Pasta de versão

Não inclua uma pasta de versão no pacote **. zip** para seu módulo.  Esse problema é uma preocupação menor para runbooks, mas causa um problema com o serviço de configuração de estado (DSC). A automação do Azure cria a pasta de versão automaticamente quando o módulo é distribuído para nós gerenciados pela DSC. Se uma pasta de versão existir, você acabará com duas instâncias. Aqui está um exemplo de estrutura de pasta para um módulo de DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informações de ajuda

Inclua uma sinopse, descrição e URI de ajuda para cada cmdlet em seu módulo. No PowerShell, você pode definir informações de ajuda para cmdlets usando `Get-Help` o cmdlet. O exemplo a seguir mostra como definir um URI de Sinopse e ajuda em um arquivo de módulo **. psm1** .

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Fornecer essas informações mostra o texto de ajuda `Get-Help` por meio do cmdlet no console do PowerShell. Esse texto também é exibido no portal do Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de conexão

Se o módulo se conectar a um serviço externo, defina um [tipo de conexão](#adding-a-connection-type-to-your-module). Cada cmdlet no módulo deve aceitar um objeto de conexão (uma instância desse tipo de conexão) como um parâmetro. Os usuários mapeiam parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. O exemplo de runbook a seguir, com base no exemplo na seção anterior, usa um ativo de conexão `ContosoConnection` contoso chamado para acessar recursos da Contoso e retornar dados do serviço externo. Neste exemplo, os campos são mapeados para as `UserName` propriedades `Password` e de um `PSCredential` objeto e, em seguida, passados para o cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Uma maneira mais fácil e melhor de abordar esse comportamento é passando diretamente o objeto de conexão para o cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Você pode habilitar um comportamento semelhante para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto `UserAccount` de parâmetros é usado para passar as propriedades do campo de conexão. `ConnectionObject`permite que você passe a conexão diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets em seu módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense de tempo de design ajude a determinar as propriedades de saída do cmdlet durante a criação. Essa prática é especialmente útil durante a criação de runbook gráfico, para a qual o conhecimento de tempo de design é fundamental para uma experiência de usuário fácil com seu módulo.

Adicionar `[OutputType([<MyOutputType>])]` onde `MyOutputType` é um tipo válido. Para saber mais sobre `OutputType`o, consulte [sobre o Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir é um exemplo de `OutputType` adição a um cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Tipo de saída do runbook gráfico](../media/modules/runbook-graphical-module-output-type.png)

  Esse comportamento é semelhante à funcionalidade "preenchimento automático" da saída de um cmdlet no ISE do PowerShell sem precisar executá-lo.

  ![IntelliSense POSH](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Estado do cmdlet

Tornar todos os cmdlets em seu módulo sem monitoração de estado. Vários trabalhos de runbook podem ser executados simultaneamente no `AppDomain` mesmo e no mesmo processo e área restrita. Se houver qualquer estado compartilhado nesses níveis, os trabalhos poderão afetar os outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar. Veja um exemplo do que não fazer:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Dependência de módulo

Verifique se o módulo está totalmente contido em um pacote que pode ser copiado usando XCopy. Os módulos de automação do Azure são distribuídos para as áreas restritas de automação quando os runbooks são executados. Os módulos devem funcionar independentemente do host que os executa. 

Você deve ser capaz de compactar e mover um pacote de módulo e fazer com que ele funcione normalmente quando importado para o ambiente do PowerShell de outro host. Para que isso aconteça, certifique-se de que o módulo não dependa de nenhum arquivo fora da pasta do módulo que é compactada quando o módulo é importado para a automação do Azure. 

Seu módulo não deve depender de nenhuma configuração de registro exclusiva em um host. Os exemplos são as configurações feitas quando um produto é instalado. 

### <a name="module-file-paths"></a>Caminhos de arquivo de módulo

Certifique-se de que todos os arquivos no módulo tenham caminhos com menos de 140 caracteres. Todos os caminhos com mais de 140 caracteres resultam em problemas com a importação de runbooks. A automação do Azure não pode importar um arquivo com tamanho de caminho superior a 140 caracteres `Import-Module`para a sessão do PowerShell com.

## <a name="importing-modules"></a>Importando módulos

Esta seção define várias maneiras que você pode importar um módulo para sua conta de automação. 

### <a name="import-modules-in-azure-portal"></a>Importar módulos no portal do Azure

Para importar um módulo no portal do Azure:

1. Abra sua conta de Automação.
2. Selecione **módulos** em **recursos compartilhados**.
3. Clique em **Adicionar um módulo**. 
4. Selecione o arquivo **. zip** que contém o módulo.
5. Clique em **OK** para iniciar a importação do processo.

### <a name="import-modules-using-powershell"></a>Importar módulos usando o PowerShell

Você pode usar o cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) para importar um módulo para sua conta de automação. O cmdlet usa uma URL para um pacote module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo de Galeria do PowerShell diretamente. Certifique-se de `ModuleName` pegar `ModuleVersion` e de [Galeria do PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importar módulos de Galeria do PowerShell

Você pode importar [Galeria do PowerShell](https://www.powershellgallery.com) módulos diretamente da galeria ou da sua conta de automação.

Para importar um módulo diretamente do Galeria do PowerShell:

1. Vá para https://www.powershellgallery.com e procure o módulo para importar.
2. Clique em **implantar na automação do Azure** na guia **automação do Azure** em **Opções de instalação**. Essa ação abre a portal do Azure. 
3. Na página Importar, selecione sua conta de Automação e clique em **OK**.

![Módulo de importação de Galeria do PowerShell](../media/modules/powershell-gallery.png)

Para importar um módulo Galeria do PowerShell diretamente da sua conta de automação:

1. Selecione **módulos** em **recursos compartilhados**. 
2. Na página módulos, clique em **procurar na Galeria**e procure um módulo na galeria. 
3. Selecione o módulo a ser importado e clique em **importar**. 
4. Na página importar, clique em **OK** para iniciar o processo de importação.

![Galeria do PowerShell importação do portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Excluindo módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, poderá excluí-lo da sua conta de automação. Não é possível excluir as versões originais dos [módulos padrão](#default-modules) que são importados quando você cria uma conta de automação. Se o módulo a ser excluído for uma versão mais recente de um dos [módulos padrão](#default-modules), ele reverterá para a versão que foi instalada com sua conta de automação. Caso contrário, qualquer módulo que você excluir da sua conta de automação será removido.

### <a name="delete-modules-in-azure-portal"></a>Excluir módulos no portal do Azure

Para remover um módulo no portal do Azure:

1. Navegue até sua conta de automação e selecione **módulos** em **recursos compartilhados**. 
2. Selecione o módulo que você deseja remover. 
3. Na página **módulo** , selecione **excluir**. Se esse módulo for um dos [módulos padrão](#default-modules), ele reverterá para a versão que existia quando a conta de automação foi criada.

### <a name="delete-modules-using-powershell"></a>Excluir módulos usando o PowerShell

Para remover um módulo por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Adicionando um tipo de conexão ao seu módulo

Você pode fornecer um [tipo de conexão](../automation-connections.md) personalizada para usar em sua conta de automação adicionando um arquivo de metadados opcional ao seu módulo. Esse arquivo especifica um tipo de conexão de automação do Azure a ser usado com os cmdlets do módulo em sua conta de automação. Para saber mais sobre como criar um módulo do PowerShell, consulte [como escrever um módulo de script do PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Usar uma conexão personalizada no portal do Azure](../media/modules/connection-create-new.png)

O arquivo que especifica as propriedades do tipo ** &lt;&gt;de conexão é denominado ModuleName-Automation. JSON** e é encontrado na pasta do módulo do arquivo **. zip** compactado. Esse arquivo contém os campos de uma conexão que são necessários para se conectar ao sistema ou serviço que o módulo representa. A configuração permite a criação de um tipo de conexão na automação do Azure. Usando esse arquivo, você pode definir os nomes de campo, tipos e se os campos são criptografados ou opcionais para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo **. JSON** que define as propriedades de nome de usuário e senha:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como usar módulos Azure PowerShell, consulte Introdução [ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Para saber mais sobre a criação de módulos do PowerShell, consulte [escrevendo um módulo do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
