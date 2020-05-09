---
title: Solucionando problemas de erros de runbook da automação do Azure
description: Saiba como solucionar e resolver problemas que podem ser encontrados com runbooks de automação do Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.custom: has-adal-ref
ms.openlocfilehash: 70f3c52adc10556c358ed75a75fd023ffb21a813
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855098"
---
# <a name="troubleshoot-runbook-errors"></a>Solucionar erros de runbook

 Este artigo descreve vários erros de runbook que podem ocorrer e como resolvê-los.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação do Azure, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="diagnose-runbook-issues"></a>Diagnosticar problemas de runbook

Ao receber erros durante a execução do runbook na automação do Azure, você pode usar as seguintes etapas para ajudar a diagnosticar os problemas:

1. Verifique se o script de runbook é executado com êxito no computador local.

    Para ver a referência de linguagem e os módulos de aprendizado, consulte os documentos do [PowerShell](/powershell/scripting/overview) ou do [Python](https://docs.python.org/3/). Executar o script localmente pode descobrir e resolver erros comuns, como:

      * Módulos ausentes
      * Erros de sintaxe
      * Erros lógicos

1. Investigue os [fluxos de erro](../automation-runbook-output-and-messages.md#runbook-output)do runbook.

    Examine esses fluxos de mensagens específicas e compare-as com os erros documentados neste artigo.

1. Verifique se os nós e o espaço de trabalho de automação têm os módulos necessários.

    Se o runbook importar módulos, verifique se eles estão disponíveis para sua conta de automação usando as etapas em [Importar módulos](../shared-resources/modules.md#import-modules). Atualize seus módulos do PowerShell para a versão mais recente seguindo as instruções em [Atualizar módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md). Para obter mais informações sobre solução de problemas, consulte [solucionar problemas de módulos](shared-resources.md#modules).

1. Se o runbook for suspenso ou falhar inesperadamente:

    * [Renove o certificado](../manage-runas-account.md#cert-renewal) se a conta Executar como tiver expirado.
    * [Renove o webhook](../automation-webhooks.md#renew-a-webhook) se você estiver tentando usar um webhook expirado para iniciar o runbook.
    * [Verifique os status do trabalho](../automation-runbook-execution.md#job-statuses) para determinar os status atuais do runbook e algumas das possíveis causas do problema.
    * [Adicione uma saída adicional](../automation-runbook-output-and-messages.md#message-streams) ao runbook para identificar o que acontece antes de o runbook ser suspenso.
    * [Manipule quaisquer exceções](../automation-runbook-execution.md#exceptions) geradas pelo seu trabalho.

1. Execute esta etapa se o trabalho de runbook ou o ambiente em Hybrid Runbook Worker não responder.

    Se você estiver executando seus runbooks em um Hybrid Runbook Worker em vez de na automação do Azure, talvez seja necessário [solucionar o problema do próprio trabalhador híbrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Cenário: falha do runbook com um erro sem permissão ou proibido 403

### <a name="issue"></a>Problema

O runbook falhará com uma permissão não ou um erro de 403 Proibido, ou equivalente.

### <a name="cause"></a>Causa

As contas Executar como podem não ter as mesmas permissões em relação aos recursos do Azure como sua conta de automação atual. 

### <a name="resolution"></a>Resolução

Verifique se sua conta Executar como tem [permissões para acessar os recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usados em seu script.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: falha na entrada da conta do Azure

### <a name="issue"></a>Problema

Você receberá um dos seguintes erros ao trabalhar com o `Connect-AzAccount` cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Esses erros ocorrerão se o nome do ativo de credencial não for válido. Eles também podem ocorrer se o nome de usuário e a senha que você usou para configurar o ativo de credencial de automação não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga estas etapas:

1. Certifique-se de que você não tem nenhum caractere especial. Esses caracteres incluem a `\@` no nome do ativo da credencial de Automação que você está usando para se conectar ao Azure.
1. Verifique se você pode usar o nome de usuário e a senha que são armazenados na credencial de automação do Azure em seu editor de ISE do PowerShell local. Execute os seguintes cmdlets no ISE do PowerShell.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Se a autenticação falhar localmente, você não configurou suas credenciais do Azure Active Directory (AD do Azure) corretamente. Para que a conta do Azure AD seja configurada corretamente, consulte a postagem do blog [Autenticando no Azure usando Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).

1. Se o erro parece ser transitório, tente adicionar lógica de repetição à sua rotina de autenticação para tornar a autenticação mais robusta.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Cenário: execute login-AzureRMAccount para fazer logon

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao executar um runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você não está usando uma conta Executar como ou a conta Executar como expirou. Para obter mais informações, consulte [gerenciar contas Executar como da automação do Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Esse erro tem duas causas principais:

* Há versões diferentes do módulo AzureRM ou AZ.
* Você está tentando acessar recursos em uma assinatura separada.

### <a name="resolution"></a>Resolução

Se você receber esse erro depois de atualizar um módulo AzureRM ou AZ, atualize todos os seus módulos para a mesma versão.

Se você estiver tentando acessar recursos em outra assinatura, siga estas etapas para configurar permissões:

1. Vá para a conta Executar como de automação e copie a **ID do aplicativo** e a **impressão digital**.

    ![Copiar a ID do aplicativo e a impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)

1. Vá para o controle de **acesso** da assinatura em que a conta de automação *não* está hospedada e adicione uma nova atribuição de função.

    ![Controle de acesso](../media/troubleshoot-runbooks/access-control.png)

1. Adicione a **ID do aplicativo** coletada anteriormente. Selecione permissões de **colaborador** .

    ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Copie o nome da assinatura.

1. Agora você pode usar o seguinte código de runbook para testar as permissões de sua conta de automação para a outra assinatura. Substituir `"\<CertificateThumbprint\>"` pelo valor copiado na etapa 1. Substituir `"\<SubscriptionName\>"` pelo valor copiado na etapa 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a> Cenário: não é possível encontrar a assinatura do Azure

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com o `Select-AzureSubscription`cmdlet, `Select-AzureRMSubscription`ou: `Select-AzSubscription`

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Esse erro pode ocorrer se:

* O nome da assinatura não é válido.
* O usuário do Azure AD que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.
* O cmdlet não está disponível.

### <a name="resolution"></a>Resolução

Siga estas etapas para determinar se você autenticou para o Azure e tem acesso à assinatura que está tentando selecionar:

1. Para certificar-se de que seu script funciona autônomo, teste-o fora da automação do Azure.
1. Verifique se o script executa o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) antes de executar o `Select-*` cmdlet.
1. Adicione `Disable-AzContextAutosave –Scope Process` ao início do seu runbook. Esse cmdlet garante que todas as credenciais sejam aplicadas apenas à execução do runbook atual.
1. Se você ainda vir a mensagem de erro, modifique seu código adicionando o `AzContext` parâmetro para `Connect-AzAccount`e, em seguida, execute o código.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: os Runbooks falham ao lidar com várias assinaturas

### <a name="issue"></a>Problema

Ao executar runbooks, o runbook falha ao gerenciar recursos do Azure.

### <a name="cause"></a>Causa

O runbook não está usando o contexto correto durante a execução.

### <a name="resolution"></a>Resolução

O contexto de assinatura pode ser perdido quando um runbook invoca vários runbooks. Para garantir que o contexto de assinatura seja passado para os runbooks, faça com que o runbook do cliente passe `Start-AzureRmAutomationRunbook` o contexto para `AzureRmContext` o cmdlet no parâmetro. Use o `Disable-AzureRmContextAutosave` cmdlet com o `Scope` parâmetro definido como `Process` para garantir que as credenciais especificadas sejam usadas somente para o runbook atual. Para saber mais, confira [Assinaturas](../automation-runbook-execution.md#subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: falha na autenticação do Azure porque a autenticação multifator está habilitada

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com seu nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, não poderá usar um Azure Active Directory usuário para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para autenticar.

### <a name="resolution"></a>Resolução

Para usar um certificado com os cmdlets do modelo de implantação clássico do Azure, consulte [criando e adicionando um certificado para gerenciar os serviços do Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Para usar uma entidade de serviço com cmdlets Azure Resource Manager, consulte [criando uma entidade de serviço usando portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [Autenticando uma entidade de serviço com Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Cenário: o runbook falha com a mensagem de erro "uma tarefa foi cancelada"

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Esse erro pode ser causado por meio de módulos do Azure desatualizados.

### <a name="resolution"></a>Resolução

Você pode resolver esse erro atualizando seus módulos do Azure para a versão mais recente:

1. Na sua conta de automação, selecione **módulos**e, em seguida, selecione **Atualizar módulos do Azure**.
1. A atualização leva aproximadamente 15 minutos. Após a conclusão, execute novamente o runbook que falhou.

Para saber mais sobre como atualizar seus módulos, confira [Atualizar módulos do Azure na automação do Azure](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: termo não reconhecido como o nome de um cmdlet, uma função ou um script

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer pelos seguintes motivos:

* O módulo que contém o cmdlet não é importado para a conta de automação.
* O módulo que contém o cmdlet é importado, mas está desatualizado.

### <a name="resolution"></a>Resolução

Execute uma das seguintes tarefas para resolver esse erro:

* Para um módulo do Azure, consulte [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md) para saber como atualizar seus módulos em sua conta de automação.
* Para um módulo não Azure, certifique-se de que o módulo seja importado para sua conta de automação.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: falha de cmdlet no runbook do PowerShell PnP na automação do Azure

### <a name="issue"></a>Problema

Quando um runbook grava um objeto gerado por PnP PowerShell na saída de automação do Azure diretamente, a saída do cmdlet não pode transmitir de volta para a automação.

### <a name="cause"></a>Causa

Esse problema geralmente ocorre quando a automação do Azure processa runbooks que invocam cmdlets do PowerShell do PnP `add-pnplistitem`, por exemplo,, sem capturar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite seus scripts para atribuir quaisquer valores de retorno às variáveis para que os cmdlets não tentem gravar objetos inteiros na saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet, como mostrado aqui.

```azurecli
  $null = add-pnplistitem
```

Se o script analisar a saída do cmdlet, o script deverá armazenar a saída em uma variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando no seu runbook. É possível que o módulo que contém o cmdlet esteja ausente na conta, haja um conflito de nome com um nome de runbook ou o cmdlet também exista em outro módulo e a automação não possa resolver o nome.

### <a name="resolution"></a>Resolução

Use qualquer uma das soluções a seguir para corrigir o problema:

* Certifique-se de que você inseriu o nome do cmdlet corretamente.
* Verifique se o cmdlet existe em sua conta de automação e se não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e procure o cmdlet que você deseja localizar na biblioteca ou execute `Get-Command <CommandName>`. Depois de validar que o cmdlet está disponível para a conta e que não há nenhum conflito de nome com outros cmdlets ou runbooks, adicione o cmdlet à tela. Verifique se você está usando um conjunto de parâmetros válido em seu runbook.
* Se você tiver um conflito de nome e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema usando o nome totalmente qualificado para o cmdlet. Por exemplo, você pode usar `ModuleName\CmdletName`.
* Se você estiver executando o runbook local em um grupo de trabalho híbrido, verifique se o módulo e o cmdlet estão instalados no computador que hospeda o Hybrid Worker.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Cenário: referência de objeto incorreta na chamada para Add-AzAccount

### <a name="issue"></a>Problema

Você recebe esse erro quando trabalha com `Add-AzAccount`o, que é um alias para o `Connect-AzAccount` cmdlet:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer se o runbook não executar as etapas apropriadas antes de `Add-AzAccount` chamar para adicionar a conta de automação. Um exemplo de uma das etapas necessárias é entrar com uma conta Executar como. Para as operações corretas a serem usadas em seu runbook, consulte [execução de runbook na automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Cenário: referência de objeto não definida para uma instância de um objeto

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao invocar um runbook filho com o `Wait` parâmetro e o fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Se o fluxo contiver objetos `Start-AzAutomationRunbook` , o não tratará o fluxo de saída corretamente.

### <a name="resolution"></a>Resolução

Implemente uma lógica de sondagem e use o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) para recuperar a saída. Um exemplo dessa lógica é definido aqui:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Cenário: O Runbook falha devido a objeto desserializado

### <a name="issue"></a>Problema

O runbook falhar com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o seu runbook for um Fluxo de Trabalho do PowerShell, ele armazenará objetos complexos em um formato desserializado para manter seu estado de runbook se o fluxo de trabalho estiver suspenso.

### <a name="resolution"></a>Resolução

Use qualquer uma das soluções a seguir para corrigir esse problema:

* Se você estiver canalizando objetos complexos de um cmdlet para outro, empacote esses cmdlets em `InlineScript` uma atividade.
* Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
* Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.



## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: 400 status de solicitação inadequado ao chamar um webhook

### <a name="issue"></a>Problema

Ao tentar invocar um webhook para um runbook de automação do Azure, você receberá o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou. 

### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você poderá reabilitá-lo por meio do portal do Azure. Se o webhook tiver expirado, você deverá excluí-lo e recriá-lo novamente. Só é possível [renovar um webhook](../automation-webhooks.md#renew-a-webhook) se ele ainda não expirou. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: a taxa de solicitação é muito grande no momento

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao executar o cmdlet `Get-AzAutomationJobOutput`:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tem muitos [fluxos detalhados](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Siga um destes procedimentos para resolver esse erro:

* Edite o runbook e reduza o número de fluxos de trabalho que ele emite.
* Reduza o número de fluxos a ser recuperado ao executar o cmdlet. Para fazer isso, você pode definir o valor do `Stream` parâmetro para o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) para recuperar somente os fluxos de saída. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Cenário: o trabalho de runbook falha porque a cota alocada foi excedida

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. Essa cota aplica-se a todos os tipos de tarefa de execução de trabalho. Algumas dessas tarefas estão testando um trabalho, iniciando um trabalho no portal, executando um trabalho usando WebHooks ou agendando um trabalho para execução usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços da automação, confira [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, altere sua assinatura da camada gratuita para a camada básica:

1. Entre em sua assinatura do Azure.
1. Selecione a conta de automação a ser atualizada.
1. Selecione **configurações**e, em seguida, selecione **preços**.
1. Selecione **habilitar** na parte inferior da página para atualizar sua conta para a camada básica.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: a tentativa de trabalho de runbook foi tentada três vezes, mas não é iniciada a cada vez

### <a name="issue"></a>Problema

O runbook falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

* **Limite de memória.** Um trabalho poderá falhar se estiver usando mais de 400 MB de memória. Os limites documentados na memória alocada para uma área restrita são encontrados nos [limites do serviço de automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 
* **Soquetes de rede.** As áreas restritas do Azure são limitadas a 1.000 soquetes de rede simultâneos. Para obter mais informações, consulte [limites do serviço de automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).
* **Módulo incompatível.** As dependências de módulo podem não estar corretas. Nesse caso, o runbook normalmente retorna uma `Command not found` mensagem ou `Cannot bind parameter` .
* **Nenhuma autenticação com Active Directory para área restrita.** Seu runbook tentou chamar um executável ou subprocesso que é executado em uma área restrita do Azure. Não há suporte para a configuração de runbooks para autenticar com o Azure AD usando a ADAL (biblioteca de autenticação Azure Active Directory).
* **Muitos dados de exceção.** O runbook tentou gravar muitos dados de exceção no fluxo de saída.

### <a name="resolution"></a>Resolução

* **Limite de memória, soquetes de rede.** As maneiras sugeridas de trabalhar nos limites de memória são dividir a carga de trabalho entre vários runbooks, processar menos dados na memória, evitar a gravação de saída desnecessária de seus runbooks e considerar quantos pontos de verificação são gravados em seus runbooks de fluxo de trabalho do PowerShell. Use o método Clear, como `$myVar.clear`, para limpar variáveis e usar `[GC]::Collect` para executar a coleta de lixo imediatamente. Essas ações reduzem o volume de memória do seu runbook durante o runtime.
* **Módulo incompatível.** Atualize seus módulos do Azure seguindo as etapas em [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).
* **Nenhuma autenticação com Active Directory para área restrita.** Ao autenticar no Azure AD com um runbook, verifique se o módulo do AD do Azure está disponível na sua conta de automação. Certifique-se de conceder à conta Executar como as permissões necessárias para executar as tarefas que o runbook automatiza.

  Se o runbook não puder chamar um executável ou subprocesso em execução em uma área restrita do Azure, use o runbook em um [Hybrid runbook Worker](../automation-hrw-run-runbooks.md). Os Hybrid Workers não são limitados pelos limites de memória e de rede que as áreas restritas do Azure têm.

* **Muitos dados de exceção.** Há um limite de 1 MB no fluxo de saída do trabalho. Verifique se o runbook inclui chamadas para um executável ou subprocesso usando `try` blocos and. `catch` Se as operações lançarem uma exceção, faça com que o código grave a mensagem da exceção em uma variável de automação. Essa técnica impede que a mensagem seja gravada no fluxo de saída do trabalho.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Cenário: o trabalho do PowerShell falha com a mensagem de erro "não é possível invocar o método"

### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao iniciar um trabalho do PowerShell em um runbook que é executado no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Esse erro pode indicar que os runbooks executados em uma área restrita do Azure não podem ser executados no [modo de linguagem completa](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há três maneiras de resolver esse erro:

* Em vez de usar [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7), use [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) para iniciar o runbook.
* Tente executar o runbook em um Hybrid Runbook Worker.

Para saber mais sobre esse comportamento e outros comportamentos dos runbooks de automação do Azure, confira [execução de runbook na automação do Azure](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: falha na conclusão de um runbook de execução longa

### <a name="issue"></a>Problema

O runbook é exibido em um estado parado após a execução por três horas. Você também pode receber esse erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Esse comportamento é por design em áreas restritas do Azure por causa do monitoramento do [Fair share](../automation-runbook-execution.md#fair-share) de processos na automação do Azure. Se um processo for executado por mais de três horas, Fair share interromperá automaticamente um runbook. O status de um runbook que ultrapassa o limite de tempo de compartilhamento justo difere por tipo de runbook. Os runbooks do PowerShell e do Python são definidos para um status Parado. Os runbooks do fluxo de trabalho do PowerShell estão definidos como falha.

### <a name="cause"></a>Causa

O runbook foi executado sobre o limite de três horas permitido pelo Fair share em uma área restrita do Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Os Hybrid Workers não são limitados pelo limite de runbook de compartilhamento justo de três horas que as áreas restritas do Azure têm. Os Runbooks executados em Hybrid runbook Workers devem ser desenvolvidos para dar suporte a comportamentos de reinicialização se houver problemas de infraestrutura local inesperados.

Outra solução é otimizar o runbook criando [runbooks filho](../automation-child-runbooks.md). Se o seu runbook faz o loop pela mesma função em vários recursos, por exemplo, em uma operação de banco de dados em vários bancos, você pode mover a função para um runbook filho. Cada runbook filho é executado em paralelo em um processo separado. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir.

Os cmdlets do PowerShell que habilitam o cenário do runbook filho são:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Esse cmdlet permite que você inicie um runbook e passe parâmetros para o runbook.
* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Se houver operações que precisam ser executadas após a conclusão do runbook filho, esse cmdlet permitirá que você verifique o status do trabalho para cada filho.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: erro em fluxos de trabalho sobre o método de get_SerializationSettings

### <a name="issue"></a>Problema

Você verá o seguinte erro em seus fluxos de trabalho para um runbook:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Causa

Esse erro é provavelmente causado pelo uso de uma migração incompleta do AzureRM para módulos AZ em seu runbook. Essa situação pode fazer com que a automação do Azure inicie um trabalho de runbook usando apenas módulos AzureRM e, em seguida, inicie outro trabalho usando apenas módulos AZ, que leva a uma falha de área restrita.

### <a name="resolution"></a>Resolução

Não recomendamos o uso de cmdlets AZ e AzureRM no mesmo runbook. Para saber mais sobre o uso correto desses módulos, consulte [migrando para módulos AZ](../shared-resources/modules.md#migrating-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Cenário: acesso negado ao usar a área restrita do Azure para runbook ou aplicativo

### <a name="issue"></a>Problema

Quando o runbook ou aplicativo tenta ser executado em uma área restrita do Azure, o ambiente nega acesso.

### <a name="cause"></a>Causa

Esse problema pode ocorrer porque as áreas restritas do Azure impedem o acesso a todos os servidores COM fora do processo. Por exemplo, um aplicativo ou runbook em área restrita não pode chamar em Instrumentação de Gerenciamento do Windows (WMI) ou no serviço Windows Installer (MSIServer. exe). 

### <a name="resolution"></a>Resolução

Para obter detalhes sobre o uso de áreas restritas do Azure, consulte [ambiente de execução de runbook](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Cenário: código de status proibido inválido ao usar Key Vault dentro de um runbook

### <a name="issue"></a>Problema

Ao tentar acessar Azure Key Vault por meio de um runbook de automação do Azure, você obtém o seguinte erro:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Causa

As possíveis causas desse problema são:

* Não está usando uma conta Executar como.
* Permissões insuficientes

### <a name="resolution"></a>Resolução

#### <a name="not-using-a-run-as-account"></a>Não usar uma conta Executar como

Siga a [etapa 5-adicionar autenticação para gerenciar recursos do Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) para garantir que você está usando uma conta Executar como para acessar Key Vault.

#### <a name="insufficient-permissions"></a>Permissões insuficientes

[Adicione permissões a Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) para garantir que sua conta Executar como tenha permissões suficientes para acessar o Key Vault.

## <a name="recommended-documents"></a>Documentos recomendados

* [Execução de runbook na Automação do Azure](../automation-runbook-execution.md)
* [Como iniciar um Runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>Próximas etapas

Se você não tiver visto seu problema aqui ou se não conseguir resolver o problema, tente um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente. O suporte do Azure conecta você à comunidade do Azure para obter respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**.
