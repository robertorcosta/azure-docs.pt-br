---
title: Parâmetros de entrada do Runbook
description: Os parâmetros de entrada do runbook aumentam a flexibilidade dos runbooks permitindo transmitir dados para um runbook quando ele é iniciado. Este artigo descreve os diferentes cenários em que os parâmetros de entrada são usados em runbooks.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 274ee0fe98281e733994f2d5df38886409cbc913
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273645"
---
# <a name="runbook-input-parameters"></a>Parâmetros de entrada do Runbook

Os parâmetros de entrada do runbook aumentam a flexibilidade de um runbook, permitindo que os dados sejam passados para ele quando iniciados. Esses parâmetros permitem que as ações de runbook sejam direcionadas para cenários e ambientes específicos. Este artigo descreve a configuração e o uso de parâmetros de entrada em seus runbooks.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Configurando parâmetros de entrada

Você pode configurar parâmetros de entrada para runbooks do PowerShell, fluxo de trabalho do PowerShell, gráfico e Python. Um runbook pode ter vários parâmetros com tipos de dados diferentes, ou não ter nenhum parâmetro. Os parâmetros de entrada podem ser obrigatórios ou opcionais, e você pode usar valores padrão para parâmetros opcionais.

Você atribui valores aos parâmetros de entrada para um runbook ao iniciá-lo. Você pode iniciar um runbook do portal do Azure, um serviço Web ou o PowerShell. Você também pode iniciá-lo como um runbook filho, que é chamado de embutido em outro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurar parâmetros de entrada em runbooks do PowerShell

Os runbooks de fluxo de trabalho do PowerShell e do PowerShell na automação do Azure dão suporte a parâmetros de entrada definidos por meio das propriedades a seguir. 

| **Propriedade** | **Descrição** |
|:--- |:--- |
| Tipo |Obrigatório. O tipo de dados esperado para o valor do parâmetro. Qualquer tipo .NET é válido. |
| {1&gt;Nome&lt;1} |Obrigatório. O nome do parâmetro. Esse nome deve ser exclusivo dentro do runbook, deve começar com uma letra e pode conter apenas letras, números ou caracteres de sublinhado. |
| Obrigatório |Opcional. Valor booliano que especifica se o parâmetro requer um valor. Se você definir isso como **true**, um valor deverá ser fornecido quando o runbook for iniciado. Se você definir como **false**, um valor será opcional. Se você não especificar um valor para a propriedade **obrigatória** , o PowerShell considerará o parâmetro de entrada opcional por padrão. |
| Valor padrão |Opcional. Um valor que será usado para o parâmetro se nenhum valor de entrada for passado quando o runbook for iniciado. O runbook pode definir um valor padrão para qualquer parâmetro. |

O Windows PowerShell dá suporte a mais atributos de parâmetros de entrada do que aqueles listados acima, como validação, aliases e conjuntos de parâmetros. No entanto, a automação do Azure atualmente dá suporte apenas às propriedades de parâmetro de entrada listadas.

Como exemplo, vamos examinar uma definição de parâmetro em um runbook de fluxo de trabalho do PowerShell. Essa definição tem o seguinte formato geral, em que vários parâmetros são separados por vírgulas.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Agora, vamos configurar os parâmetros de entrada para um runbook de fluxo de trabalho do PowerShell que gera detalhes sobre máquinas virtuais, uma única VM ou todas as VMs em um grupo de recursos. Este runbook tem dois parâmetros, conforme mostrado na seguinte captura de tela: o nome da máquina virtual (*VMName*) e o nome do grupo de recursos (*resourceGroupName*).

![Fluxo de trabalho do PowerShell de automação](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Nessa definição de parâmetro, os parâmetros de entrada são parâmetros simples do tipo cadeia de caracteres.

Observe que os runbooks de fluxo de trabalho do PowerShell e do PowerShell dão suporte a todos os tipos simples e tipos complexos, como **Object** ou **PSCredential** para parâmetros de entrada. Se o runbook tiver um parâmetro de entrada de objeto, você deverá usar uma tabela de hash do PowerShell com pares de nome-valor para passar um valor. Por exemplo, você tem o seguinte parâmetro em um runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Nesse caso, você pode passar o valor a seguir para o parâmetro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando você não passa um valor para um parâmetro de cadeia de caracteres opcional com um valor padrão nulo, o valor do parâmetro é uma cadeia de caracteres vazia em vez de **NULL**.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Como configurar parâmetros de entrada em runbooks gráficos

Para ilustrar a configuração de parâmetros de entrada para um runbook gráfico, vamos criar um runbook que produza detalhes sobre máquinas virtuais, uma única VM ou todas as VMs dentro de um grupo de recursos. Para obter detalhes, consulte [meu primeiro runbook gráfico](automation-first-runbook-graphical.md).

Um runbook gráfico usa essas principais atividades de runbook:

* Configuração da conta Executar como do Azure para autenticar com o Azure. 
* Definição de um cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) para obter as propriedades da VM.
* Uso da atividade [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) para gerar os nomes de VM. 

A atividade **Get-AzVM** define duas entradas, o nome da VM e o nome do grupo de recursos. Como esses nomes podem ser diferentes cada vez que o runbook é iniciado, você deve adicionar parâmetros de entrada ao seu runbook para aceitar essas entradas. Consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).

Siga estas etapas para configurar os parâmetros de entrada.

1. Selecione o runbook gráfico na página **Runbooks** e clique em **Editar**.
2. No editor gráfico, clique no botão **entrada e saída** e **adicione entrada** para abrir o painel parâmetro de entrada do runbook.

   ![Runbook gráfico de automação](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. O controle entrada e saída exibe uma lista de parâmetros de entrada que são definidos para o runbook. Aqui você pode adicionar um novo parâmetro de entrada ou editar a configuração de um parâmetro de entrada existente. Para adicionar um novo parâmetro para o runbook, clique em **Adicionar entrada** para abrir a folha **parâmetro de entrada do runbook** , na qual você pode configurar parâmetros usando as propriedades definidas em [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).

    ![Como adicionar nova entrada](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Crie dois parâmetros com as seguintes propriedades a serem usadas pela atividade **Get-AzVM** e clique em **OK**.

   * Parâmetro 1:
        * **Nome** -- **VMName**
        * **Tipo** --cadeia de caracteres
        * **Obrigatório** -- **não**

   * Parâmetro 2:
        * **Nome** -- **resourceGroupName**
        * **Tipo** --cadeia de caracteres
        * **Obrigatório** -- **não**
        * **Valor padrão** -- **personalizado**
        * Valor padrão personalizado--nome do grupo de recursos que contém as VMs

5. Exiba os parâmetros no controle de entrada e saída. 
6. Clique em **OK** novamente e, em seguida, clique em **salvar**.
7. Clique em **publicar** para publicar seu runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurar parâmetros de entrada em runbooks Python

Ao contrário do PowerShell, do fluxo de trabalho do PowerShell e dos runbooks gráficos, os runbooks do Python não assumem parâmetros nomeados. O editor de runbook analisa todos os parâmetros de entrada como uma matriz de valores de argumento. Você pode acessar a matriz importando o módulo **Sys** para o script Python e, em seguida, usando a matriz **Sys. argv** . É importante observar que o primeiro elemento da matriz, `sys.argv[0]`, é o nome do script. Portanto, o primeiro parâmetro de entrada real é *Sys. argv [1]* .

Para obter um exemplo de como usar parâmetros de entrada em um runbook Python, consulte [Meu primeiro runbook Python na Automação do Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Atribuindo valores a parâmetros de entrada em runbooks

Esta seção descreve várias maneiras de passar valores para parâmetros de entrada em runbooks. Você pode atribuir valores de parâmetro quando você:

* [Iniciar um runbook](#start-a-runbook-and-assign-parameters)
* [Testar um runbook](#test-a-runbook-and-assign-parameters)
* [Vincular uma agenda para o runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Criar um webhook para o runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Iniciar um runbook e atribuir parâmetros

Um runbook pode ser iniciado de várias maneiras: por meio do portal do Azure, com um webhook, com cmdlets do PowerShell, com a API REST ou com o SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Iniciar um runbook publicado usando o portal do Azure e atribuir parâmetros

Quando você [inicia o runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) no portal do Azure, a folha **Iniciar runbook** é aberta e você pode inserir valores para os parâmetros que você criou.

![Como começar a usar o portal](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

No rótulo abaixo da caixa de entrada, você pode ver as propriedades que foram definidas para definir atributos de parâmetro, por exemplo, obrigatório ou opcional, tipo, valor padrão. O balão de ajuda ao lado do nome do parâmetro também define as principais informações necessárias para tomar decisões sobre valores de entrada de parâmetro. 

> [!NOTE]
> Parâmetros de cadeia de caracteres dão suporte a valores vazios do tipo cadeia de caracteres. Inserir **[EmptyString]** na caixa do parâmetro de entrada passa uma cadeia de caracteres vazia para o parâmetro. Além disso, os parâmetros de cadeia de caracteres não dão suporte a NULL. Se você não passar qualquer valor para um parâmetro de cadeia de caracteres, o PowerShell o interpretará como nulo.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Iniciar um runbook publicado usando cmdlets do PowerShell e atribuir parâmetros

* **Azure Resource Manager cmdlets:** Você pode iniciar um runbook de automação que foi criado em um grupo de recursos usando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
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
> Quando você inicia um runbook usando cmdlets do PowerShell, um parâmetro padrão, *MicrosoftApplicationManagementStartedBy*, é criado com o valor **PowerShell**. Você pode exibir esse parâmetro no painel de detalhes do trabalho.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Iniciar um runbook usando um SDK e atribuir parâmetros

* **Método de Azure Resource Manager:** Você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Método de modelo de implantação clássico do Azure:** você pode iniciar um runbook usando o SDK de uma linguagem de programação. Abaixo está um snippet de código em C# para iniciar um runbook em sua conta de Automação. Você pode exibir todo o código em nosso [repositório do GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

   Para iniciar esse método, crie um dicionário para armazenar os parâmetros de runbook *VMName* e *resourceGroupName* e seus valores. Em seguida, inicie o runbook. Abaixo está o snippet de código em C# para chamar o método definido acima.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Iniciar um runbook usando a API REST e atribuir parâmetros

Você pode criar e iniciar um trabalho de runbook com a API REST de automação do Azure usando o método **Put** com o seguinte URI de solicitação: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

No URI de solicitação, substitua os seguintes parâmetros:

* *SubscriptionId*: sua ID de assinatura do Azure.  
* *resourceGroupName*: o nome do grupo de recursos para a conta de automação.
* *automationAccountName*: o nome da conta de automação que é hospedada no serviço de nuvem especificado.  
* *JobName*: o GUID para o trabalho. Os GUIDs no PowerShell podem ser criados usando `[GUID]::NewGuid().ToString()*`.

Para passar parâmetros para o trabalho de runbook, use o corpo da solicitação. Ele usa as seguintes informações, fornecidas no formato JSON:

* Nome do runbook: obrigatório. O nome do runbook para que o trabalho seja iniciado.  
* Parâmetros de runbook: opcional. Um dicionário da lista de parâmetros no formato (nome, valor), em que nome é do tipo cadeia de caracteres e o valor pode ser qualquer valor JSON válido.

Se você quiser iniciar o runbook **Get-AzureVMTextual** criado anteriormente com *VMName* e *resourceGroupName* como parâmetros, use o formato JSON a seguir para o corpo da solicitação.

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

Um código de status HTTP 201 será retornado se o trabalho for criado com êxito. Para obter mais informações sobre cabeçalhos de resposta e o corpo da resposta, consulte [criar um trabalho de runbook usando a API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testar um runbook e atribuir parâmetros

Quando você [testa a versão de rascunho do seu runbook](automation-testing-runbook.md) usando a opção de teste, a página de **teste** é aberta. Use esta página para configurar valores para os parâmetros que você criou.

![Como testar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Vincular um agendamento para um runbook e atribuir parâmetros

Você pode [vincular uma agenda](automation-schedules.md) ao runbook para que ele seja iniciado em um momento específico. Você atribui parâmetros de entrada ao criar a agenda. O runbook usa esses valores quando é iniciado pela agenda. Você não pode salvar o agendamento até que todos os valores de parâmetro obrigatórios sejam fornecidos.

![Como agendar e atribuir parâmetros](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Criar um webhook de um runbook e atribuir parâmetros

Você pode criar um [webhook](automation-webhooks.md) para seu runbook e configurar parâmetros de entrada do runbook. Você não pode salvar o webhook até que todos os valores de parâmetro obrigatório sejam fornecidos.

![Como criar o webhook e atribuir parâmetros](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando você executa um runbook usando um webhook, o parâmetro de entrada predefinido *[WebhookData](automation-webhooks.md#details-of-a-webhook)* é enviado, juntamente com os parâmetros de entrada que você define. 

![Parâmetro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Passando um objeto JSON para um runbook

Isso pode ser útil para armazenar os dados que você deseja passar para um runbook em um arquivo JSON. Por exemplo, você pode criar um arquivo JSON que contém todos os parâmetros que você deseja passar para um runbook. Para fazer isso, você deve converter o código JSON em uma cadeia de caracteres e, em seguida, converter a cadeia de caracteres em um objeto do PowerShell antes de passá-lo para o runbook.

Esta seção usa um exemplo no qual um script do PowerShell chama [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) para iniciar um runbook do PowerShell, passando o conteúdo do arquivo JSON para o runbook. O runbook do PowerShell inicia uma VM do Azure recuperando os parâmetros para a VM do objeto JSON.

### <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o código a seguir em um arquivo de texto e salve-o como `test.json` em algum lugar no computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook do PowerShell chamado **Test-JSON** na automação do Azure. Veja [meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve utilizar um objeto como um parâmetro de entrada. O runbook pode usar as propriedades definidas no arquivo JSON.

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

1. Entre no Azure, conforme mostrado. Depois disso, você será solicitado a inserir suas credenciais do Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Para runbooks do PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são aliases para **Connect-AzAccount**. Observe que esses aliases não estão disponíveis para runbooks gráficos. Um runbook gráfico só pode usar o **Connect-AzAccount** em si.

1. Obtenha o conteúdo do arquivo JSON salvo e converta-o em uma cadeia de caracteres. `JsonPath` é o caminho em que você salvou o arquivo JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converta o conteúdo da cadeia de caracteres de `$json` em um objeto do PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Crie uma tabela de hash para os parâmetros para **Start-AzAutomationRunbook**. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observe que você está definindo o valor de *parâmetros* para o objeto do PowerShell que contém os valores do arquivo JSON.
1. Inicie o runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [Como iniciar um runbook](automation-starting-a-runbook.md).
* Para editar um runbook textual, consulte [Como editar runbooks textuais](automation-edit-textual-runbook.md).
* Para editar um runbook gráfico, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
