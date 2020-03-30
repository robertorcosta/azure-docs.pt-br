---
title: Gerenciar módulos na automação do Azure
description: Este artigo descreve como gerenciar módulos no Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278331"
---
# <a name="manage-modules-in-azure-automation"></a>Gerenciar módulos na automação do Azure

A Azure Automation oferece a capacidade de importar módulos PowerShell em sua conta de automação para serem usados pelos runbooks baseados em PowerShell. Esses módulos podem ser módulos personalizados que você criou, a partir da Galeria PowerShell, ou dos módulos AzureRM e Az para o Azure. Quando você cria uma conta de automação, alguns módulos são importados por padrão.

## <a name="import-modules"></a>Importar módulos

Existem várias maneiras de importar um módulo para sua conta de automação. As seções a seguir mostram as diferentes maneiras de importar um módulo.

> [!NOTE]
> O caminho máximo de um arquivo em um módulo a ser usado no Azure Automation é de 140 caracteres. Qualquer caminho com mais de 140 caracteres não poderá `Import-Module`ser importado para a sessão PowerShell com .

### <a name="powershell"></a>PowerShell

Você pode usar o [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) para importar um módulo para sua conta de automação. O cmdlet leva uma url para um pacote zip do módulo.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Você também pode usar o mesmo cmdlet para importar um módulo diretamente da Galeria PowerShell. Certifique-se de pegar **ModuleName** e **ModuleVersion** da [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, navegue até sua conta de automação e selecione **Módulos** em **Recursos Compartilhados**. Clique **+ Adicionar um módulo**. Selecione um arquivo **.zip** que contenha seu módulo e clique **em Ok** para iniciar o processo de importação.

### <a name="powershell-gallery"></a>Galeria do PowerShell

Os módulos da galeria PowerShell podem ser importados diretamente da [Galeria PowerShell](https://www.powershellgallery.com) ou da sua Conta de Automação.

Para importar um módulo da Galeria https://www.powershellgallery.com PowerShell, vá e procure o módulo que deseja importar. Clique **em Implantar no Azure Automation** na guia Automação do **Azure** em **Opções de instalação**. Essa ação abre o portal do Azure. Na página **Importar,** selecione sua conta de automação e clique em **OK**.

![Módulo de importação da Galeria PowerShell](../media/modules/powershell-gallery.png)

Você também pode importar módulos da Galeria PowerShell diretamente da sua Conta de Automação. Em sua conta de automação, selecione **Módulos** em **Recursos Compartilhados**. Na página dos módulos, clique **em Procurar galeria**e, em seguida, procure na Galeria PowerShell por um módulo. Selecione o módulo que deseja importar e clique **em Importar**. Na página **Importar,** clique em **OK** para iniciar o processo de importação.

![Importação da Galeria PowerShell do portal Azure](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Excluir módulos

Se você tiver problemas com um módulo ou precisar reverter para uma versão anterior de um módulo, você pode excluí-lo da sua Conta de Automação. Não é possível excluir a versão original dos [módulos padrão importados](#default-modules) quando você cria uma conta de automação. Se o módulo que você deseja excluir for uma versão mais recente de um dos [módulos padrão instalados,](#default-modules) ele voltará para a versão que foi instalada com sua Conta de Automação. Caso contrário, qualquer módulo que você excluir de sua conta de automação será removido.

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, navegue até sua conta de automação e selecione **Módulos** em **Recursos Compartilhados**. Selecione o módulo que deseja remover. Na página **Módulo,** **selecione Excluir**. Se este módulo for um dos [módulos padrão,](#default-modules)ele será revertido para a versão que estava presente quando a Conta de Automação foi criada.

### <a name="powershell"></a>PowerShell

Para remover um módulo através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Cmdlets internos

A seguir está uma lista de cmdlets no módulo interno `Orchestrator.AssetManagement.Cmdlets` que é importado para cada Conta de Automação. Esses cmdlets são acessíveis em seus runbooks e configurações de DSC e permitem que você interaja com seus ativos dentro de sua Conta de Automação. Além disso, os cmdlets internos permitem que você recupere segredos dos campos **de Variáveis** criptografadas, **Credenciais**e **Conexões** criptografadas. Os cmdlets Do Azure PowerShell não são capazes de recuperar esses segredos. Esses cmdlets não exigem que você se conecte implicitamente ao Azure ao usá-los, como usar uma Conta run as para autenticar o Azure.

>[!NOTE]
>Esses cmdlets internos estão disponíveis em um Windows Hybrid Runbook Worker, eles não estão disponíveis em um Linux Hybrid Runbook Worker. Use os módulos [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) ou [Az correspondentes](../az-modules.md) para runbooks executados diretamente no computador ou contra recursos em seu ambiente. 
>

|Nome|Descrição|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Trabalho de automação de espera|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Adicione um tipo de conexão ao seu módulo

Você pode fornecer um tipo de [conexão](../automation-connections.md) personalizada para você usar em sua Conta de Automação adicionando um arquivo opcional ao seu módulo. Este arquivo é um arquivo de metadados especificando um tipo de conexão Azure Automation a ser usado com os cmdlets do módulo em sua Conta de Automação. Para conseguir isso, você deve primeiro saber como criar um módulo PowerShell. Para obter mais informações sobre a autoria do módulo, consulte [Como escrever um módulo de script PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Use uma conexão personalizada no portal Azure](../media/modules/connection-create-new.png)

Para adicionar um tipo de conexão Azure Automation, `<ModuleName>-Automation.json` seu módulo deve conter um arquivo com o nome que especifica as propriedades do tipo de conexão. O arquivo json é colocado dentro da pasta do módulo do seu arquivo .zip compactado. Esse arquivo contém os campos de uma conexão que é necessária para estabelecer conexão com o sistema ou serviço que o módulo representa. A configuração acaba criando um tipo de conexão no Azure Automation. Usando este arquivo, você pode definir os nomes de campo, tipos e se os campos devem ser criptografados ou opcionais, para o tipo de conexão do módulo. O exemplo a seguir é um modelo no formato de arquivo json que define uma propriedade de nome de usuário e senha:

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

## <a name="module-best-practices"></a>Práticas recomendadas do módulo

Os módulos PowerShell podem ser importados na Automação do Azure de modo a disponibilizar os cmdlets para uso em runbooks e recursos DSC para uso em configurações de DSC. Em segundo plano, a Automação do Azure armazena esses módulos e, no momento da execução do trabalho do runbook e do trabalho de compilação do DSC, os carrega nas áreas restritas da Automação do Azure, onde runbooks são executados e configurações de DSC compiladas. Todos os recursos DSC em módulos também são colocados automaticamente no servidor de pull de DSC de Automação. Eles podem ser obtidos por máquinas quando elas aplicam configurações de DSC.

Recomendamos que você considere o seguinte ao criar um módulo PowerShell para uso na Automação Azure:

* NÃO inclua uma pasta de versão dentro do pacote .zip.  Este problema é menos preocupante para os runbooks, mas causará um problema com o serviço de Configuração do Estado.  O Azure Automation criará a pasta de versão automaticamente quando o módulo for distribuído a nós gerenciados pelo DSC e, se existir uma pasta de versão, você acabará com duas instâncias.  Exemplo de estrutura de pasta para um módulo DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Incluem uma sinopse, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, você pode usar o cmdlet **Get-Help** para definir certas informações de ajuda com relação aos cmdlets para que o usuário receba ajuda sobre como usá-los. O exemplo a seguir mostra como definir uma sinopse e ajudar o URI em um arquivo de módulo .psm1:

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

  Fornecer essas informações mostra essa ajuda usando o cmdlet **Get-Help** no console do PowerShell. Esta descrição também é exibida no portal Azure.

  ![Ajuda do Módulo de Integração](../media/modules/module-activity-description.png)

* Se o módulo se conectar a um serviço externo, ele deve conter um [tipo de conexão](#add-a-connection-type-to-your-module). Cada cmdlet no módulo deve ser capaz de receber um objeto de conexão (uma instância desse tipo de conexão) como um parâmetro. Os usuários mapeiam parâmetros do ativo de conexão aos parâmetros correspondentes do cmdlet cada vez que chamam um cmdlet. Com base no exemplo do runbook acima, ele usa um exemplo de recurso de conexão Contoso chamado ContosoConnection para acessar recursos da Contoso e devolver dados do serviço externo.

  No exemplo a seguir, os campos são mapeados `PSCredential` para as propriedades Nome de usuário e senha de um objeto e, em seguida, passados para o cmdlet.

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

  Você pode habilitar um comportamento como o do exemplo anterior para seus cmdlets, permitindo que eles aceitem um objeto de conexão diretamente como um parâmetro, em vez de apenas campos de conexão para parâmetros. Normalmente, convém ter um conjunto de parâmetros para cada um, para que um usuário que não esteja usando a Automação do Azure possa chamar seus cmdlets sem construir uma tabela de hash para agir como o objeto de conexão. O conjunto `UserAccount`de parâmetros é usado para passar as propriedades do campo de conexão. `ConnectionObject`permite que você passe a conexão diretamente.

* Defina o tipo de saída para todos os cmdlets do módulo. A definição de um tipo de saída para um cmdlet permite o IntelliSense no tempo de criação para ajudar você a determinar as propriedades de saída do cmdlet, para uso durante a criação. Isso é especialmente útil durante a criação gráfica do runbook da Automação, quando o conhecimento do tempo de design é fundamental para uma experiência de usuário facilitada com o seu módulo.

Adicione `[OutputType([<MyOutputType>])]` onde MyOutputType é um tipo válido. Para saber mais sobre OutputType, consulte [Sobre funções OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). O código a seguir `OutputType` é um exemplo de adição a um cmdlet:

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

* Torne todos os cmdlets no módulo cmdlets sem estado. Vários trabalhos de runbook podem ser executados simultaneamente no mesmo AppDomain e no mesmo processo e sandbox. Se houver algum estado compartilhado nesses níveis, os empregos podem afetar uns aos outros. Esse comportamento pode levar a problemas intermitentes e difíceis de diagnosticar.  Veja um exemplo do que não fazer:

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

* O módulo deve estar totalmente contido em um pacote xcopyable. Os módulos de automação do Azure são distribuídos para as caixas de areia de Automação quando os runbooks precisam ser executados. Os módulos precisam funcionar independentemente do host em que estão em execução. Você deve poder compactar e mover um pacote de módulo, e este deve funcionar normalmente quando importado para outro ambiente do PowerShell do host. Para que isso aconteça, o módulo não deve depender de qualquer arquivo fora da pasta do módulo. Essa pasta é a pasta que é compactada quando o módulo é importado para a Automação do Azure. O módulo também não deve depender de qualquer configuração de registro exclusiva em um host, como as configurações definidas quando um produto é instalado. Todos os arquivos do módulo devem ter um caminho inferior a 140 caracteres. Quaisquer caminhos acima de 140 caracteres causarão problemas na importação do seu runbook. Se essa melhor prática não for seguida, o módulo não poderá ser usado na Automação do Azure.  

* Se estiver fazendo referência aos [módulos Az do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seu módulo, certifique-se de que não esteja fazendo referência também a `AzureRM`. O módulo `Az` não pode ser usado em conjunto com os módulos `AzureRM`. Há suporte para `Az` em runbooks, mas não são importados por padrão. Para saber mais sobre os módulos `Az` e considerações nas quais prestar atenção, confira [Suporte ao módulo Az na Automação do Azure](../az-modules.md).

## <a name="default-modules"></a>Módulos padrão

A tabela a seguir lista os módulos importados por padrão quando uma conta de automação é criada. Os módulos listados abaixo podem ter versões mais recentes deles importados, mas a versão original não pode ser removida da sua Conta de Automação, mesmo se você excluir uma versão mais recente deles.

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

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como criar os Módulos do PowerShell, consulte [Escrevendo um Módulo do Windows PowerShell](/powershell/scripting/developer/windows-powershell)
