---
title: Gerenciar módulos na Automação do Azure
description: A automação do Azure permite que você importe módulos do PowerShell para habilitar cmdlets em runbooks e recursos de DSC em configurações de DSC.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84fdb5a9cf3c22048473cd00ee6f8e7ac36c9097
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864293"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na Automação do Azure

A automação do Azure permite que você importe módulos do PowerShell para habilitar cmdlets em runbooks e recursos de DSC em configurações de DSC. Os módulos usados na automação do Azure incluem:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0).
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0).
* Outros módulos do PowerShell.
* Módulo `Orchestrator.AssetManagement.Cmdlets` interno.
* Módulos Python 2.
* Módulos personalizados que você cria.

Quando você cria uma conta de automação, a automação do Azure importa alguns módulos por padrão. Consulte [módulos padrão](#default-modules).

Quando a automação executa trabalhos de compilação de runbook e DSC, ele carrega os módulos em áreas restritas em que os runbooks podem ser executados e as configurações de DSC podem ser compiladas. A automação também coloca automaticamente todos os recursos de DSC em módulos no servidor de pull de DSC. Os computadores podem extrair os recursos quando aplicam as configurações de DSC.

>[!NOTE]
>Certifique-se de importar apenas os módulos de que seus runbooks e configurações DSC realmente precisam. Não é recomendável importar o módulo AZ raiz. Ele inclui muitos outros módulos que talvez não sejam necessários, o que pode causar problemas de desempenho. Importe módulos individuais, como AZ. Compute, em vez disso.

>[!NOTE]
>Este artigo usa o módulo Azure PowerShell AZ. Você ainda pode usar o módulo AzureRM. Para saber mais sobre o módulo AZ e a compatibilidade do AzureRM, confira [introdução ao novo módulo Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos que a automação do Azure importa por padrão quando você cria sua conta de automação. A automação pode importar versões mais recentes desses módulos. No entanto, não é possível remover a versão original da sua conta de automação, mesmo que você exclua uma versão mais recente. Observe que esses módulos padrão incluem vários módulos AzureRM. 

A automação não importa o módulo AZ raiz automaticamente para qualquer conta de automação nova ou existente. Para obter mais informações sobre como trabalhar com esses módulos, consulte [migrando para módulos AZ](#migrating-to-az-modules).

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

## <a name="az-modules"></a>Módulos AZ

Para AZ. Automation, a maioria dos cmdlets tem os mesmos nomes usados para os módulos AzureRM, exceto que o prefixo *AzureRM* foi alterado para *AZ*. Para obter uma lista de módulos AZ que não seguem essa Convenção de nomenclatura, consulte a [lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Cmdlets internos

A automação do Azure dá `Orchestrator.AssetManagement.Cmdlets` suporte ao módulo interno para o agente de log Analytics para Windows, instalado por padrão. A tabela a seguir define os cmdlets internos. Esses cmdlets são projetados para serem usados em vez de Azure PowerShell cmdlets para interagir com recursos compartilhados. Eles podem recuperar segredos de variáveis criptografadas, credenciais e conexões criptografadas.

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

Observe que os cmdlets internos diferem na nomenclatura dos cmdlets AZ e AzureRM. Os nomes de cmdlets internos não contêm palavras como "Azure" ou "AZ" no substantivo, mas usam a palavra *Automation*. Recomendamos seu uso sobre o uso de cmdlets AZ ou AzureRM durante a execução do runbook em uma área restrita do Azure ou em um Hybrid Runbook Worker do Windows. Eles exigem menos parâmetros e são executados no contexto de seu trabalho que já está em execução.

Use os cmdlets AZ ou AzureRM para manipular recursos de automação fora do contexto de um runbook. 

## <a name="python-modules"></a>Módulos do Python

Você pode criar runbooks do Python 2 na automação do Azure. Para obter informações sobre o módulo python, consulte [gerenciar pacotes python 2 na automação do Azure](../python-packages.md).

## <a name="custom-modules"></a>Módulos personalizados

A automação do Azure dá suporte a módulos personalizados do PowerShell que você cria para usar com seus runbooks e configurações DSC. Um tipo de módulo personalizado é um módulo de integração que, opcionalmente, contém um arquivo de metadados para definir a funcionalidade personalizada para os cmdlets do módulo. Um exemplo do uso de um módulo de integração é fornecido em [Adicionar um tipo de conexão](../automation-connections.md#add-a-connection-type).

A automação do Azure pode importar um módulo personalizado para disponibilizar seus cmdlets. Nos bastidores, ele armazena o módulo e o usa nas áreas restritas do Azure, assim como faz com outros módulos.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

### <a name="migration-considerations"></a>Considerações sobre migração

Esta seção inclui considerações a serem levadas em conta quando você estiver migrando para os módulos AZ na automação. Para obter mais informações, consulte [migrar Azure PowerShell de AzureRM para AZ](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Uso de módulos AzureRM e módulos AZ na mesma conta de automação

 Não recomendamos a execução de módulos AzureRM e módulos AZ na mesma conta de automação. Quando você tiver certeza de que deseja migrar de AzureRM para AZ, é melhor confirmar completamente uma migração completa. A automação geralmente reutiliza as áreas restritas na conta de automação para economizar nos tempos de inicialização. Se você não fizer uma migração completa de módulo, poderá iniciar um trabalho que usa apenas módulos AzureRM e, em seguida, iniciar outro trabalho que usa apenas módulos AZ. A área restrita logo falha e você recebe um erro informando que os módulos não são compatíveis. Essa situação resulta na ocorrência de falhas aleatoriamente para qualquer runbook ou configuração específica. 

#### <a name="importing-az-modules-into-the-powershell-session"></a>Importando módulos AZ para a sessão do PowerShell

Importar um módulo AZ para sua conta de automação não importa automaticamente o módulo para a sessão do PowerShell que os runbooks usam. Os módulos são importados na sessão do PowerShell nas seguintes situações:

* Quando um runbook invoca um cmdlet de um módulo.
* Quando um runbook importa o módulo explicitamente com o cmdlet [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7) .
* Quando um runbook importa outro módulo dependente.

#### <a name="testing-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testando seus runbooks e configurações DSC antes da migração de módulo

Certifique-se de testar todos os runbooks e configurações de DSC com cuidado, em uma conta de automação separada, antes de migrar para os módulos AZ. 

#### <a name="updates-for-tutorial-runbooks"></a>Atualizações para runbooks do tutorial 

Quando você cria uma nova conta de automação, mesmo após a migração para os módulos AZ, a automação instala os módulos AzureRM por padrão. Você ainda pode atualizar os runbooks do tutorial com os cmdlets AzureRM. No entanto, você não deve executar esses runbooks.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Parar e cancelar o agendamento de todos os runbooks que usam módulos AzureRM

Para garantir que você não execute quaisquer runbooks existentes ou configurações DSC que usam módulos AzureRM, você deve parar e cancelar o agendamento de todos os runbooks e configurações afetados. Primeiro, certifique-se de examinar cada runbook ou configuração DSC e suas agendas separadamente para garantir que você possa reagendar o item no futuro, se necessário. 

Quando estiver pronto para remover suas agendas, você poderá usar o portal do Azure ou o cmdlet [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . Consulte [remover uma agenda](schedules.md#remove-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Remover os módulos AzureRM

É possível remover os módulos AzureRM antes de importar os módulos AZ. No entanto, se você fizer isso, poderá interromper a sincronização do controle do código-fonte e causar a falha de todos os scripts que ainda estiverem agendados. Se você decidir remover os módulos, consulte [desinstalar o AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Importar os módulos Az

Você pode importar os módulos AZ no portal do Azure. Lembre-se de importar apenas os módulos AZ que você precisa, não o módulo AZ. Automation inteiro. Como [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos AZ, certifique-se de importar esse módulo antes de qualquer outro.

1. Em sua conta de automação, em **recursos compartilhados**, selecione **módulos**. 
2. Selecione **procurar na Galeria**.  
3. Na barra de pesquisa, insira o nome do módulo (por exemplo `Az.Accounts`,). 
4. Na página **módulo do PowerShell** , selecione **importar** para importar o módulo para sua conta de automação.

    ![Captura de tela da importação de módulos em sua conta de automação](../media/modules/import-module.png)

Você também pode fazer essa importação por meio do [Galeria do PowerShell](https://www.powershellgallery.com), pesquisando o módulo a ser importado. Quando encontrar o módulo, selecione-o e escolha a guia **automação do Azure** . Selecione **implantar na automação do Azure**.

![Captura de tela da importação de módulos diretamente do Galeria do PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testar seus runbooks

Depois de importar os módulos AZ para a conta de automação, você pode começar a editar seus runbooks e configurações DSC para usar os novos módulos. Uma maneira de testar a modificação de um runbook para usar os novos cmdlets é usando o `Enable-AzureRmAlias -Scope Process` comando no início do runbook. Ao adicionar esse comando ao seu runbook, o script pode ser executado sem alterações. 

## <a name="author-modules"></a>Criar módulos

Recomendamos que você siga as considerações nesta seção ao criar um módulo do PowerShell personalizado para uso na automação do Azure. Para preparar seu módulo para importação, você deve criar pelo menos um arquivo de módulo **. dll** psd1, Psm1 ou PowerShell com o mesmo nome que a pasta do módulo. Em seguida, você pode compactar a pasta do módulo para que a automação do Azure possa importá-la como um único arquivo. O pacote **. zip** deve ter o mesmo nome que a pasta de módulo contida. 

Para saber mais sobre como criar um módulo do PowerShell, consulte [como escrever um módulo de script do PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

### <a name="version-folder"></a>Pasta de versão

Não inclua uma pasta de versão no pacote **. zip** para seu módulo. Esse problema é uma preocupação menor para runbooks, mas causa um problema com o serviço de configuração de estado (DSC). A automação do Azure cria a pasta de versão automaticamente quando o módulo é distribuído para nós gerenciados pela configuração de estado. Se uma pasta de versão existir, você acabará com duas instâncias. Aqui está um exemplo de estrutura de pasta para um módulo de DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informações de ajuda

Inclua uma sinopse, descrição e URI de ajuda para cada cmdlet em seu módulo. No PowerShell, você pode definir informações de ajuda para cmdlets usando o `Get-Help` cmdlet. O exemplo a seguir mostra como definir um URI de Sinopse e ajuda em um arquivo de módulo **. psm1** .

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

  ![Captura de tela da ajuda do módulo de integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de conexão

Se o módulo se conectar a um serviço externo, defina um tipo de conexão usando um [módulo de integração personalizado](#custom-modules). Cada cmdlet no módulo deve aceitar uma instância desse tipo de conexão (objeto de conexão) como um parâmetro. Os usuários mapeiam parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. 

![Usar uma conexão personalizada no portal do Azure](../media/modules/connection-create-new.png)

O exemplo de runbook a seguir usa um ativo de `ContosoConnection` conexão contoso chamado para acessar recursos da Contoso e retornar dados do serviço externo. Neste exemplo, os campos são mapeados para as `UserName` propriedades `Password` e de um `PSCredential` objeto e, em seguida, passados para o cmdlet.

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

Você pode habilitar um comportamento semelhante para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, você deseja um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a automação possa chamar seus cmdlets sem construir uma tabela de hash para atuar como o objeto de conexão. O conjunto `UserAccount` de parâmetros é usado para passar as propriedades do campo de conexão. `ConnectionObject`permite que você passe a conexão diretamente.

### <a name="output-type"></a>Tipo de saída

Defina o tipo de saída para todos os cmdlets em seu módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense de tempo de design ajude a determinar as propriedades de saída do cmdlet durante a criação. Essa prática é especialmente útil durante a criação de runbook gráfico, para a qual o conhecimento de tempo de design é fundamental para uma experiência de usuário fácil com seu módulo.

Adicionar `[OutputType([<MyOutputType>])]`, onde `MyOutputType` é um tipo válido. Para saber mais sobre `OutputType`o, consulte [sobre o Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir é um exemplo de `OutputType` adição a um cmdlet:

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

  ![Captura de tela do tipo de saída de runbook gráfico](../media/modules/runbook-graphical-module-output-type.png)

  Esse comportamento é semelhante à funcionalidade de "tipo antecipado" da saída de um cmdlet no ambiente do serviço de integração do PowerShell, sem a necessidade de executá-lo.

  ![Captura de tela do POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

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

Verifique se o módulo está totalmente contido em um pacote que pode ser copiado usando XCopy. Os módulos de automação são distribuídos para as áreas restritas de automação quando os runbooks são executados. Os módulos devem funcionar independentemente do host que os executa. 

Você deve ser capaz de compactar e mover um pacote de módulo e fazer com que ele funcione normalmente quando for importado para o ambiente do PowerShell de outro host. Para que isso aconteça, certifique-se de que o módulo não dependa de nenhum arquivo fora da pasta do módulo que é compactada quando o módulo é importado para a automação. 

Seu módulo não deve depender de nenhuma configuração de registro exclusiva em um host. Os exemplos são as configurações feitas quando um produto é instalado. 

### <a name="module-file-paths"></a>Caminhos de arquivo de módulo

Certifique-se de que todos os arquivos no módulo tenham caminhos com menos de 140 caracteres. Todos os caminhos com mais de 140 caracteres resultam em problemas com a importação de runbooks. A automação não pode importar um arquivo com tamanho de caminho superior a 140 caracteres na `Import-Module`sessão do PowerShell com.

## <a name="import-modules"></a>Importar módulos

Esta seção define várias maneiras que você pode importar um módulo para sua conta de automação. 

### <a name="import-modules-in-the-azure-portal"></a>Importar módulos no portal do Azure

Para importar um módulo no portal do Azure:

1. Vá para sua conta de Automação.
2. Em **recursos compartilhados**, selecione **módulos**.
3. Selecione **Adicionar um módulo**. 
4. Selecione o arquivo **. zip** que contém o módulo.
5. Selecione **OK** para iniciar a importação do processo.

### <a name="import-modules-by-using-powershell"></a>Importar módulos usando o PowerShell

Você pode usar o cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) para importar um módulo para sua conta de automação. O cmdlet usa uma URL para um pacote module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo do Galeria do PowerShell diretamente. Certifique-se de `ModuleName` pegar `ModuleVersion` e do [Galeria do PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importar módulos do Galeria do PowerShell

Você pode importar [Galeria do PowerShell](https://www.powershellgallery.com) módulos diretamente da galeria ou da sua conta de automação.

Para importar um módulo diretamente do Galeria do PowerShell:

1. Vá para https://www.powershellgallery.com e procure o módulo para importar.
2. Em **Opções de instalação**, na guia **automação do Azure** , selecione **implantar na automação do Azure**. Essa ação abre a portal do Azure. 
3. Na página **importar** , selecione sua conta de automação e selecione **OK**.

![Captura de tela do módulo importar Galeria do PowerShell](../media/modules/powershell-gallery.png)

Para importar um módulo Galeria do PowerShell diretamente da sua conta de automação:

1. Em **recursos compartilhados**, selecione **módulos**. 
2. Selecione **procurar na Galeria**e procure um módulo na galeria. 
3. Selecione o módulo a ser importado e selecione **importar**. 
4. Selecione **OK** para iniciar o processo de importação.

![Captura de tela da importação de um módulo de Galeria do PowerShell da portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Excluir módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, poderá excluí-lo da sua conta de automação. Não é possível excluir as versões originais dos [módulos padrão](#default-modules) que são importados quando você cria uma conta de automação. Se o módulo a ser excluído for uma versão mais recente de um dos [módulos padrão](#default-modules), ele reverterá para a versão que foi instalada com sua conta de automação. Caso contrário, qualquer módulo que você excluir da sua conta de automação será removido.

### <a name="delete-modules-in-the-azure-portal"></a>Excluir módulos no portal do Azure

Para remover um módulo no portal do Azure:

1. Vá para sua conta de Automação. Em **recursos compartilhados**, selecione **módulos**. 
2. Selecione o módulo que você deseja remover. 
3. Na página **módulo** , selecione **excluir**. Se esse módulo for um dos [módulos padrão](#default-modules), ele reverterá para a versão que existia quando a conta de automação foi criada.

### <a name="delete-modules-by-using-powershell"></a>Excluir módulos usando o PowerShell

Para remover um módulo por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como usar módulos Azure PowerShell, consulte Introdução [ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Para saber mais sobre a criação de módulos do PowerShell, consulte [escrevendo um módulo do Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
