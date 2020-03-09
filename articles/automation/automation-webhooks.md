---
title: Iniciar um runbook de Automação do Azure com um webhook
description: Um webhook que permite que um cliente inicie um runbook na Automação do Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 043350db2c5372fc81fbb2b68155a4ac75457208
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373442"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um runbook de Automação do Azure com um webhook

Um webhook permite que um serviço externo inicie um runbook específico na automação do Azure por meio de uma única solicitação HTTP. Os serviços externos incluem Azure DevOps Services, GitHub, logs de Azure Monitor e aplicativos personalizados. Esse serviço pode usar um webhook para iniciar um runbook sem implementar uma solução completa usando a API de automação do Azure. Você pode comparar WebHooks com outros métodos de iniciar um runbook no [início de um runbook na automação do Azure](automation-starting-a-runbook.md).

> [!NOTE]
> Não há suporte para o uso de um webhook para iniciar um runbook do Python.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Propriedades de webhook

A tabela a seguir descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| {1&gt;Nome&lt;1} |Nome do webhook. Você pode fornecer qualquer nome desejado, já que ele não é exposto ao cliente. Ele é usado apenas por você para identificar o runbook na Automação do Azure. Como melhor prática, você deve atribuir ao webhook um nome relacionado ao cliente que o utiliza. |
| {1&gt;URL&lt;1} |URL do webhook. Esse é o endereço exclusivo que um cliente chama com um HTTP POST para iniciar o runbook vinculado ao webhook. Ele é gerado automaticamente quando você cria o webhook. Você não pode especificar uma URL personalizada. <br> <br> A URL contém um token de segurança que permite que um sistema de terceiros invoque o runbook sem autenticação adicional. Por esse motivo, você deve tratar a URL como uma senha. Por motivos de segurança, você só pode exibir a URL no portal do Azure ao criar o webhook. Anote a URL em um local seguro para uso futuro. |
| Data de expiração | Data de validade do webhook, após o qual ele não pode mais ser usado. Você pode modificar a data de validade após a criação do webhook, desde que o webhook não tenha expirado. |
| Habilitado | Configuração que indica se o webhook está habilitado por padrão quando é criado. Se você definir essa propriedade como desabilitada, nenhum cliente poderá usar o webhook. Você pode definir essa propriedade ao criar o webhook ou qualquer outra hora após sua criação. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parâmetros usados quando o webhook inicia um runbook

Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado. O webhook deve incluir valores para quaisquer parâmetros de runbook obrigatórios e pode incluir valores para parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado mesmo após a criação do webhook. Vários WebHooks vinculados a um único runbook podem usar diferentes valores de parâmetro de runbook. Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook.

Para receber dados do cliente, o runbook dá suporte a um único parâmetro chamado *WebhookData*. Esse parâmetro define um objeto que contém dados que o cliente inclui em uma solicitação POST.

![Propriedades de WebhookData](media/automation-webhooks/webhook-data-properties.png)

O parâmetro *WebhookData* tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName | Nome do webhook. |
| RequestHeader | Tabela de hash que contém os cabeçalhos da solicitação POST de entrada. |
| RequestBody | Corpo da solicitação POST de entrada. Esse corpo mantém qualquer formatação de dados, como cadeia de caracteres, JSON, XML ou codificado por formulário. O runbook deve ser escrito para trabalhar com o formato de dados esperado. O runbook deve ser escrito para trabalhar com o formato de dados esperado. |

Não há nenhuma configuração do webhook necessária para dar suporte ao parâmetro *WebhookData* e o runbook não é necessário para aceitá-lo. Se o runbook não definir o parâmetro, todos os detalhes da solicitação enviada do cliente serão ignorados.

> [!NOTE]
> Ao chamar um webhook, o cliente sempre deve armazenar qualquer valor de parâmetro em caso de falha da chamada. Se houver uma interrupção de rede ou um problema de conexão, o aplicativo não poderá recuperar chamadas de webhook com falha.

Se você especificar um valor para *WebhookData* na criação do webhook, ele será substituído quando o webhook iniciar o runbook com os dados da solicitação post do cliente. Isso acontece mesmo que o aplicativo não inclua dados no corpo da solicitação. 

Se você iniciar um runbook que define *WebhookData* usando um mecanismo diferente de um webhook, poderá fornecer um valor para *WebhookData* que o runbook reconheça. Esse valor deve ser um objeto com as mesmas [Propriedades](#webhook-properties) que o parâmetro *WebhookData* para que o runbook possa trabalhar com ele da mesma forma que funciona com objetos *WebhookData* reais passados por um webhook.

Por exemplo, se você estiver iniciando o runbook a seguir no portal do Azure e desejar passar alguns dados de webhook de exemplo para teste, deverá passar os dados em JSON na interface do usuário.

![Parâmetro WebhookData da interface do usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o próximo exemplo de runbook, vamos definir as seguintes propriedades *WebhookData*:

* **Webhookname**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Agora, passamos o seguinte objeto JSON na interface do usuário para o parâmetro *WebhookData* . Este exemplo, com retornos de carro e caracteres de nova linha, corresponde ao formato passado de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o parâmetro WebhookData na interface do usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> A automação do Azure registra em log os valores de todos os parâmetros de entrada com o trabalho de runbook. Portanto, qualquer entrada fornecida pelo cliente na solicitação de webhook é registrada e disponível para qualquer pessoa com acesso ao trabalho de automação. Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.

## <a name="webhook-security"></a>Segurança de webhook

A segurança de um webhook depende da privacidade de sua URL, que contém um token de segurança que permite que o webhook seja invocado. A automação do Azure não executa nenhuma autenticação em uma solicitação, desde que seja feita na URL correta. Por esse motivo, os clientes não devem usar WebHooks para runbooks que executam operações altamente confidenciais sem usar um meio alternativo de validar a solicitação.

Você pode incluir lógica em um runbook para determinar se ele é chamado por um webhook. Faça com que o runbook Verifique a propriedade **webhookname** do parâmetro *WebhookData* . O runbook pode executar uma validação adicional procurando informações específicas nas propriedades **RequestHeader** e **RequestBody** .

Outra estratégia é fazer com que o runbook execute algumas validações de uma condição externa ao receber uma solicitação de webhook. Por exemplo, considere um runbook chamado pelo GitHub sempre que houver uma nova confirmação para um repositório GitHub. O runbook pode se conectar ao GitHub para validar que uma nova confirmação ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criando um webhook

Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no Portal do Azure.

1. Na página Runbooks no portal do Azure, clique no runbook que o webhook inicia para exibir os detalhes do runbook. Verifique se o campo **status** do runbook está definido como **publicado**.
2. Clique em **webhook** na parte superior da página para abrir a página Adicionar webhook.
3. Clique em **criar novo webhook** para abrir a página Criar webhook.
4. Preencha os campos de **nome** e **data de validade** do webhook e especifique se ele deve ser habilitado. Consulte [Propriedades do webhook](#webhook-properties) para obter mais informações sobre essas propriedades.
5. Clique no ícone copiar e pressione Ctrl + C para copiar a URL do webhook. Em seguida, anote-o em um local seguro. 

    > [!NOTE]
    > Depois de criar o webhook, você não poderá recuperar a URL novamente.

   ![URL de Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook. Se o runbook tiver parâmetros obrigatórios, você não poderá criar o webhook, a menos que forneça valores.
1. Clique em **Criar** para criar o webhook.

## <a name="using-a-webhook"></a>Usando um webhook

Para usar um webhook depois que ele tiver sido criado, o cliente deverá emitir uma solicitação HTTP POST com a URL para o webhook. A sintaxe do é:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de retorno da solicitação POST.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceita |A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 |Solicitação Inválida |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li>  </ul> |
| 404 |Não encontrado |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro Interno do Servidor |A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

Supondo que a solicitação foi bem-sucedida, a resposta do webhook contém a ID do trabalho no formato JSON, conforme mostrado abaixo. Ele contém uma única ID de trabalho, mas o formato JSON permite possíveis aprimoramentos futuros.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar o status da conclusão do webhook ou quando o trabalho de runbook é concluído. Ele pode descobrir essas informações usando a ID do trabalho com outro mecanismo, como o [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou a [API de automação do Azure](/rest/api/automation/job).

## <a name="renew-webhook"></a>Renovando um webhook

Quando um webhook é criado, ele tem um período de tempo de validade de dez anos, após o qual ele expira automaticamente. Quando um webhook tiver expirado, você não poderá reativá-lo. Você só pode remover e, em seguida, recriá-lo. 

Você pode estender um webhook que não atingiu seu tempo de expiração. Para estender um webhook:

1. Navegue até o runbook que contém o webhook. 
2. Selecione **Webhooks** em **Recursos**. 
3. Clique no webhook que você deseja estender. 
4. Na página webhook, escolha uma nova data e hora de expiração e clique em **salvar**.

## <a name="sample-runbook"></a>Runbook de exemplo

O runbook de exemplo a seguir aceita os dados do webhook e inicia as máquinas virtuais especificadas no corpo da solicitação. Para testar esse runbook, em sua conta de automação em **Runbooks**, clique em **criar um runbook**. Se você não souber como criar um runbook, confira [Criando um runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> Para runbooks não gráficos do PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são aliases para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de automação para as versões mais recentes. Talvez seja necessário atualizar seus módulos mesmo se você acabou de criar uma nova conta de automação.

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

O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook. Qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook. O Windows PowerShell é usado aqui como exemplo.

O runbook está esperando uma lista de máquinas virtuais formatadas em JSON no corpo da solicitação. O runbook é validado também que os cabeçalhos contêm uma mensagem definida para validar que o chamador do webhook é válido.

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

O exemplo a seguir mostra o corpo da solicitação que está disponível para o runbook na propriedade **RequestBody** do *WebhookData*. Esse valor é formatado em JSON para ser compatível com o formato incluído no corpo da solicitação.

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

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Para saber como usar a automação do Azure para tomar medidas sobre alertas do Azure, consulte [usar um alerta para disparar um runbook de automação do Azure](automation-create-alert-triggered-runbook.md).
