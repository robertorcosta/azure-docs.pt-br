---
title: Iniciar um runbook de Automação do Azure com um webhook
description: Um webhook que permite que um cliente inicie um runbook na Automação do Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367016"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um runbook de Automação do Azure com um webhook

Um webhook permite que um serviço externo inicie um runbook específico no Azure Automation através de uma única solicitação HTTP. Os serviços externos incluem serviços Azure DevOps, GitHub, logs do Monitor Azure e aplicativos personalizados. Esse serviço pode usar um webhook para iniciar um runbook sem implementar uma solução completa usando a API de Automação Azure. Você pode comparar webhooks com outros métodos de iniciar um runbook no [Iniciar um runbook no Azure Automation](automation-starting-a-runbook.md).

> [!NOTE]
> O uso de um webhook para iniciar um runbook Python não é suportado.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Propriedades do Webhook

A tabela a seguir descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Nome do webhook. Você pode fornecer qualquer nome que quiser, já que não está exposto ao cliente. Ele é usado apenas por você para identificar o runbook na Automação do Azure. Como melhor prática, você deve atribuir ao webhook um nome relacionado ao cliente que o utiliza. |
| URL |URL do webhook. Este é o endereço exclusivo que um cliente liga com um POST HTTP para iniciar o runbook vinculado ao webhook. Ele é gerado automaticamente quando você cria o webhook. Você não pode especificar uma URL personalizada. <br> <br> A URL contém um token de segurança que permite que um sistema de terceiros invoque o runbook sem mais autenticação. Por essa razão, você deve tratar a URL como uma senha. Por razões de segurança, você só pode visualizar a URL no portal Azure ao criar o webhook. Anote a URL em um local seguro para uso futuro. |
| Data de validade | Data de validade do webhook, após o qual ele não pode mais ser usado. Você pode modificar a data de validade após a criação do webhook, desde que o webhook não tenha expirado. |
| habilitado | Configuração indicando se o webhook está ativado por padrão quando ele é criado. Se você definir esta propriedade como Desativada, nenhum cliente poderá usar o webhook. Você pode definir esta propriedade quando criar o webhook ou qualquer outra hora após sua criação. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parâmetros usados quando o webhook inicia um runbook

Um webhook pode definir valores para parâmetros de runbook que são usados quando o livro de execução é iniciado. O webhook deve incluir valores para quaisquer parâmetros obrigatórios do runbook e pode incluir valores para parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado mesmo após a criação do webhook. Vários webhooks vinculados a um único runbook podem usar valores diferentes do parâmetro do manual. Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook.

Para receber dados do cliente, o livro de `WebhookData`execução suporta um único parâmetro chamado . Este parâmetro define um objeto contendo dados que o cliente inclui em uma solicitação POST.

![Propriedades do WebhookData](media/automation-webhooks/webhook-data-properties.png)

O parâmetro `WebhookData` tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| `WebhookName` | Nome do webhook. |
| `RequestHeader` | Hashtable contendo os cabeçalhos da solicitação POST recebida. |
| `RequestBody` | Corpo do pedido POST de entrada. Este corpo retém qualquer formatação de dados, como string, JSON, XML ou forma codificada. O runbook deve ser escrito para trabalhar com o formato de dados esperado. O runbook deve ser escrito para trabalhar com o formato de dados esperado. |

Não há configuração do webhook necessário `WebhookData` para suportar o parâmetro, e o manual não é necessário para aceitá-lo. Se o manual não definir o parâmetro, quaisquer detalhes da solicitação enviada do cliente serão ignorados.

> [!NOTE]
> Ao chamar um webhook, o cliente deve sempre armazenar quaisquer valores de parâmetro no caso de a chamada falhar. Se houver uma interrupção de rede ou problema de conexão, o aplicativo não poderá recuperar chamadas com falha de webhook.

Se você especificar `WebhookData` um valor para a criação do webhook, ele será substituído quando o webhook iniciar o runbook com os dados da solicitação do CLIENTE POST. Isso acontece mesmo que o aplicativo não inclua nenhum dado no órgão de solicitação. 

Se você iniciar um runbook que define `WebhookData` usando um mecanismo diferente de `WebhookData` um webhook, você pode fornecer um valor para que o runbook reconheça. Esse valor deve ser um objeto `WebhookData` com as [mesmas propriedades](#webhook-properties) do parâmetro para que o `WebhookData` runbook possa trabalhar com ele assim como funciona com objetos reais passados por um webhook.

Por exemplo, se você está iniciando o seguinte runbook do portal Azure e deseja passar alguns dados de webhook de amostra para testes, você deve passar os dados em JSON na interface do usuário.

![Parâmetro WebhookData da interface do usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o próximo exemplo de runbook, vamos `WebhookData`definir as seguintes propriedades para :

* **WebhookName**: MyWebhook
* **Solicitbody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Agora passamos o seguinte objeto JSON `WebhookData` na ui para o parâmetro. Este exemplo, com retornos de transporte e caracteres newline, corresponde ao formato que é passado a partir de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o parâmetro WebhookData na interface do usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> O Azure Automation registra os valores de todos os parâmetros de entrada com o trabalho de livro de execução. Assim, qualquer entrada fornecida pelo cliente na solicitação de webhook é registrada e disponível para qualquer pessoa com acesso ao trabalho de automação. Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.

## <a name="webhook-security"></a>Segurança do Webhook

A segurança de um webhook depende da privacidade de sua URL, que contém um token de segurança que permite que o webhook seja invocado. O Azure Automation não executa nenhuma autenticação em uma solicitação, desde que seja feita na URL correta. Por essa razão, seus clientes não devem usar webhooks para runbooks que executam operações altamente sensíveis sem usar um meio alternativo de validar a solicitação.

Você pode incluir a lógica dentro de um runbook para determinar se ele é chamado por um webhook. Faça com que `WebhookName` o manual `WebhookData` verifique a propriedade do parâmetro. O runbook pode realizar uma validação adicional `RequestHeader` `RequestBody` procurando informações específicas nas propriedades e nas propriedades.

Outra estratégia é fazer com que o runbook realize alguma validação de uma condição externa quando receber uma solicitação de webhook. Por exemplo, considere um runbook que é chamado pelo GitHub sempre que houver um novo compromisso com um repositório do GitHub. O runbook pode se conectar ao GitHub para validar que um novo compromisso ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criando um webhook

Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no Portal do Azure.

1. Na página Runbooks no portal Azure, clique no runbook que o webhook começa a visualizar os detalhes do runbook. Certifique-se de que o campo **status do** runbook está definido como **Publicado**.
2. Clique em **Webhook** na parte superior da página para abrir a página Adicionar webhook.
3. Clique **em Criar novo webhook** para abrir a página Criar webhook.
4. Preencha os campos **Nome** e **Data de Expiração** para o webhook e especifique se ele deve ser ativado. Consulte [as propriedades do Webhook](#webhook-properties) para obter mais informações sobre essas propriedades.
5. Clique no ícone copiar e pressione Ctrl + C para copiar a URL do webhook. Em seguida, anote-o em um local seguro. 

    > [!NOTE]
    > Quando você cria o webhook, não é possível recuperar a URL novamente.

   ![URL de Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook. Se o runbook tiver parâmetros obrigatórios, você não poderá criar o webhook a menos que você forneça valores.
1. Clique em **Criar** para criar o webhook.

## <a name="using-a-webhook"></a>Usando um webhook

Para usar um webhook depois de criado, seu `POST` cliente deve emitir uma solicitação HTTP com a URL para o webhook. A sintaxe do é:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes `POST` códigos de devolução da solicitação.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceita |A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 |Solicitação incorreta |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li>  </ul> |
| 404 |Não encontrado |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

Supondo que a solicitação seja bem sucedida, a resposta webhook contém o ID de trabalho no formato JSON, conforme mostrado abaixo. Ele contém um único ID de trabalho, mas o formato JSON permite potenciais melhorias futuras.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar o status da conclusão do webhook ou quando o trabalho de runbook é concluído. Ele pode descobrir essas informações usando o ID de trabalho com outro mecanismo, como o [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou a [API de Automação Do Azure](/rest/api/automation/job).

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Renovando um webhook

Quando um webhook é criado, ele tem um período de validade de dez anos, após o qual expira automaticamente. Uma vez que um webhook tenha expirado, você não pode reativá-lo. Você só pode removê-lo e depois recriá-lo. 

Você pode estender um webhook que não atingiu seu tempo de validade. Para estender um webhook:

1. Navegue até o runbook que contém o webhook. 
2. Selecione **Webhooks** em **Recursos**. 
3. Clique no webhook que deseja estender. 
4. Na página Webhook, escolha uma nova data e hora de validade e clique **em Salvar**.

## <a name="sample-runbook"></a>Runbook de exemplo

O runbook de exemplo a seguir aceita os dados do webhook e inicia as máquinas virtuais especificadas no corpo da solicitação. Para testar este runbook, em sua conta de Automação em **Runbooks,** clique **em Criar um runbook**. Se você não souber como criar um runbook, confira [Criando um runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> Para runbooks PowerShell não `Add-AzAccount` gráficos e `Add-AzureRMAccount` são pseudônimos para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Você pode usar esses cmdlets ou atualizar [seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Você pode precisar atualizar seus módulos mesmo se você acabou de criar uma nova conta de Automação.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Testando o exemplo

O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook. Qualquer idioma que possa fazer uma solicitação HTTP pode usar um webhook. O Windows PowerShell é usado aqui como exemplo.

O runbook está esperando uma lista de máquinas virtuais formatadas em JSON no corpo da solicitação. O runbook valida também que os cabeçalhos contêm uma mensagem definida para validar se o chamador do webhook é válido.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

O exemplo a seguir mostra o corpo da solicitação `RequestBody` que `WebhookData`está disponível para o runbook na propriedade de . Este valor é formatado em JSON para ser compatível com o formato incluído no corpo da solicitação.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

A imagem a seguir mostra a solicitação sendo enviada do Windows PowerShell e a resposta resultante. A ID do trabalho é extraída da resposta e convertida em uma cadeia de caracteres.

![Botão Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber como usar o Azure Automation para agir em alertas do Azure, consulte [Use um alerta para ativar um runbook do Azure Automation](automation-create-alert-triggered-runbook.md).
