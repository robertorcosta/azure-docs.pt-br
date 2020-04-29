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
ms.openlocfilehash: ef3f50e79f4458845ec2e3df73e1e87766bfd0f4
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508778"
---
# <a name="troubleshoot-runbook-errors"></a>Solucionar erros de runbook

 Este artigo descreve os vários erros de runbook que podem ocorrer e como resolvê-los.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Diagnosticando problemas de runbook

Ao receber erros durante a execução do runbook na automação do Azure, você pode usar as etapas a seguir para ajudar a diagnosticar os problemas.

1. **Verifique se o script de runbook é executado com êxito no computador local.** 

    Consulte a documentação do [PowerShell](/powershell/scripting/overview) ou os [documentos do Python](https://docs.python.org/3/) para referência de linguagem e módulos de aprendizado. Executar o script localmente pode descobrir e resolver erros comuns, como:

      * Módulos ausentes
      * Erros de sintaxe
      * Erros lógicos

2. **Investigue os [fluxos de erro](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)do runbook.**

    Examine esses fluxos de mensagens específicas e compare-as com os erros documentados neste artigo.

3. **Verifique se os nós e o espaço de trabalho de automação têm os módulos necessários.** 

    Se o runbook importar módulos, verifique se eles estão disponíveis para sua conta de automação usando as etapas listadas em [Importar módulos](../shared-resources/modules.md#importing-modules). Atualize seus módulos para a versão mais recente seguindo as instruções em [Atualizar módulos do Azure na automação do Azure](..//automation-update-azure-modules.md). Para obter mais informações sobre solução de problemas, consulte [solucionar problemas de módulos](shared-resources.md#modules).

4. **Se o runbook for suspenso ou falhar inesperadamente.**

    * [Verifique](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) se os status do trabalho definem os status do runbook e algumas causas possíveis.
    * [Adicione uma saída adicional](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) ao runbook para identificar o que acontece antes de o runbook ser suspenso.
    * [Manipule quaisquer exceções](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) geradas pelo seu trabalho.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Cenário: execute login-AzureRMAccount para fazer logon

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao executar um runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você não está usando uma conta Executar como ou a conta Executar como expirou. Consulte [gerenciar contas Executar como da automação do Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Esse erro tem duas causas principais:

* Há versões diferentes do módulo AzureRM ou AZ.
* Você está tentando acessar recursos em uma assinatura separada.

### <a name="resolution"></a>Resolução

Se você receber esse erro depois de atualizar um módulo AzureRM ou AZ, você deverá atualizar todos os seus módulos para a mesma versão.

Se você estiver tentando acessar recursos em outra assinatura, poderá seguir as etapas abaixo para configurar permissões.

1. Vá para a conta Executar como de automação e copie a ID do aplicativo e a impressão digital.

    ![ID da cópia e impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)

1. Vá para o controle de acesso da assinatura em que a conta de automação não está hospedada e adicione uma nova atribuição de função.

    ![Controle de acesso](../media/troubleshoot-runbooks/access-control.png)

1. Adicione a ID do aplicativo coletada anteriormente. Selecione permissões de colaborador.

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

Você recebe o seguinte erro ao trabalhar com o `Select-AzureSubscription`cmdlet `Select-AzureRMSubscription`, ou `Select-AzSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro do

Esse erro pode ocorrer se:

* O nome da assinatura não é válido.
* O usuário Azure Active Directory que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.
* O cmdlet não está disponível.

### <a name="resolution"></a>Resolução

Siga as etapas abaixo para determinar se você autenticou para o Azure e tem acesso à assinatura que você está tentando selecionar.

1. Para certificar-se de que seu script funciona autônomo, teste-o fora da automação do Azure.
2. Verifique se o script executa o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) antes de executar o `Select-*` cmdlet.
3. Adicione `Disable-AzContextAutosave –Scope Process` ao início do seu runbook. Essa chamada de cmdlet garante que todas as credenciais se apliquem somente à execução do runbook atual.
4. Se você ainda vir essa mensagem de erro, modifique seu código adicionando o `AzContext` parâmetro para `Connect-AzAccount`e, em seguida, execute o código.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: Autenticação do Azure falhou porque a autenticação multifator é habilitada

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com o nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, você não pode usar um usuário do Active Directory do Azure para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para autenticar.

### <a name="resolution"></a>Resolução

Para usar um certificado com os cmdlets do modelo de implantação clássico do Azure, consulte [criando e adicionando um certificado para gerenciar os serviços do Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Para usar uma entidade de serviço com cmdlets Azure Resource Manager, consulte [criando uma entidade de serviço usando portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [Autenticando uma entidade de serviço com Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: você vê um erro em seus fluxos de trabalho sobre o método de get_SerializationSettings

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

Esse erro é provavelmente causado pelo uso de uma migração incompleta do AzureRM para módulos AZ em seu runbook. Isso pode fazer com que a automação do Azure inicie um trabalho de runbook usando apenas módulos AzureRM e, em seguida, inicie outro trabalho usando apenas módulos AZ, levando a uma falha de área restrita. 

### <a name="resolution"></a>Resolução

Não recomendamos o uso de cmdlets AZ e AzureRM no mesmo runbook. Para saber mais sobre o uso correto desses módulos, consulte [migrando para módulos AZ](../shared-resources/modules.md#migrating-to-az-modules).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Cenário: O runbook falha com o erro: uma tarefa foi cancelada

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Esse erro pode ser causado por meio de módulos do Azure desatualizados.

### <a name="resolution"></a>Resolução

Você pode resolver esse erro atualizando seus módulos do Azure para a versão mais recente. 

1. Em sua conta de automação, clique em **módulos**e **atualize os módulos do Azure**. 
2. A atualização leva aproximadamente 15 minutos. Após a conclusão, execute novamente o runbook que falhou.

Para saber mais sobre como atualizar seus módulos, confira [Atualizar módulos do Azure na automação do Azure](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: os Runbooks falham ao lidar com várias assinaturas

### <a name="issue"></a>Problema

Ao executar runbooks, o runbook falha ao gerenciar recursos do Azure.

### <a name="cause"></a>Causa

O runbook não está usando o contexto correto durante a execução.

### <a name="resolution"></a>Resolução

O contexto de assinatura pode ser perdido quando um runbook invoca vários runbooks. Para garantir que o contexto de assinatura seja passado para os runbooks, faça com que o runbook do cliente passe `Start-AzureRmAutomationRunbook` o contexto para `AzureRmContext` o cmdlet no parâmetro. Use o `Disable-AzureRmContextAutosave` cmdlet com o `Scope` parâmetro definido como `Process` para garantir que as credenciais especificadas sejam usadas somente para o runbook atual. Para obter mais informações, consulte [trabalhando com várias assinaturas](../automation-runbook-execution.md#working-with-multiple-subscriptions).

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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: termo não reconhecido como o nome de um cmdlet, função, script

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

Execute uma das tarefas a seguir para resolver esse erro. 

* Para um módulo do Azure, consulte [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md) para saber como atualizar seus módulos em sua conta de automação.

* Para um módulo não Azure, certifique-se de que o módulo em importado para sua conta de automação.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: o início do trabalho de runbook foi tentado três vezes, mas falhou ao iniciar a cada vez

### <a name="issue"></a>Problema

O runbook falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

* Limite de Memória. Um trabalho poderá falhar se estiver usando mais de 400 MB de memória. Os limites documentados na memória alocada para uma área restrita são encontrados nos [limites do serviço de automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Soquetes de rede. As áreas restritas do Azure são limitadas a 1000 soquetes de rede simultâneos. Consulte [limites do serviço de automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Módulo Incompatível. As dependências de módulo podem não estar corretas. Nesse caso, o runbook normalmente retorna uma `Command not found` mensagem ou `Cannot bind parameter` .

* Nenhuma autenticação com Active Directory para área restrita. Seu runbook tentou chamar um executável ou subprocesso que é executado em uma área restrita do Azure. Não há suporte para a configuração de runbooks para autenticar com o Azure AD usando a ADAL (biblioteca de autenticação Azure Active Directory).

* Muitos dados de exceção. O runbook tentou gravar muitos dados de exceção no fluxo de saída.

### <a name="resolution"></a>Resolução

* Limite de memória, soquetes de rede. As maneiras sugeridas de trabalhar nos limites de memória são dividir a carga de trabalho entre vários runbooks, processar menos dados na memória, evitar a gravação de saída desnecessária de seus runbooks e considerar quantos pontos de verificação são gravados em seus runbooks de fluxo de trabalho do PowerShell. Use o método Clear, como `$myVar.clear`, para limpar variáveis e usar `[GC]::Collect` para executar a coleta de lixo imediatamente. Essas ações reduzem o volume de memória do seu runbook durante o runtime.

* Módulo Incompatível. Atualize seus módulos do Azure seguindo as etapas em [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

* Nenhuma autenticação com Active Directory para área restrita. Ao autenticar no Azure AD com um runbook, verifique se o módulo do Azure AD está disponível na sua conta de automação. Certifique-se de conceder à conta Executar como as permissões necessárias para executar as tarefas que o runbook automatiza.

  Se o runbook não puder chamar um executável ou subprocesso em execução em uma área restrita do Azure, use o runbook em um [Hybrid runbook Worker](../automation-hrw-run-runbooks.md). Os Hybrid Workers não são limitados pelos limites de memória e de rede que as áreas restritas do Azure têm.

* Muitos dados de exceção. Há um limite de 1MB no fluxo de saída do trabalho. Verifique se o runbook inclui chamadas para um executável ou subprocesso usando `try` e `catch` bloqueia. Se as operações lançarem uma exceção, faça com que o código grave a mensagem da exceção em uma variável de automação. Essa técnica impede que a mensagem seja gravada no fluxo de saída do trabalho.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: falha na entrada da conta do Azure

### <a name="issue"></a>Problema

Você recebe um dos seguintes erros ao trabalhar com o `Connect-AzAccount` cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Esses erros ocorrerão se o nome do ativo de credencial não for válido. Eles também podem ocorrer se o nome de usuário e a senha que você usou para configurar o ativo de credencial de automação não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga os seguintes passos:

1. Certifique-se de que você não tem nenhum caractere especial. Esses caracteres incluem a `\@` no nome do ativo da credencial de Automação que você está usando para se conectar ao Azure.
2. Verifique se você pode usar o nome de usuário e a senha que são armazenados na credencial de automação do Azure em seu editor de ISE do PowerShell local. Execute os seguintes cmdlets no ISE do PowerShell.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Se a autenticação falhar localmente, você não configurou suas credenciais de Azure Active Directory corretamente. Consulte a postagem de blog [autenticação no Azure usando Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) para obter a conta de Azure Active Directory configurada corretamente.

4. Se o erro parece ser transitório, tente adicionar lógica de repetição à sua rotina de autenticação para tornar a autenticação mais robusta.

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

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Cenário: referência de objeto incorreta na chamada para Add-AzAccount

### <a name="issue"></a>Problema

Você recebe esse erro ao trabalhar com `Add-AzAccount`, que é um alias para o `Connect-AzAccount` cmdlet:

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

`Start-AzAutomationRunbook`não tratará o fluxo de saída corretamente se o fluxo contiver objetos.

### <a name="resolution"></a>Resolução

É recomendável implementar uma lógica de sondagem e usar o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) para recuperar a saída. Um exemplo dessa lógica é definido abaixo.

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

Use qualquer uma das soluções a seguir para corrigir esse problema.

* Se você estiver canalizando objetos complexos de um cmdlet para outro, empacote esses cmdlets em `InlineScript` uma atividade.
* Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
* Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Cenário: falha no trabalho de runbook porque a cota alocada foi excedida

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. Essa cota aplica-se a todos os tipos de tarefa de execução de trabalho. Algumas dessas tarefas estão testando um trabalho, iniciando um trabalho no portal, executando um trabalho usando WebHooks ou agendando um trabalho para execução usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços da automação, confira [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, altere sua assinatura da camada gratuita para a camada básica.

1. Entre em sua assinatura do Azure.
2. Selecione a conta de automação a ser atualizada.
3. Clique em **configurações**e em **preços**.
4. Clique em **habilitar** na parte inferior da página para atualizar sua conta para a camada básica.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando no seu runbook. É possível que o módulo que contém o cmdlet esteja ausente na conta, haja um conflito de nome com um nome de runbook ou o cmdlet também exista em outro módulo e a automação não possa resolver o nome.

### <a name="resolution"></a>Resolução

Use qualquer uma das soluções a seguir para corrigir o problema.

* Certifique-se de que você inseriu o nome do cmdlet corretamente.
* Verifique se o cmdlet existe em sua conta de automação e se não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e procure o cmdlet que você deseja localizar na biblioteca ou execute `Get-Command <CommandName>`. Depois de validar que o cmdlet está disponível para a conta e que não há nenhum conflito de nome com outros cmdlets ou runbooks, adicione o cmdlet à tela e verifique se você está usando um conjunto de parâmetros válido em seu runbook.
* Se você tiver um conflito de nome e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema usando o nome totalmente qualificado para o cmdlet. Por exemplo, você pode usar `ModuleName\CmdletName`.
* Se você estiver executando o runbook local em um grupo de trabalho híbrido, verifique se o módulo e o cmdlet estão instalados no computador que hospeda o Hybrid Worker.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: falha na conclusão de um runbook de execução longa

### <a name="issue"></a>Problema

Seu runbook é exibido em um estado Parado após a execução por 3 horas. Você também pode receber esse erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Esse comportamento é por design em áreas restritas do Azure por causa do monitoramento do [Fair share](../automation-runbook-execution.md#fair-share) de processos na automação do Azure. Se um processo for executado por mais de três horas, Fair share interromperá automaticamente um runbook. O status de um runbook que ultrapassa o limite de tempo de compartilhamento justo difere por tipo de runbook. Os runbooks do PowerShell e do Python são definidos para um status Parado. Os runbooks do fluxo de trabalho do PowerShell estão definidos como falha.

### <a name="cause"></a>Causa

O runbook foi executado no limite de 3 horas permitido pelo Fair share em uma área restrita do Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Os Hybrid Workers não são limitados pelo limite de runbook de compartilhamento justo de 3 horas que as áreas restritas do Azure têm. Os Runbooks executados em Hybrid runbook Workers devem ser desenvolvidos para dar suporte a comportamentos de reinicialização se houver problemas de infraestrutura local inesperados.

Outra solução é otimizar o runbook criando [runbooks filho](../automation-child-runbooks.md). Se o seu runbook faz o loop pela mesma função em vários recursos, por exemplo, em uma operação de banco de dados em vários bancos, você pode mover a função para um runbook filho. Cada runbook filho é executado em paralelo em um processo separado. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir.

Os cmdlets do PowerShell que habilitam o cenário do runbook filho são:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Esse cmdlet permite que você inicie um runbook e passe parâmetros para o runbook.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Se houver operações que precisam ser executadas após a conclusão do runbook filho, esse cmdlet permitirá que você verifique o status do trabalho para cada filho.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: status: 400 solicitação inadequada ao chamar um webhook

### <a name="issue"></a>Problema

Ao tentar invocar um webhook para um runbook de automação do Azure, você receberá o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou.

### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você pode habilitar novamente o webhook por meio do portal do Azure. Se o webhook tiver expirado, você deverá excluí-lo e recriá-lo. Só é possível [renovar um webhook](../automation-webhooks.md#renew-webhook) se ele ainda não expirou.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: a taxa de solicitação atualmente é muito grande...

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao executar o cmdlet `Get-AzAutomationJobOutput`:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tem muitos [fluxos detalhados](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Siga um destes procedimentos para resolver esse erro.

* Edite o runbook e reduza o número de fluxos de trabalho que ele emite.

* Reduza o número de fluxos a ser recuperado ao executar o cmdlet. Para fazer isso, você pode definir o valor do `Stream` parâmetro para o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) para recuperar somente os fluxos de saída. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Cenário: falha no trabalho do PowerShell com o erro: não é possível invocar o método

### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao iniciar um trabalho do PowerShell em um runbook em execução no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Esse erro pode indicar que os runbooks em execução em uma área restrita do Azure não podem ser executados no [modo de linguagem completa](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há duas maneiras de resolver esse erro.

* Em vez de usar [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7), use [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) para iniciar o runbook.
* Tente executar o runbook em um Hybrid Runbook Worker.

Para saber mais sobre esse comportamento e outros comportamentos dos runbooks de automação do Azure, confira [execução de runbook na automação do Azure](../automation-runbook-execution.md).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Cenário: o Linux Hybrid Runbook Worker recebe um prompt para uma senha ao assinar um runbook

### <a name="issue"></a>Problema

A execução `sudo` do comando para um Hybrid runbook Worker Linux recupera uma solicitação inesperada para uma senha.

### <a name="cause"></a>Causa

A conta **nxautomationuser** para o agente de log Analytics para Linux não está configurada corretamente no arquivo **sudoers** . O Hybrid Runbook Worker precisa da configuração apropriada de permissões de conta e outros dados para que ele possa assinar runbooks no runbook Worker do Linux.

### <a name="resolution"></a>Resolução

* Verifique se o Hybrid Runbook Worker tem o executável GnuPG (GPG) no computador.

* Verifique a configuração da conta **nxautomationuser** no arquivo **sudoers** . Consulte [executando runbooks em um Hybrid runbook Worker](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: falha de cmdlet no runbook do PowerShell PnP na automação do Azure

### <a name="issue"></a>Problema

Quando um runbook grava um objeto gerado por PnP PowerShell na saída de automação do Azure diretamente, a saída do cmdlet não pode transmitir de volta para a automação.

### <a name="cause"></a>Causa

Esse problema geralmente ocorre quando a automação do Azure processa runbooks que invocam cmdlets do PowerShell do PnP `add-pnplistitem`, por exemplo,, sem capturar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite seus scripts para atribuir quaisquer valores de retorno a variáveis para que os cmdlets não tentem gravar objetos inteiros na saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet, conforme mostrado abaixo.

```azurecli
  $null = add-pnplistitem
```

Se o script analisar a saída do cmdlet, o script deverá armazenar a saída em uma variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Cenário: código de status inválido "proibido" ao usar Key Vault dentro de um runbook

### <a name="issue"></a>Problema

Ao tentar acessar Key Vault por meio de um runbook de automação do Azure, você obtém o seguinte erro:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Causa

Possíveis causas para esse problema:

* Não está usando uma conta Executar como.
* Permissões insuficientes

### <a name="resolution"></a>Resolução

#### <a name="not-using-run-as-account"></a>Não usar conta Executar como

Siga a [etapa 5-adicionar autenticação para gerenciar recursos do Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) para garantir que você está usando uma conta Executar como para acessar Key Vault. 

#### <a name="insufficient-permissions"></a>Permissões insuficientes

[Adicione permissões a Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) para garantir que sua conta Executar como tenha permissões suficientes para acessar o Key Vault. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Meu problema não está listado acima

As seções a seguir listam outros erros comuns e fornecem documentação de suporte para ajudá-lo a resolver o problema.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>O Hybrid Runbook Worker não executa trabalhos ou não está respondendo

Se você estiver executando trabalhos em um Hybrid Runbook Worker em vez de na automação do Azure, talvez seja necessário [solucionar o problema do próprio trabalhador híbrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Falha no runbook sem permissão ou alguma variação

As contas Executar como podem não ter as mesmas permissões em relação aos recursos do Azure como sua conta atual. Verifique se sua conta Executar como tem [permissões para acessar os recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usados em seu script.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemas ao passar parâmetros para WebHooks

Para obter ajuda com a passagem de parâmetros em WebHooks, consulte [Iniciar um runbook de um webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemas usando módulos AZ

Usar uma migração incompleta de seus módulos de runbook de AzureRM para AZ pode causar falhas de área restrita e runbook. Consulte [usando módulos em seus runbooks](../automation-runbook-execution.md#using-modules-in-your-runbooks).

### <a name="inconsistent-behavior-in-runbooks"></a>Comportamento inconsistente em runbooks

Siga as orientações em [execução de runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) para evitar problemas com trabalhos simultâneos, recursos sendo criados várias vezes ou outra lógica sensível a intervalos em runbooks.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Falha no runbook com o erro sem permissão, proibido (403) ou alguma variação

As contas Executar como podem não ter as mesmas permissões em relação aos recursos do Azure como sua conta atual. Verifique se sua conta Executar como tem [permissões para acessar os recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usados em seu script.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam funcionando, mas de repente pararam

* Verifique se a conta Executar como não expirou. Consulte [renovação de certificação](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se você estiver usando um [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) para iniciar um runbook, verifique se o webhook não expirou.

### <a name="passing-parameters-into-webhooks"></a>Passando parâmetros a webhooks

Para obter ajuda com a passagem de parâmetros em WebHooks, consulte [Iniciar um runbook de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-self-signed-certificates"></a>Usando certificados autoassinados

Para usar certificados autoassinados, consulte [criando um novo certificado](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Acesso negado ao usar a área restrita do Azure para um runbook

A área restrita do Azure impede o acesso a todos os servidores COM fora do processo. Por exemplo, um aplicativo ou runbook em área restrita não pode chamar em Instrumentação de Gerenciamento do Windows (WMI) ou no serviço Windows Installer (MSIServer. exe). Para obter detalhes sobre o uso da área restrita, consulte [execução de runbook na automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Documentos recomendados

* [Execução de runbook na Automação do Azure](../automation-runbook-execution.md)
* [Como iniciar um Runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Execução de runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se ao – a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
