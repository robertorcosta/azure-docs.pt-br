---
title: Solucionar erros com os livros de execução de automação do Azure
description: Aprenda a solucionar e resolver problemas que você pode encontrar com os runbooks da Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b5d326d02587d6b5bd8fd73dcccfefdb13c47d57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500925"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Solucionar problemas de erros com runbooks

Quando você tem erros na execução de runbooks no Azure Automation, você pode usar as seguintes etapas para ajudar a diagnosticar os problemas.

1. **Certifique-se de que seu script de runbook seja executado com sucesso em sua máquina local.** 

    Consulte o [PowerShell Docs](/powershell/scripting/overview) ou [O Docs Python](https://docs.python.org/3/) para obter módulos de referência e aprendizagem de idiomas. A execução do seu script localmente pode descobrir e resolver erros comuns, como:

      * Módulos ausentes
      * Erros de sintaxe
      * Erros de lógica

2. **Investigue [fluxos de erro](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)do runbook .**

    Veja esses fluxos para mensagens específicas e compare-os com os erros documentados neste artigo.

3. **Certifique-se de que seus nódulos e espaço de trabalho de automação tenham os módulos necessários.** 

    Se o seu runbook importar quaisquer módulos, verifique se eles estão disponíveis para sua conta de Automação usando as etapas listadas nos [módulos de importação](../shared-resources/modules.md#import-modules). Atualize seus módulos para a versão mais recente seguindo as instruções nos [módulos Update Azure na Azure Automation](..//automation-update-azure-modules.md). Para obter mais informações sobre solução de [problemas, consulte módulos de solução de problemas](shared-resources.md#modules).

4. **Faça se o seu manual estiver suspenso ou falhar inesperadamente.**

    * [Verificar os status do trabalho](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) define os status do runbook e algumas possíveis causas.
    * [Adicione saída adicional](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) ao livro de execução para identificar o que acontece antes que o livro de execução seja suspenso.
    * [Cuide de todas as exceções](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) que forem jogadas pelo seu trabalho.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Cenário: Execute login-AzureRMAccount para fazer login

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao executar um runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você não está usando uma conta Run As ou a conta Run As expirou. Consulte [Gerenciar a execução de automação do Azure como contas](https://docs.microsoft.com/azure/automation/manage-runas-account).

Este erro tem duas causas primárias:

* Existem diferentes versões do módulo AzureRM ou Az.
* Você está tentando acessar recursos em uma assinatura separada.

### <a name="resolution"></a>Resolução

Se você receber esse erro depois de atualizar um módulo AzureRM ou Az, você deve atualizar todos os seus módulos para a mesma versão.

Se você estiver tentando acessar recursos em outra assinatura, você pode seguir as etapas abaixo para configurar permissões.

1. Vá para a conta 'Executar a automação como' e copie o ID do aplicativo e a impressão digital.
  ![Copiar id do aplicativo e impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)
1. Vá para o Controle de Acesso da assinatura onde a conta de automação NÃO está hospedada e adicione uma nova atribuição de função.
  ![Controle de acesso](../media/troubleshoot-runbooks/access-control.png)
1. Adicione o ID do aplicativo coletado anteriormente. Selecione permissões de contribuinte.
   ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Copie o nome da assinatura.
1. Agora você pode usar o seguinte código de runbook para testar as permissões de sua conta de Automação para a outra assinatura. Substitua pelo `"\<CertificateThumbprint\>"` valor copiado na etapa 1. Substitua pelo `"\<SubscriptionName\>"` valor copiado na etapa 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a> Cenário: não é possível encontrar a assinatura do Azure

### <a name="issue"></a>Problema

Você recebe o seguinte erro `Select-AzureSubscription` `Select-AzureRmSubscription` ao trabalhar com o cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Esse erro pode ocorrer se:

* O nome da assinatura não é válido.
* O usuário do Azure Active Directory que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.

### <a name="resolution"></a>Resolução

Siga as etapas abaixo para determinar se você foi autenticado no Azure e tem acesso à assinatura que você está tentando selecionar.

1. Para garantir que seu script funcione autônomo, teste-o fora da Azure Automation.
2. Certifique-se de que `Add-AzureAccount` o seu script `Select-AzureSubscription` execute o cmdlet antes de executar o cmdlet.
3. Adicione `Disable-AzureRmContextAutosave –Scope Process` ao início do seu runbook. Esta chamada cmdlet garante que quaisquer credenciais se apliquem apenas à execução do manual atual.
4. Se você ainda vir esta mensagem de `AzureRmContext` erro, `Add-AzureAccount` modifique seu código adicionando o parâmetro para o cmdlet e execute o código.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: Autenticação do Azure falhou porque a autenticação multifator é habilitada

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com o nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, você não pode usar um usuário do Active Directory do Azure para autenticar no Azure. Em vez disso, você precisa usar um certificado ou um diretor de serviço para autenticar.

### <a name="resolution"></a>Resolução

Para usar um certificado com cmdlets do modelo clássico de implantação do Azure, consulte [Criar e adicionar um certificado para gerenciar os serviços do Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Para usar um diretor de serviço com cmdlets do Azure Resource Manager, consulte [Criando o principal de serviço usando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [autenticando um diretor de serviço com o Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: Você vê um erro em seus fluxos de trabalho sobre o método get_SerializationSettings

### <a name="issue"></a>Problema

Você vê o seguinte erro em seus fluxos de trabalho para um runbook:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Causa

Esse erro é causado pelo uso de cmdlets de módulo AzureRM e Az em um runbook. Ocorre quando você importa o módulo Az antes de importar o módulo AzureRM.

### <a name="resolution"></a>Resolução

Os cmdlets Az e AzureRM não podem ser importados e usados no mesmo runbook. Para saber mais sobre cmdlets Az no Azure Automation, consulte [o suporte ao módulo Az no Azure Automation](../az-modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Cenário: O runbook falha com o erro: uma tarefa foi cancelada

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Esse erro pode ser causado por meio de módulos do Azure desatualizados.

### <a name="resolution"></a>Resolução

Você pode resolver esse erro atualizando seus módulos Azure para a versão mais recente. 

1. Em sua conta de automação, clique **em Módulos**e **atualize módulos do Azure.** 
2. A atualização leva cerca de 15 minutos. Uma vez concluído, execute novamente o manual que falhou.

Para saber mais sobre como atualizar seus módulos, consulte [Atualizar módulos Azure no Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: Runbooks falham ao lidar com várias assinaturas

### <a name="issue"></a>Problema

Ao executar runbooks, o runbook falha em gerenciar os recursos do Azure.

### <a name="cause"></a>Causa

O runbook não está usando o contexto correto durante a execução.

### <a name="resolution"></a>Resolução

O contexto de assinatura pode ser perdido quando um runbook invoca vários runbooks. Para garantir que o contexto de assinatura seja passado para os runbooks, tenha que o manual do cliente passe o contexto para o `Start-AzureRmAutomationRunbook` cmdlet no `AzureRmContext` parâmetro. Use `Disable-AzureRmContextAutosave` o cmdlet `Scope` com o `Process` parâmetro definido para garantir que as credenciais especificadas sejam usadas apenas para o runbook atual. Para obter mais informações, consulte [Trabalhando com várias assinaturas](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: Termo não reconhecido como o nome de um cmdlet, função, script

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Esse erro pode acontecer pelas seguintes razões:

* O módulo contendo o cmdlet não é importado para a conta automação.
* O módulo contendo o cmdlet é importado, mas está desatualizado.

### <a name="resolution"></a>Resolução

Faça uma das seguintes tarefas para resolver esse erro. 

* Para obter um módulo Azure, consulte [Como atualizar os módulos Do Azure PowerShell na Azure Automation](../automation-update-azure-modules.md) para saber como atualizar seus módulos em sua conta de Automação.

* Para um módulo não-Azure, certifique-se de que o módulo seja importado para sua conta de Automação.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: O início do trabalho de runbook foi tentado três vezes, mas não conseguiu começar cada vez

### <a name="issue"></a>Problema

Seu runbook falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

* Limite de Memória. Um trabalho pode falhar se estiver usando mais de 400 MB de memória. Os limites documentados de memória alocados a uma caixa de areia são encontrados nos [limites de serviço de Automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Soquetes de rede. As caixas de areia do Azure são limitadas a 1000 soquetes de rede simultâneos. Consulte [os limites de serviço de automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Módulo Incompatível. As dependências do módulo podem não estar corretas. Neste caso, seu runbook normalmente retorna uma `Command not found` ou `Cannot bind parameter` mensagem.

* Sem autenticação com diretório ativo para caixa de areia. Seu runbook tentou chamar um executável ou subprocesso que é executado em uma caixa de areia Azure. A configuração de runbooks para autenticar com o Azure AD usando a ADAL (Active Directory Authentication Library, biblioteca de autenticação de diretório ativo do Azure) não é suportada.

* Dados de exceção demais. Seu runbook tentou escrever muitos dados de exceção para o fluxo de saída.

### <a name="resolution"></a>Resolução

* Limite de memória, soquetes de rede. As maneiras sugeridas de trabalhar dentro dos limites de memória são dividir a carga de trabalho entre vários runbooks, processar menos dados na memória, evitar escrever saídas desnecessárias de seus runbooks e considerar quantos pontos de verificação estão escritos no seu fluxo de trabalho PowerShell runbooks. Use o método claro, como, `$myVar.clear`para limpar `[GC]::Collect` variáveis e usar para executar a coleta de lixo imediatamente. Essas ações reduzem o volume de memória do seu runbook durante o runtime.

* Módulo Incompatível. Atualize seus módulos Do Azure seguindo as etapas de [Como atualizar os módulos Do Zure PowerShell na Automação Azure](../automation-update-azure-modules.md).

* Sem autenticação com diretório ativo para caixa de areia. Ao autenticar o Azure AD com um runbook, certifique-se de que o módulo Azure AD esteja disponível em sua conta de Automação. Certifique-se de conceder à conta Executar como as permissões necessárias para executar as tarefas que o runbook automatiza.

  Se o seu runbook não puder chamar um executável ou subprocesso em execução em uma caixa de areia Do Zure, use o runbook em um [Trabalhador de runbook híbrido](../automation-hrw-run-runbooks.md). Os trabalhadores híbridos não são limitados pelos limites de memória e rede que as caixas de areia do Azure têm.

* Dados de exceção demais. Há um limite de 1MB no fluxo de saída de trabalho. Certifique-se de que o seu manual de execução encerra as chamadas para um executável ou subprocesso usando `try` e `catch` bloqueia. Se as operações abrirem uma exceção, faça com que o código escreva a mensagem da exceção em uma variável Automação. Esta técnica impede que a mensagem seja escrita no fluxo de saída de trabalho.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: Falha no login na conta do Azure

### <a name="issue"></a>Problema

Você recebe um dos seguintes `Add-AzureAccount` erros `Connect-AzureRmAccount` ao trabalhar com o cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Esses erros ocorrem se o nome do ativo de credencial não for válido. Eles também podem ocorrer se o nome de usuário e a senha que você usou para configurar o ativo de credencial de Automação não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga os seguintes passos:

1. Certifique-se de que você não tem nenhum personagem especial. Esses caracteres incluem a `\@` no nome do ativo da credencial de Automação que você está usando para se conectar ao Azure.
2. Verifique se você pode usar o nome de usuário e a senha armazenados na credencial de Automação Azure no editor ISE do PowerShell local. Execute os seguintes cmdlets no PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se sua autenticação falhar localmente, você não configurará suas credenciais do Azure Active Directory corretamente. Consulte a [autenticação do Azure usando o](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) post do blog Azure Active Directory para obter a conta do Diretório Ativo do Azure configurada corretamente.

4. Se o erro parecer transitório, tente adicionar lógica de repetição à sua rotina de autenticação para tornar a autenticação mais robusta.

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
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Cenário: Referência de objeto não definida como uma instância de um objeto

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao invocar `Wait` um manual de crianças com o parâmetro e o fluxo De saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

`Start-AzureRmAutomationRunbook`não manuseie o fluxo de saída corretamente se o fluxo contiver objetos.

### <a name="resolution"></a>Resolução

Recomenda-se implementar uma lógica de votação e usar o [cmdlet Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para recuperar a saída. Uma amostra dessa lógica é definida abaixo.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
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

Use qualquer uma das seguintes soluções para corrigir este problema.

* Se você estiver tubulando objetos complexos de um cmdlet para `InlineScript` outro, enrole esses cmdlets em uma atividade.
* Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
* Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Cenário: Trabalho de runbook falha porque cota alocada excedeu

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. Essa cota aplica-se a todos os tipos de tarefa de execução de trabalho. Algumas dessas tarefas são testar um trabalho, iniciar um trabalho no portal, executar um trabalho usando webhooks ou agendar um trabalho para executar usando o portal Azure ou seu data center. Para saber mais sobre preços para Automação, consulte [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, altere sua assinatura do nível Free para o nível Básico.

1. Entre em sua assinatura do Azure.
2. Selecione a conta Automação para atualizar.
3. Clique **em Configurações** **e, em seguida, preços**.
4. Clique em **Ativar** na parte inferior da página para atualizar sua conta para o nível Básico.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando no seu runbook. É possível que o módulo contendo o cmdlet esteja faltando na conta, há um conflito de nome com um nome de runbook, ou o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

### <a name="resolution"></a>Resolução

Use qualquer uma das seguintes soluções para corrigir o problema.

* Certifique-se de que você inseriu o nome cmdlet corretamente.
* Certifique-se de que o cmdlet existe em sua conta de Automação e que não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e `Get-Command <CommandName>`procure o cmdlet que deseja encontrar na biblioteca ou execute . Depois de validar que o cmdlet está disponível para a conta e que não há conflitos de nome com outros cmdlets ou runbooks, adicione o cmdlet à tela e certifique-se de que você está usando um parâmetro válido definido em seu runbook.
* Se você tiver um conflito de nome e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema usando o nome totalmente qualificado para o cmdlet. Por exemplo, você pode usar `ModuleName\CmdletName`.
* Se você estiver executando o runbook no local em um grupo de trabalhadores híbridos, certifique-se de que o módulo e o cmdlet estejam instalados na máquina que hospeda o trabalhador híbrido.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: Um runbook de longa duração não consegue completar

### <a name="issue"></a>Problema

Seu runbook é exibido em um estado Parado após a execução por 3 horas. Você também pode receber este erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Esse comportamento é por design em caixas de areia do Azure devido ao monitoramento [justo](../automation-runbook-execution.md#fair-share) de processos dentro da Azure Automation. Se um processo for executado por mais de três horas, o fair share automaticamente interrompe um runbook. O status de um runbook que ultrapassa o limite de tempo de participação justa difere pelo tipo de runbook. Os runbooks do PowerShell e do Python são definidos para um status Parado. Os runbooks do fluxo de trabalho do PowerShell estão definidos como falha.

### <a name="cause"></a>Causa

O runbook ultrapassou o limite de 3 horas permitido por uma cota justa em uma caixa de areia Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Os trabalhadores híbridos não são limitados pelo limite de 3 horas do livro de reservas que as caixas de areia do Azure têm. Os runbooks executados no Hybrid Runbook Workers devem ser desenvolvidos para suportar comportamentos de reinicialização se houver problemas inesperados de infra-estrutura local.

Outra solução é otimizar o runbook criando [livros infantis](../automation-child-runbooks.md). Se o seu runbook fizer loops através da mesma função em vários recursos, por exemplo, em uma operação de banco de dados em vários bancos de dados, você pode mover a função para um runbook filho. Cada manual de crianças é executado em paralelo em um processo separado. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir.

Os cmdlets do PowerShell que habilitam o cenário do runbook filho são:

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook). Esse cmdlet permite que você inicie um runbook e passe parâmetros para o runbook.

* [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Se houver operações que precisam ser realizadas após a conclusão do livro infantil, este cmdlet permite que você verifique o status do trabalho de cada criança.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: Status: 400 Solicitação ruim ao chamar um webhook

### <a name="issue"></a>Problema

Quando você tenta invocar um webhook para um runbook do Azure Automation, você recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou.

### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você pode habilitar novamente o webhook por meio do portal do Azure. Se o webhook expirou, você deve excluí-lo e recriá-lo. Só é possível [renovar um webhook](../automation-webhooks.md#renew-webhook) se ele ainda não expirou.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: A taxa de solicitação é atualmente muito grande...

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao executar o cmdlet `Get-AzureRmAutomationJobOutput`:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tem muitos [fluxos Verbose](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Faça um dos seguintes para resolver este erro.

* Edite o runbook e reduza o número de fluxos de trabalho que ele emite.

* Reduza o número de fluxos a ser recuperado ao executar o cmdlet. Para fazer isso, você pode `Stream` definir o `Get-AzureRmAutomationJobOutput` valor do parâmetro para o cmdlet para recuperar apenas fluxos de saída. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Cenário: O trabalho do PowerShell falha com o erro: não é possível invocar o método

### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao iniciar um trabalho powershell em um runbook em execução no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Esse erro pode indicar que os runbooks que executam em uma caixa de areia Do Azure não podem ser executados no [modo de idioma completo](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há duas maneiras de resolver esse erro.

* Em vez `Start-Job`de `Start-AzureRmAutomationRunbook` usar, use para iniciar o runbook.
* Tente executar o manual em um Trabalhador de Runbook Híbrido.

Para saber mais sobre esse comportamento e outros comportamentos dos runbooks da Azure Automation, consulte [o comportamento do Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Cenário: Linux Hybrid Runbook Worker recebe um prompt para uma senha ao assinar um runbook

### <a name="issue"></a>Problema

Executando `sudo` o comando para um Linux Hybrid Runbook Worker recupera um prompt inesperado para uma senha.

### <a name="cause"></a>Causa

A conta **nxautomationuser do** agente Log Analytics para Linux não está configurada corretamente no arquivo **sudoers.** O Hybrid Runbook Worker precisa da configuração adequada de permissões de conta e outros dados para que possa assinar runbooks no Linux Runbook Worker.

### <a name="resolution"></a>Resolução

* Certifique-se de que o Hybrid Runbook Worker tem o GnuPG (GPG) executável na máquina.

* Verifique a configuração da conta de **usuário nxautomation** no arquivo **sudoers.** Consulte [Runningbooks em um trabalhador híbrido de runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: Falha do Cmdlet no runbook PnP PowerShell na Automação Azure

### <a name="issue"></a>Problema

Quando um runbook grava um objeto gerado pelo PnP PowerShell diretamente na saída azure Automation, a saída do cmdlet não pode ser transmitida de volta à Automação.

### <a name="cause"></a>Causa

Esse problema ocorre mais comumente quando o Azure Automation processa runbooks que invocam cmdlets PnP PowerShell, por exemplo, `add-pnplistitem`sem capturar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite seus scripts para atribuir quaisquer valores de retorno a variáveis para que os cmdlets não tentem escrever objetos inteiros para a saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet, conforme mostrado abaixo.

```azurecli
  $null = add-pnplistitem
```

Se o script analisar a saída de cmdlet, o script deve armazenar a saída em uma variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Meu problema não está listado acima

As seções abaixo listam outros erros comuns e fornecem documentação de suporte para ajudá-lo a resolver seu problema.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>O Hybrid Runbook Worker não executa trabalhos ou não está respondendo

Se você está executando trabalhos em um Híbrido Runbook Worker em vez de em Azure Automation, você pode precisar [solucionar problemas do próprio trabalhador híbrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook falha sem permissão ou alguma variação

Executar como contas pode não ter as mesmas permissões contra os recursos do Azure como sua conta corrente. Certifique-se de que sua conta Run As tenha [permissões para acessar todos os recursos usados](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) em seu script.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemas passando parâmetros em webhooks

Para obter ajuda para passar parâmetros em webhooks, consulte [Inicie um runbook a partir de um webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemas usando módulos AZ

O uso de módulos Az e módulos AzureRM na mesma conta de automação não é suportado. Consulte [os módulos Az em runbooks](https://docs.microsoft.com/azure/automation/az-modules) para obter mais detalhes.

### <a name="inconsistent-behavior-in-runbooks"></a>Comportamento inconsistente em runbooks

Siga as orientações na [execução do Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) para evitar problemas com trabalhos simultâneos, recursos sendo criados várias vezes ou outra lógica sensível ao tempo em runbooks.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook falha com o erro Sem permissão, Proibido (403), ou alguma variação

Executar como contas pode não ter as mesmas permissões contra os recursos do Azure como sua conta corrente. Certifique-se de que sua conta Run As tenha [permissões para acessar todos os recursos usados](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) em seu script.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam funcionando, mas de repente pararam

* Certifique-se de que a conta Run As não expirou. Veja [a renovação da certificação](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se você estiver usando um [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) para iniciar um runbook, certifique-se de que o webhook não expirou.

### <a name="passing-parameters-into-webhooks"></a>Passando parâmetros a webhooks

Para obter ajuda para passar parâmetros em webhooks, consulte [Inicie um runbook a partir de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Usando módulos Az

O uso de módulos Az e módulos AzureRM na mesma conta de automação não é suportado. Consulte [módulos Az em runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Usando certificados auto-assinados

Para usar certificados auto-assinados, consulte [Criar um novo certificado](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Acesso negado ao usar caixa de areia do Azure para um runbook

A caixa de areia do Azure impede o acesso a todos os servidores COM fora de processo. Por exemplo, um aplicativo ou runbook sandboxed não pode ligar para o WMI (Windows Management Instrumentation, instrumentação de gerenciamento do Windows) ou para o serviço windows installer (msiserver.exe). Para obter detalhes sobre o uso da caixa de areia, consulte [execução do Runbook no Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Documentos Recomendados

* [Como iniciar um Runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Execução de runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
