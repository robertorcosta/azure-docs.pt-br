---
title: Gerenciar módulos na automação do Azure
description: Este artigo descreve como gerenciar módulos na automação do Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82f02f0ac11c80161f709b3b493306bc8aafc8bd
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435459"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na automação do Azure

A automação do Azure fornece a capacidade de importar módulos do PowerShell para sua conta de automação a ser usada pelos runbooks baseados no PowerShell. Esses módulos podem ser módulos personalizados que você criou, do Galeria do PowerShell, ou os módulos AzureRM e AZ para o Azure. Quando você cria uma conta de automação, alguns módulos são importados por padrão.

## <a name="import-modules"></a>Importar módulos

Há várias maneiras pelas quais você pode importar um módulo para sua conta de automação. As seções a seguir mostram as diferentes maneiras de importar um módulo.

> [!NOTE]
> O caminho máximo de um arquivo em um módulo a ser usado na automação do Azure é de 140 caracteres. Qualquer caminho acima de 140 caracteres não será capaz de ser importado para a sessão do PowerShell com `Import-Module`.

### <a name="powershell"></a>PowerShell

Você pode usar o [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para sua conta de automação. O cmdlet usa uma URL para um pacote zip de módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Portal do Azure

Na portal do Azure, navegue até sua conta de automação e selecione **módulos** em **recursos compartilhados**. Clique em **+ Adicionar um módulo**. Selecione um arquivo **. zip** que contenha seu módulo e clique em **OK** para iniciar a importação do processo.

### <a name="powershell-gallery"></a>Galeria do PowerShell

Os módulos da galeria do PowerShell podem ser importados do [Galeria do PowerShell](https://www.powershellgallery.com) diretamente ou da sua conta de automação.

Para importar um módulo do Galeria do PowerShell, acesse https://www.powershellgallery.com e procure o módulo que deseja importar. Clique em **implantar na automação do Azure** na guia **automação do Azure** em **Opções de instalação**. Essa ação abre o portal do Azure. Na página **importar** , selecione sua conta de automação e clique em **OK**.

![Módulo de importação de Galeria do PowerShell](../media/modules/powershell-gallery.png)

Você também pode importar módulos do Galeria do PowerShell diretamente da sua conta de automação. Em sua conta de automação, selecione **módulos** em **recursos compartilhados**. Na página módulos, clique em **procurar na Galeria**e pesquise o Galeria do PowerShell de um módulo. Selecione o módulo que você deseja importar e clique em **importar**. Na página **importar** , clique em **OK** para iniciar o processo de importação.

![Galeria do PowerShell importação do portal do Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Excluir módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, poderá excluí-lo da sua conta de automação. Não é possível excluir a versão original dos [módulos padrão](#default-modules) que são importados quando você cria uma conta de automação. Se o módulo que você deseja excluir for uma versão mais recente de um dos [módulos padrão](#default-modules) instalados, ele será revertido para a versão que foi instalada com sua conta de automação. Caso contrário, qualquer módulo que você excluir da sua conta de automação será removido.

### <a name="azure-portal"></a>Portal do Azure

Na portal do Azure, navegue até sua conta de automação e selecione **módulos** em **recursos compartilhados**. Selecione o módulo que você deseja remover. Na página do **módulo** , clcick **delete**. Se esse módulo for um dos [módulos padrão](#default-modules), ele será revertido para a versão que estava presente quando a conta de automação foi criada.

### <a name="powershell"></a>PowerShell

Para remover um módulo por meio do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlets internos

Veja a seguir uma lista de cmdlets no módulo interno `Orchestrator.AssetManagement.Cmdlets` que é importado para cada conta de automação. Esses cmdlets podem ser acessados em seus runbooks e configurações DSC e permitem que você interaja com seus ativos na sua conta de automação. Além disso, os cmdlets internos permitem que você recupere segredos de valores de **variáveis** criptografados, **credenciais**e campos de **conexão** criptografados. Os cmdlets Azure PowerShell não podem recuperar esses segredos. Esses cmdlets não exigem que você se conecte implicitamente ao Azure ao usá-los, como usar uma conta Executar como para autenticar no Azure.

|name|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Adicionar um tipo de conexão ao seu módulo

Você pode fornecer um [tipo de conexão](../automation-connections.md) personalizada para usar em sua conta de automação adicionando um arquivo opcional ao seu módulo. Esse arquivo é um arquivo de metadados que especifica um tipo de conexão de automação do Azure a ser usado com os cmdlets do módulo em sua conta de automação. Para conseguir isso, você deve primeiro saber como criar um módulo do PowerShell. Para obter mais informações sobre a criação de módulo, consulte [como gravar um módulo de script do PowerShell](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Usar uma conexão personalizada no portal do Azure](../media/modules/connection-create-new.png)

Para adicionar um tipo de conexão de automação do Azure, seu módulo deve conter um arquivo com o nome `<ModuleName>-Automation.json` que especifica as propriedades do tipo de conexão. O arquivo JSON é colocado dentro da pasta do módulo do arquivo. zip compactado. Esse arquivo contém os campos de uma conexão que é necessária para se conectar ao sistema ou serviço que o módulo representa. A configuração acaba criando um tipo de conexão na automação do Azure. Usando esse arquivo, você pode definir os nomes de campo, tipos e se os campos devem ser criptografados ou opcionais, para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo JSON que define uma propriedade de nome de usuário e senha:

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

## <a name="module-best-practices"></a>Práticas recomendadas de módulo

Os módulos PowerShell podem ser importados na Automação do Azure de modo a disponibilizar os cmdlets para uso em runbooks e recursos DSC para uso em configurações de DSC. Em segundo plano, a Automação do Azure armazena esses módulos e, no momento da execução do trabalho do runbook e do trabalho de compilação do DSC, os carrega nas áreas restritas da Automação do Azure, onde runbooks são executados e configurações de DSC compiladas. Todos os recursos DSC em módulos também são colocados automaticamente no servidor de pull de DSC de Automação. Eles podem ser obtidos por máquinas quando elas aplicam configurações de DSC.

Recomendamos que você considere o seguinte ao criar um módulo do PowerShell para uso na automação do Azure:

* Não inclua uma pasta de versão dentro do pacote. zip.  Esse problema é uma preocupação menor para runbooks, mas causará um problema com o serviço de configuração de estado.  A automação do Azure criará a pasta de versão automaticamente quando o módulo for distribuído para nós gerenciados pela DSC e, se uma pasta de versão existir, você acabará com duas instâncias.  Exemplo de estrutura de pastas para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Incluem uma sinopse, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, você pode usar o cmdlet **Get-Help** para definir certas informações de ajuda com relação aos cmdlets para que o usuário receba ajuda sobre como usá-los. O exemplo a seguir mostra como definir um URI de Sinopse e ajuda para em um arquivo de módulo. psm1:

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

  Fornecer essas informações mostra essa ajuda usando o cmdlet **Get-Help** no console do PowerShell. Essa descrição também é exibida no portal do Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

* Se o módulo se conectar a um serviço externo, ele deverá conter um [tipo de conexão](#add-a-connection-type-to-your-module). Cada cmdlet no módulo deve ser capaz de receber um objeto de conexão (uma instância desse tipo de conexão) como um parâmetro. Os usuários mapeiam parâmetros do ativo de conexão para os parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. Com base no exemplo de runbook acima, ele usa um ativo de conexão contoso de exemplo chamado ContosoConnection para acessar os recursos da Contoso e retornar dados do serviço externo.

  No exemplo a seguir, os campos são mapeados para as propriedades UserName e password de um objeto `PSCredential` e, em seguida, passado para o cmdlet.

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

  Você pode habilitar um comportamento como o do exemplo anterior para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto de parâmetros `UserAccount`, é usado para passar as propriedades do campo de conexão. `ConnectionObject` permite que você passe a conexão diretamente.

* Defina o tipo de saída para todos os cmdlets no módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de criação para ajudar você a determinar as propriedades de saída do cmdlet, para uso durante a criação. Isso é especialmente útil durante a criação gráfica do runbook da Automação, quando o conhecimento do tempo de design é fundamental para uma experiência de usuário facilitada com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` em que myoutputtype é um tipo válido. Para saber mais sobre OutputType, consulte [sobre o Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir é um exemplo de adição de `OutputType` a um cmdlet:

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

* Torne todos os cmdlets no módulo cmdlets sem estado. Vários trabalhos de runbook podem ser executados simultaneamente no mesmo AppDomain e no mesmo processo e área restrita. Se houver qualquer estado compartilhado nesses níveis, os trabalhos poderão afetar os outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar.  Veja um exemplo do que não fazer:

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

* O módulo deve estar totalmente contido em um pacote habilitado para xcopy. Os módulos de automação do Azure são distribuídos para as áreas restritas de automação quando os runbooks precisam ser executados. Os módulos precisam funcionar independentemente do host em que estão em execução. Você deve poder compactar e mover um pacote de módulo, e este deve funcionar normalmente quando importado para outro ambiente do PowerShell do host. Para que isso aconteça, o módulo não deve depender de qualquer arquivo fora da pasta do módulo. Essa pasta é a pasta que é compactada quando o módulo é importado para a Automação do Azure. O módulo também não deve depender de qualquer configuração de registro exclusiva em um host, como as configurações definidas quando um produto é instalado. Todos os arquivos no módulo devem ter um caminho inferior a 140 caracteres. Todos os caminhos acima de 140 caracteres causarão problemas ao importar o runbook. Se essa melhor prática não for seguida, o módulo não poderá ser usado na Automação do Azure.  

* Se estiver fazendo referência aos [módulos Az do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seu módulo, certifique-se de que não esteja fazendo referência também a `AzureRM`. O módulo `Az` não pode ser usado em conjunto com os módulos `AzureRM`. Há suporte para `Az` em runbooks, mas não são importados por padrão. Para saber mais sobre os módulos `Az` e considerações nas quais prestar atenção, confira [Suporte ao módulo Az na Automação do Azure](../az-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos que são importados por padrão quando uma conta de automação é criada. Os módulos listados abaixo podem ter versões mais recentes importadas, mas a versão original não pode ser removida da sua conta de automação mesmo que você exclua uma versão mais recente delas.

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
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft. PowerShell. Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1,1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre como criar os Módulos do PowerShell, consulte [Escrevendo um Módulo do Windows PowerShell](/powershell/scripting/developer/windows-powershell)
