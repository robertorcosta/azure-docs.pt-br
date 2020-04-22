---
title: Gerenciar módulos na Automação do Azure
description: Este artigo descreve como gerenciar módulos no Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770937"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na Automação do Azure

O Azure Automation permite importar módulos PowerShell para habilitar cmdlets em runbooks e recursos DSC em configurações DeDC. Os módulos usados no Azure Automation incluem:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Módulo `Orchestrator.AssetManagement.Cmdlets` interno para o agente Log Analytics para Windows
* [AzureAutomationAutoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Outros módulos PowerShell
* Módulos personalizados que você cria 

Quando você cria uma conta de Automação, a Azure Automation importa alguns módulos por padrão. Consulte [módulos padrão](#default-modules).

Quando o Azure Automation executa trabalhos de compilação de runbook e DSC, ele carrega os módulos em caixas de areia onde os runbooks podem ser executados e as configurações do DSC podem compilar. A automação também coloca automaticamente qualquer recurso DSC em módulos no servidor de tração DSC. As máquinas podem puxar os recursos quando aplicam as configurações do DSC.

>[!NOTE]
>Certifique-se de importar apenas os módulos que seus runbooks e configurações DeDC realmente precisam. Não importe o módulo de rollup, por exemplo, Az.Automation, em qualquer caso.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista módulos que o Azure Automation importa por padrão quando você cria sua conta de Automação. A automação pode importar versões mais recentes desses módulos. No entanto, você não pode remover a versão original de sua conta de Automação mesmo se você excluir uma versão mais recente. Observe que esses módulos padrão incluem vários módulos AzureRM. 

> [!NOTE]
> Não recomendamos alterar módulos e runbooks em contas de automação que contenham quaisquer soluções. 

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
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |


Os módulos Az.Automation são preferidos em seus runbooks e configurações de DSC. No entanto, a Azure Automation não importa o módulo Az rollup automaticamente em quaisquer contas de Automação novas ou existentes. Para saber mais sobre como trabalhar com esses módulos, consulte [Migrando para módulos Az](#migrating-to-az-modules).

## <a name="internal-cmdlets"></a>Cmdlets internos

A tabela a seguir define os cmdlets internos suportados pelo `Orchestrator.AssetManagement.Cmdlets` módulo. Use-os em seus runbooks e configurações DSC para interagir com ativos de automação dentro da conta automação. Os cmdlets são projetados para recuperar segredos de variáveis criptografadas, credenciais e conexões criptografadas. 

> [!NOTE]
> Os cmdlets do Azure PowerShell não podem obter os segredos de ativos que os cmdlets internos podem recuperar. 

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Trabalho de automação de espera|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Esses cmdlets internos estão disponíveis em um Trabalhador de Runbook Híbrido do Windows, mas não em um Executão Híbrido Linux. Eles são executados a partir de uma caixa de areia orquestradora, que é usada pelo trabalhador híbrido.  Seu uso não requer uma conexão implícita com o Azure, como ao usar uma conta Run As para autenticação.

Em vez de usar os cmdlets internos, use os cmdlets Az ou AzureRM para runbooks e configurações que são executados diretamente no computador ou contra recursos em seu ambiente. Nesses casos, você deve se conectar implicitamente ao Azure ao usar os cmdlets, como ao usar uma conta Run As para autenticar o Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Módulos que suportam Get-AutomationPSCredential

Para o desenvolvimento local usando o Azure `Get-AutomationPSCredential` Automation Authoring Toolkit, o cmdlet faz parte da montagem [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Para o Azure que trabalha com o `Orchestrator.AssetManagement.Cmdlets`contexto de Automação, o cmdlet está dentro . Para saber mais sobre o uso de credenciais no Azure Automation, consulte [os ativos credenciais na Azure Automation](credentials.md).

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

Existem várias coisas a serem consideradas ao usar os módulos Az na Automação Azure:

* Soluções de nível superior em sua conta de automação podem usar runbooks e módulos. Portanto, editar runbooks ou atualizar módulos pode potencialmente causar problemas com suas soluções. Você deve testar todos os runbooks e soluções cuidadosamente em uma conta de automação separada antes de importar os módulos Az. 

* Quaisquer modificações nos módulos podem afetar negativamente as [VMs Start/Stop durante a solução de horas de folga](../automation-solution-vm-management.md). 

* Importar um módulo Az para sua conta de automação não importa automaticamente o módulo na sessão PowerShell que os runbooks usam. Os módulos são importados na sessão do PowerShell nas seguintes situações:

    * Quando um runbook invoca um cmdlet de um módulo
    * Quando um runbook importa o `Import-Module` módulo explicitamente com o cmdlet
    * Quando um runbook importa outro módulo dependente

Depois de concluir a migração de seus módulos, não tente iniciar runbooks usando módulos AzureRM na conta Automação. Também é recomendado não importar ou atualizar módulos AzureRM na conta. Considere a conta migrada para Az.Automation e opere apenas com módulos Az. 

>[!IMPORTANT]
>Quando você cria uma nova conta de Automação, a Azure Automation instala os módulos AzureRM por padrão. Você ainda pode atualizar os runbooks tutoriais com cmdlets AzureRM. No entanto, você não deve executar esses runbooks.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Pare e desmarque todos os runbooks que usam módulos AzureRM

Para garantir que você não execute nenhum runbooks existente que use módulos AzureRM, pare e desprograme todos os runbooks afetados. Você pode ver quais horários existem e quais horários remover executando código semelhante a este exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante revisar cada cronograma separadamente para garantir que você possa reagendar no futuro para seus runbooks, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

Esta seção diz como importar os módulos Az no portal Azure. Lembre-se de importar apenas os módulos Az que você precisa, não todo o módulo Az.Automation. Como [o Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos Az, certifique-se de importar este módulo antes de qualquer outro.

1. A partir de sua conta de automação, selecione **Módulos** em **Recursos Compartilhados**. 
2. Clique em **Procurar na Galeria** para abrir a página Procurar na Galeria.  
3. Na barra de pesquisa, digite o `Az.Accounts`nome do módulo, por exemplo, . 
4. Na página do Módulo PowerShell, clique **em Importar** para importar o módulo para sua conta de Automação.

    ![Importar módulos para conta de automação](../media/modules/import-module.png)

Esse processo de importação também pode ser feito através da [Galeria PowerShell,](https://www.powershellgallery.com) procurando o módulo para importar. Depois de encontrar o módulo, selecione-o, escolha a guia **Automação Do Azul** e clique em Implantar no **Azure Automation**.

![Importar módulos diretamente da galeria](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testar seus runbooks

Depois de importar os módulos Az para a conta Automação, você pode começar a editar os runbooks para usar os novos módulos. A maioria dos cmdlets tem os mesmos nomes dos módulos AzureRM, exceto que o prefixo AzureRM (ou AzureRm) foi alterado para Az. Para obter uma lista de módulos que não seguem esta convenção de nomeação, consulte [a lista de exceções](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Uma maneira de testar a modificação de um runbook para `Enable-AzureRmAlias -Scope Process` usar os novos cmdlets é usando no início do runbook. Adicionando este comando ao seu runbook, o script pode ser executado sem alterações.

## <a name="authoring-modules"></a>Módulos de autoria

Recomendamos que você siga as considerações nesta seção ao criar um módulo PowerShell para uso na Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Referências a AzureRM e Az

Se fizer referência aos módulos Az em seu módulo, certifique-se de que você também não está fazendo referência aos módulos AzureRM. Você não pode usar os dois conjuntos de módulos ao mesmo tempo. 

### <a name="version-folder"></a>Pasta de versão

NÃO inclua uma pasta de versão no pacote **.zip** para o seu módulo.  Este problema é menos preocupante para os runbooks, mas causa um problema com o serviço de Configuração do Estado (DSC). A Azure Automation cria a pasta de versão automaticamente quando o módulo é distribuído a nós gerenciados pelo DSC. Se existir uma pasta de versão, você acaba com duas instâncias. Aqui está uma estrutura de pasta de exemplo para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informações de ajuda

Inclua uma sinopse, descrição e ajude uri para cada cmdlet em seu módulo. No PowerShell, você pode definir informações de `Get-Help` ajuda para cmdlets usando o cmdlet. O exemplo a seguir mostra como definir uma sinopse e ajudar o URI em um arquivo de módulo **.psm1.**

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

  Fornecer essas informações mostra `Get-Help` texto de ajuda através do cmdlet no console PowerShell. Este texto também é exibido no portal Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Tipo de conexão

Se o módulo se conectar a um serviço externo, defina um [tipo de conexão](#adding-a-connection-type-to-your-module). Cada cmdlet no módulo deve aceitar um objeto de conexão (uma instância desse tipo de conexão) como parâmetro. Os usuários mapeiam parâmetros do ativo de conexão aos parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. O exemplo do runbook a seguir, baseado no exemplo da `ContosoConnection` seção anterior, usa um ativo de conexão Contoso chamado para acessar os recursos do Contoso e devolver dados do serviço externo. Neste exemplo, os campos são `UserName` mapeados para as propriedades e `Password` propriedades de um `PSCredential` objeto e, em seguida, passados para o cmdlet.

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

Defina o tipo de saída para todos os cmdlets do seu módulo. A definição de um tipo de saída para um cmdlet permite que o IntelliSense ajude a determinar as propriedades de saída do cmdlet durante a autoria. Essa prática é especialmente útil durante a autoria de runbook gráfico, para o qual o conhecimento em tempo de design é fundamental para uma experiência fácil do usuário com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` `MyOutputType` onde é um tipo válido. Para saber `OutputType`mais sobre , consulte [Sobre funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir `OutputType` é um exemplo de adição a um cmdlet:

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

### <a name="cmdlet-state"></a>Cmdlet

Torne todos os cmdlets do seu módulo apátridas. Vários trabalhos de runbook podem `AppDomain` ser executados simultaneamente no mesmo processo e caixa de areia. Se houver algum estado compartilhado nesses níveis, os empregos podem afetar uns aos outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar. Aqui está um exemplo do que NÃO fazer:

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

### <a name="module-dependency"></a>Dependência do módulo

Certifique-se de que o módulo está totalmente contido em um pacote que pode ser copiado usando xcopy. Os módulos de automação do Azure são distribuídos às caixas de areia de Automação quando os runbooks são executados. Os módulos devem funcionar independentemente do host que os executa. 

Você deve ser capaz de fechar e mover um pacote de módulo e tê-lo funcionando normalmente quando importado para o ambiente PowerShell de outro host. Para que isso aconteça, certifique-se de que o módulo não dependa de nenhum arquivo fora da pasta do módulo que seja fechado quando o módulo for importado para a Automação Azure. 

Seu módulo não deve depender de nenhuma configuração de registro única em um host. Exemplos são as configurações que são feitas quando um produto é instalado. 

### <a name="module-file-paths"></a>Caminhos de arquivo de módulo

Certifique-se de que todos os arquivos do módulo tenham caminhos com menos de 140 caracteres. Quaisquer caminhos com mais de 140 caracteres de comprimento causam problemas com a importação de runbooks. O Azure Automation não pode importar um arquivo com tamanho de `Import-Module`caminho acima de 140 caracteres na sessão PowerShell com .

## <a name="importing-modules"></a>Módulos de importação

Esta seção define várias maneiras pelas quais você pode importar um módulo para sua conta de Automação. 

### <a name="import-modules-in-azure-portal"></a>Módulos de importação no portal Azure

Para importar um módulo no portal Azure:

1. Abra sua conta de Automação.
2. Selecione **módulos** em **Recursos Compartilhados**.
3. Clique **em Adicionar um módulo**. 
4. Selecione o arquivo **.zip** que contém o módulo.
5. Clique em **OK** para iniciar o processo de importação.

### <a name="import-modules-using-powershell"></a>Módulos de importação usando powershell

Você pode usar o cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) para importar um módulo para sua conta de Automação. O cmdlet leva uma URL para um pacote .zip do módulo.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo diretamente da Galeria PowerShell. Certifique-se `ModuleName` de `ModuleVersion` pegar e da [Galeria PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Módulos de importação da Galeria PowerShell

Você pode importar módulos da [Galeria PowerShell](https://www.powershellgallery.com) diretamente da galeria ou da sua conta de Automação.

Para importar um módulo diretamente da Galeria PowerShell:

1. Vá https://www.powershellgallery.com e procure o módulo para importar.
2. Clique **em Implantar no Azure Automation** na guia Automação do **Azure** em **Opções de instalação**. Esta ação abre o portal Azure. 
3. Na página Importar, selecione sua conta de Automação e clique em **OK**.

![Módulo de importação da Galeria PowerShell](../media/modules/powershell-gallery.png)

Para importar um módulo da Galeria PowerShell diretamente da sua conta de automação:

1. Selecione **módulos** em **Recursos Compartilhados**. 
2. Na página Módulos, clique **em Procurar galeria**e, em seguida, procure na galeria por um módulo. 
3. Selecione o módulo para importar e clique **em Importar**. 
4. Na página Importar, clique em **OK** para iniciar o processo de importação.

![Importação da Galeria PowerShell do portal Azure](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Excluindo módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, você pode excluí-lo da sua conta de Automação. Você não pode excluir as versões originais dos [módulos padrão importados](#default-modules) quando você cria uma conta de Automação. Se o módulo a ser excluído for uma versão mais recente de um dos [módulos padrão,](#default-modules)ele volta para a versão que foi instalada com sua conta de Automação. Caso contrário, qualquer módulo que você excluir da sua conta de Automação será removido.

### <a name="delete-modules-in-azure-portal"></a>Excluir módulos no portal Azure

Para remover um módulo no portal Azure:

1. Navegue até sua conta de automação e selecione **Módulos** em **Recursos Compartilhados**. 
2. Selecione o módulo que deseja remover. 
3. Na página **Módulo,** **selecione Excluir**. Se este módulo for um dos [módulos padrão,](#default-modules)ele volta para a versão que existia quando a conta de Automação foi criada.

### <a name="delete-modules-using-powershell"></a>Excluir módulos usando o PowerShell

Para remover um módulo através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Adicionando um tipo de conexão ao seu módulo

Você pode fornecer um tipo de [conexão](../automation-connections.md) personalizada para usar em sua conta de Automação adicionando um arquivo de metadados opcional ao seu módulo. Este arquivo especifica um tipo de conexão Azure Automation a ser usado com os cmdlets do módulo em sua conta de Automação. Para saber mais sobre a autoria de um módulo PowerShell, consulte [Como escrever um módulo de script PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Use uma conexão personalizada no portal Azure](../media/modules/connection-create-new.png)

O arquivo especificando propriedades do tipo de conexão é chamado ** &lt;ModuleName&gt;-Automation.json** e é encontrado na pasta de módulo do seu arquivo **compactado .zip.** Este arquivo contém os campos de uma conexão que são necessários para se conectar ao sistema ou serviço que o módulo representa. A configuração permite a criação de um tipo de conexão no Azure Automation. Usando este arquivo, você pode definir os nomes de campo, tipos e se os campos são criptografados ou opcionais para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo **.json** que define as propriedades do nome de usuário e da senha:

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

* Para obter mais informações sobre como usar módulos Azure PowerShell, consulte [Iniciar com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Para saber mais sobre a criação de módulos PowerShell, consulte [Writing a Windows PowerShell Module](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
