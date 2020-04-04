---
title: Parâmetros de entrada do Runbook
description: Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks permitindo transmitir dados para um runbook quando ele é iniciado. Este artigo descreve os diferentes cenários em que os parâmetros de entrada são usados em runbooks.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656023"
---
# <a name="runbook-input-parameters"></a>Parâmetros de entrada do Runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade de um livro de execução, permitindo que os dados sejam passados para ele quando foriniciado. Esses parâmetros permitem que ações de runbook sejam direcionadas para cenários e ambientes específicos. Este artigo descreve a configuração e o uso de parâmetros de entrada em seus runbooks.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Configuração de parâmetros de entrada

Você pode configurar parâmetros de entrada para runbooks PowerShell, PowerShell Workflow, gráficos e Python. Um runbook pode ter vários parâmetros com tipos de dados diferentes, ou não ter nenhum parâmetro. Os parâmetros de entrada podem ser obrigatórios ou opcionais, e você pode usar valores padrão para parâmetros opcionais.

Você atribui valores aos parâmetros de entrada de um livro de execução quando o inicia. Você pode iniciar um runbook a partir do portal Azure, um serviço web ou PowerShell. Você também pode iniciá-lo como um runbook filho, que é chamado de embutido em outro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurar parâmetros de entrada em runbooks do PowerShell

Os runbooks powershell e powershell workflow no Azure Automation suportam parâmetros de entrada definidos através das seguintes propriedades. 

| **Propriedade** | **Descrição** |
|:--- |:--- |
| Type |Obrigatórios. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| Nome |Obrigatórios. O nome do parâmetro. Este nome deve ser único dentro do manual, deve começar com uma letra e pode conter apenas letras, números ou caracteres sublinhados. |
| Obrigatório |Opcional. Valor booleano especificando se o parâmetro requer um valor. Se você definir isso como True, um valor deve ser fornecido quando o livro de execução for iniciado. Se você definir isso como Falso, um valor é opcional. Se você não especificar um `Mandatory` valor para a propriedade, o PowerShell considerará o parâmetro de entrada opcional por padrão. |
| Valor padrão |Opcional. Um valor que é usado para o parâmetro se nenhum valor de entrada for passado quando o livro de execução for iniciado. O runbook pode definir um valor padrão para qualquer parâmetro. |

O Windows PowerShell suporta mais atributos de parâmetros de entrada do que os listados acima, como validação, aliases e conjuntos de parâmetros. No entanto, a Azure Automation atualmente suporta apenas as propriedades de parâmetro de entrada listadas.

Como exemplo, vamos olhar para uma definição de parâmetro em um runbook PowerShell Workflow. Esta definição tem a seguinte forma geral, onde vários parâmetros são separados por commas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Agora vamos configurar os parâmetros de entrada de um runbook PowerShell Workflow que produz detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs dentro de um grupo de recursos. Este runbook tem dois parâmetros, como mostrado na captura`VMName`de tela a seguir:`resourceGroupName`o nome da máquina virtual ( ) e o nome do grupo de recursos ( ).

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nesta definição de parâmetro, os parâmetros de entrada são parâmetros simples da cadeia de tipo.

Observe que os runbooks PowerShell e PowerShell Workflow suportam todos os tipos simples e tipos complexos, como `Object` ou `PSCredential` para parâmetros de entrada. Se o seu runbook tiver um parâmetro de entrada de objeto, você deve usar uma hashtable PowerShell com pares de valor de nome para passar em um valor. Por exemplo, você tem o seguinte parâmetro em um runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Neste caso, você pode passar o seguinte valor para o parâmetro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando você não passa um valor para um parâmetro de String opcional com um valor padrão nulo, o valor do parâmetro é uma seqüência vazia em vez de Nulo.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Como configurar parâmetros de entrada em runbooks gráficos

Para ilustrar a configuração dos parâmetros de entrada de um runbook gráfico, vamos criar um runbook que produz detalhes sobre máquinas virtuais, seja uma única VM ou todas as VMs dentro de um grupo de recursos. Para obter detalhes, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md).

Um runbook gráfico usa essas principais atividades do runbook:

* Configuração da conta Azure Run As para autenticar com o Azure. 
* Definição de um [cmdlet Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) para obter propriedades VM.
* Uso da atividade ['Gravar-Saída'](/powershell/module/microsoft.powershell.utility/write-output) para produzir os nomes vm. 

A `Get-AzVM` atividade define duas entradas, o nome VM e o nome do grupo de recursos. Uma vez que esses nomes podem ser diferentes cada vez que o livro de execução é iniciado, você deve adicionar parâmetros de entrada ao seu livro de execução para aceitar essas entradas. Consulte a [autoria gráfica em Azure Automation](automation-graphical-authoring-intro.md).

Siga estas etapas para configurar os parâmetros de entrada.

1. Selecione o runbook gráfico na página Runbooks e clique em **Editar**.
2. No editor gráfico, clique no botão **Entrada e saída** e adicione **entrada** para abrir o painel Parâmetro de entrada do Runbook.

   ![Runbook gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. O controle Entrada e Saída exibe uma lista de parâmetros de entrada definidos para o runbook. Aqui você pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro para o livro de execução, clique em **Adicionar entrada** para abrir a lâmina de parâmetro de entrada **do Runbook,** onde você pode configurar parâmetros usando as propriedades definidas na [autoria gráfica no Azure Automation](automation-graphical-authoring-intro.md).

    ![Como adicionar nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as seguintes propriedades a serem usadas pela `Get-AzVM` atividade e clique em **OK**.

   * Parâmetro 1:
        * **Nome** -- **VMName**
        * **Tipo** - String
        * **Obrigatório****Não**  -- 

   * Parâmetro 2:
        * **Recurso** -- **nomeGrupoNome**
        * **Tipo** - String
        * **Obrigatório****Não**  -- 
        * **Valor padrão** -- **Personalizado**
        * Valor padrão personalizado -- Nome do grupo de recursos que contém as VMs

5. Veja os parâmetros no controle de entrada e saída. 
6. Clique em **OK** novamente e clique **em Salvar**.
7. Clique **em Publicar** para publicar seu livro de execução.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em runbooks Python

Ao contrário do PowerShell, do PowerShell Workflow e dos runbooks gráficos, os runbooks python não tomam parâmetros nomeados. O editor de runbook analisa todos os parâmetros de entrada como uma matriz de valores de argumento. Você pode acessar a matriz `sys` importando o módulo para `sys.argv` o seu script Python e, em seguida, usando a matriz. É importante notar que o primeiro elemento `sys.argv[0]`da matriz, é o nome do script. Portanto, o primeiro parâmetro `sys.argv[1]`de entrada real é .

Para obter um exemplo de como usar parâmetros de entrada em um runbook Python, consulte [Meu primeiro runbook Python na Automação do Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Atribuindo valores aos parâmetros de entrada em runbooks

Esta seção descreve várias maneiras de passar valores para parâmetros de entrada em runbooks. Você pode atribuir valores de parâmetro quando você:

* [Iniciar um runbook](#start-a-runbook-and-assign-parameters)
* [Testar um runbook](#test-a-runbook-and-assign-parameters)
* [Vincular um cronograma para o runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Criar um webhook para o runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de muitas maneiras: através do portal Azure, com um webhook, com cmdlets PowerShell, com a API REST, ou com o SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Inicie um runbook publicado usando o portal Azure e atribua parâmetros

Quando você [inicia o runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) no portal Azure, a lâmina **Start Runbook** é aberta e você pode inserir valores para os parâmetros que você criou.

![Como começar a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

No rótulo abaixo da caixa de entrada, você pode ver as propriedades que foram definidas para definir atributos de parâmetro, por exemplo, obrigatórios ou opcionais, tipo, valor padrão. O balão de ajuda ao lado do nome do parâmetro também define as informações-chave necessárias para tomar decisões sobre valores de entrada de parâmetros. 

> [!NOTE]
> Os parâmetros de string suportam valores vazios do tipo String. Entrar `[EmptyString]` na caixa de parâmetros de entrada passa uma seqüência vazia para o parâmetro. Além disso, os parâmetros de seqüência não suportam Nulo. Se você não passar qualquer valor para um parâmetro de string, o PowerShell o interpretará como Nulo.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Inicie um runbook publicado usando cmdlets PowerShell e atribua parâmetros

* **Cmdlets do Azure Resource Manager:** Você pode iniciar um runbook de automação criado em um grupo de recursos usando [start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets de modelo de implantação clássico do Azure:** você pode iniciar um runbook de automação criado em um grupo de recursos padrão usando [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Quando você inicia um runbook usando cmdlets PowerShell, um parâmetro `MicrosoftApplicationManagementStartedBy` `PowerShell`padrão, é criado com o valor . Você pode ver este parâmetro no painel de detalhes do trabalho.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Inicie um runbook usando um SDK e atribua parâmetros

* **Método do Azure Resource Manager:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Método de modelo de implantação clássico do Azure:** você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Para iniciar este método, crie um dicionário `VMName` `resourceGroupName` para armazenar os parâmetros do runbook e seus valores. Em seguida, inicie o runbook. Abaixo está o snippet de código em C# para chamar o método definido acima.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Inicie um runbook usando a API REST e atribua parâmetros

Você pode criar e iniciar um trabalho de runbook com `PUT` a API Azure Automation REST usando o método com a seguinte solicitação URI:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

No URI de solicitação, substitua os seguintes parâmetros:

* `subscriptionId`: Seu ID de assinatura do Azure.  
* `resourceGroupName`: O nome do grupo de recursos para a conta automação.
* `automationAccountName`: O nome da conta de Automação hospedada dentro do serviço de nuvem especificado.  
* `jobName`: O GUID para o trabalho. GuiDs no PowerShell podem ser `[GUID]::NewGuid().ToString()*`criados usando .

Para passar parâmetros para o trabalho do manual, use o corpo de solicitação. Ele leva as seguintes informações, fornecidas no formato JSON:

* Nome do Runbook: Obrigatório. O nome do runbook para que o trabalho seja iniciado.  
* Parâmetros do Runbook: Opcional. Um dicionário da lista de parâmetros no formato (nome, valor), onde o nome é do tipo String e valor pode ser qualquer valor JSON válido.

Se você quiser iniciar o **runbook Get-AzureVMTextual** criado anteriormente com `VMName` e `resourceGroupName` como parâmetros, use o seguinte formato JSON para o corpo de solicitação.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Um código de status HTTP 201 será retornado se o trabalho for criado com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo de resposta, consulte [criar um trabalho de runbook usando a API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testar um runbook e atribuir parâmetros

Quando você [testa a versão de rascunho do seu livro de execução](automation-testing-runbook.md) usando a opção de teste, a página teste é aberta. Use esta página para configurar valores para os parâmetros criados.

![Como testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Vincular um agendamento para um runbook e atribuir parâmetros

Você pode [vincular uma agenda](automation-schedules.md) ao runbook para que ele seja iniciado em um momento específico. Você atribui parâmetros de entrada ao criar a agenda. O runbook usa esses valores quando é iniciado pela agenda. Você não pode salvar o cronograma até que todos os valores de parâmetros obrigatórios sejam fornecidos.

![Como agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Criar um webhook de um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar parâmetros de entrada do runbook. Você não pode salvar o webhook até que todos os valores de parâmetro obrigatórios sejam fornecidos.

![Como criar o webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando você executa um runbook usando um webhook, o parâmetro `[WebhookData](automation-webhooks.md)` de entrada predefinido é enviado, juntamente com os parâmetros de entrada que você define. 

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Passando um objeto JSON para um runbook

Isso pode ser útil para armazenar os dados que você deseja passar para um runbook em um arquivo JSON. Por exemplo, você pode criar um arquivo JSON que contenha todos os parâmetros que você deseja passar para um runbook. Para fazer isso, você deve converter o código JSON em uma string e, em seguida, converter a seqüência para um objeto PowerShell antes de passá-lo para o runbook.

Esta seção usa um exemplo no qual um script PowerShell chama [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) para iniciar um runbook powerShell, passando o conteúdo do arquivo JSON para o runbook. O runbook PowerShell inicia uma VM Azure recuperando os parâmetros para a VM do objeto JSON.

### <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o seguinte código em um arquivo de texto e salve-o como **test.json** em algum lugar do seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook powershell chamado **Test-Json** no Azure Automation. Veja [meu primeiro runbook powershell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve utilizar um objeto como um parâmetro de entrada. O runbook pode então usar as propriedades definidas no arquivo JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Salve e publique este runbook na sua conta de Automação.

### <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora você pode chamar o runbook de seu computador local usando o Azure PowerShell. 

1. Faça login no Azure como mostrado. Depois, você é solicitado a inserir suas credenciais do Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Para runbooks powershell e `Add-AzAccount` `Add-AzureRMAccount` são `Connect-AzAccount`pseudônimos para . Observe que esses pseudônimos não estão disponíveis para runbooks gráficos. Um runbook gráfico só `Connect-AzAccount` pode usar a si mesmo.

1. Obtenha o conteúdo do arquivo JSON salvo e converta-o em uma seqüência. `JsonPath`indica o caminho onde você salvou o arquivo JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converta o `$json` conteúdo da seqüência de string s em um objeto PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Criar uma tabela hashpara `Start-AzAutomationRunbook`os parâmetros para . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observe que você está definindo o valor de `Parameters` para o objeto do PowerShell que contém os valores do arquivo JSON.
1. Iniciar o runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [Como iniciar um runbook](automation-starting-a-runbook.md).
* Para editar um runbook textual, consulte [Como editar runbooks textuais](automation-edit-textual-runbook.md).
* Para editar um runbook gráfico, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
