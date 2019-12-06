---
title: Solucionar erros com os livros de execução de automação do Azure
description: Saiba como solucionar problemas com runbooks de automação do Azure
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 84a17cb4468f60abf2463e6aa3ca331466aad247
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850135"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Solucionar problemas de erros com runbooks

Quando houver erros ao executar runbooks na automação do Azure, você poderá usar as etapas a seguir para ajudar a diagnosticar o problema.

1. **Verifique se o script de runbook é executado com êxito no computador local:**  Consulte a documentação do [PowerShell](/powershell/scripting/overview) ou os [documentos do Python](https://docs.python.org/3/) para referência de linguagem e módulos de aprendizado.

   Executar o script localmente pode descobrir e resolver erros comuns, como:

   - **Módulos ausentes**
   - **Erros de sintaxe**
   - **Erros lógicos**

2. **Investigue** os [fluxos de erro](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) de runbook para mensagens específicas e compare-os com os erros abaixo.

3. **Verifique se os nós e o espaço de trabalho de automação têm os módulos necessários:** Se o runbook importar módulos, verifique se eles estão disponíveis na sua conta de automação usando as etapas listadas em [Importar módulos](../shared-resources/modules.md#import-modules). Atualize seus módulos para a versão mais recente seguindo as instruções em [Atualizar módulos do Azure na automação do Azure](..//automation-update-azure-modules.md). Para obter mais informações sobre solução de problemas, consulte [solucionar problemas de módulos](shared-resources.md#modules).

Se o runbook for suspenso ou falhar inesperadamente:

* [Verifique](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) se os status do trabalho definem os status do runbook e algumas causas possíveis.
* [Adicione uma saída adicional](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) ao runbook para identificar o que acontece antes de o runbook ser suspenso.
* [Manipule quaisquer exceções](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) geradas pelo seu trabalho.

## <a name="login-azurerm"></a>Cenário: execute login-AzureRMAccount para fazer logon

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao executar um runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

É um erro que pode ocorrer quando você não está usando uma conta RunAs ou a conta RunAs expirou. Consulte [gerenciar contas Executar como da automação do Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Esse erro tem duas causas principais:

* Versões diferentes dos módulos do AzureRM.
* Você está tentando acessar recursos em uma assinatura separada.

### <a name="resolution"></a>Resolução

Se você receber esse erro depois de atualizar um módulo AzureRM, deverá atualizar todos os seus módulos AzureRM para a mesma versão.

Se você estiver tentando acessar recursos em outra assinatura, poderá seguir as etapas abaixo para configurar permissões.

1. Vá para a conta Executar como da conta de automação e copie a ID do aplicativo e a impressão digital.
  ![copiar a ID do aplicativo e a impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)
1. Vá para o controle de acesso da assinatura em que a conta de automação não está hospedada e adicione uma nova atribuição de função.
  ![Controle de acesso](../media/troubleshoot-runbooks/access-control.png)
1. Adicione a ID do aplicativo coletada na etapa anterior. Selecione permissões de colaborador.
   ![adicionar](../media/troubleshoot-runbooks/add-role-assignment.png) de atribuição de função
1. Copie o nome da assinatura para a próxima etapa.
1. Agora você pode usar o seguinte código de runbook para testar as permissões de sua conta de automação para a outra assinatura.

    Substitua o "\<CertificateThumbprint\>" pelo valor copiado na etapa #1 e o valor "\<Subscriptionname\>" copiado na etapa #4.

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

## <a name="unable-to-find-subscription"></a> Cenário: não é possível encontrar a assinatura do Azure

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com os cmdlets `Select-AzureSubscription` ou `Select-AzureRmSubscription`:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Esse erro pode ocorrer se:

* O nome da assinatura é inválido

* O usuário do Azure Active Directory que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.

### <a name="resolution"></a>Resolução

Siga estas etapas para determinar se você se autenticou no Azure e tem acesso à assinatura que está tentando selecionar:

1. Para ter certeza de que o seu script funciona de maneira independente, teste-o fora da Automação do Azure.
2. Antes de executar o cmdlet `Select-AzureSubscription`, verifique se você executou o cmdlet `Add-AzureAccount`.
3. Adicione `Disable-AzureRmContextAutosave –Scope Process` ao início do seu runbook. Esse cmdlet garante que todas as credenciais sejam aplicadas apenas à execução do runbook atual.
4. Se essa mensagem de erro ainda for exibida, modifique o código adicionando o parâmetro **-AzureRmContext** após o cmdlet `Add-AzureAccount` e execute o código.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Cenário: Autenticação do Azure falhou porque a autenticação multifator é habilitada

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com o nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, você não pode usar um usuário do Active Directory do Azure para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para se autenticar no Azure.

### <a name="resolution"></a>Resolução

Para usar um certificado com os cmdlets do modelo de implantação clássico do Azure, consulte [criando e adicionando um certificado para gerenciar os serviços do Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com os cmdlets do Azure Resource Manager, veja [criando entidades de serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [autenticando uma entidade de serviço com o Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="get-serializationsettings"></a>Cenário: você vê um erro em seus fluxos de trabalho sobre o método de get_SerializationSettings

### <a name="issue"></a>Problema

Você verá o erro em seus fluxos de trabalho para um runbook com a seguinte mensagem:

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

Esse erro é causado ao usar ambos os cmdlets AzureRM e Az em um runbook. Ele ocorre quando você importa `Az` antes de importar `AzureRM`.

### <a name="resolution"></a>Resolução

Os cmdlets de Az e AzureRM não podem ser importados e usados no mesmo runbook. Para saber mais sobre o suporte para Az na Automação do Azure, confira [suporte do módulo Az na Automação do Azure](../az-modules.md).

## <a name="task-was-cancelled"></a>Cenário: O runbook falha com o erro: uma tarefa foi cancelada

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Esse erro pode ser causado por meio de módulos do Azure desatualizados.

### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido atualizando os módulos do Azure para a versão mais recente.

Na sua conta de automação, clique em **módulos**e clique em **módulos do Azure atualização**. A atualização leva aproximadamente 15 minutos. Depois de concluída, execute novamente o runbook que apresentou falha. Para saber mais sobre como atualizar seus módulos, consulte [Atualizar os módulos do Azure na Automação do Azure](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Cenário: os Runbooks falham ao lidar com várias assinaturas

### <a name="issue"></a>Problema

Ao executar runbooks, o runbook falha ao gerenciar recursos do Azure.

### <a name="cause"></a>Causa

O runbook não está usando o contexto correto durante a execução.

### <a name="resolution"></a>Resolução

Ao trabalhar com várias assinaturas, o contexto de assinatura pode ser perdido ao invocar runbooks. Para garantir que o contexto da assinatura seja passado para os runbooks, adicione o parâmetro `AzureRmContext` ao cmdlet e passe o contexto para ele. Também é recomendável usar o cmdlet `Disable-AzureRmContextAutosave` com o escopo **Process** para garantir que as credenciais que você usa somente sejam usadas para o runbook atual.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
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

Para obter mais informações, consulte [trabalhando com várias assinaturas](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Cenário: termo não reconhecido como o nome de um cmdlet, função, script

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer devido a um dos seguintes motivos:

* O módulo que contém o cmdlet não é importado para a conta de automação
* O módulo que contém o cmdlet é importado, mas está desatualizado

### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido executando uma das seguintes tarefas:

Se o módulo for um módulo do Azure, consulte [Como atualizar módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md) para saber como atualizar os módulos em sua conta de automação.

Se for um módulo separado, verifique se o módulo foi importado na sua conta de automação.

## <a name="job-attempted-3-times"></a>Cenário: O início do trabalho de runbook foi tentado três vezes, mas falhou ao iniciar a cada vez

### <a name="issue"></a>Problema

O runbook falhar com o erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

* Limite de Memória. Os limites documentados sobre quanta memória é alocada em uma área restrita é encontrado em [Limites do serviço de automação](../../azure-subscription-service-limits.md#automation-limits). Um trabalho pode falhar se ele está usando mais de 400 MB de memória.

* Soquetes de rede. Áreas restritas do Azure são limitadas a 1.000 soquetes de rede simultâneos, conforme descrito em [Limites de serviço de automação](../../azure-subscription-service-limits.md#automation-limits).

* Módulo Incompatível. Esse erro pode ocorrer se as dependências do módulo não estiverem corretas e, se não o forem, o seu runbook normalmente retornará uma mensagem "Comando não encontrado" ou "Não é possível ligar o parâmetro".

* Seu runbook tentou chamar um executável ou subprocesso em um runbook executado em uma área restrita do Azure. Não há suporte para esse cenário em áreas restritas do Azure.

* O runbook tentou gravar muitos dados de exceção no fluxo de saída.

### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Métodos sugeridos para trabalhar dentro do limite de memória são dividir a carga de trabalho entre vários runbooks, não processar muitos dados na memória, não gravar saída desnecessária de seus runbooks nem considerar quantos pontos de verificação você grava nos runbooks de fluxo de trabalho do PowerShell. Você pode usar o método de limpeza, como `$myVar.clear()` para limpar a variável e usar `[GC]::Collect()` para executar a coleta de lixo imediatamente. Essas ações reduzem o volume de memória do seu runbook durante o runtime.

* Atualize os módulos do Azure seguindo as etapas [Como atualizar os módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md).

* Outra solução é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Workers não são limitados pelos limites de memória e rede que são áreas de segurança do Azure.

* Se precisar chamar um processo (como .exe ou subprocess.call) em um runbook, é preciso executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

* Há um limite de 1MB no fluxo de saída do trabalho. Certifique-se de colocar chamadas para um executável ou subprocesso em um bloco try/catch. Se eles lançarem uma exceção, grave a mensagem dessa exceção em uma variável de automação. Isso impedirá que ele seja gravado no fluxo de saída do trabalho.

## <a name="sign-in-failed"></a>Cenário: Entrar na conta do Azure falha

### <a name="issue"></a>Problema

Você recebe um dos seguintes erros ao trabalhar com os cmdlets `Add-AzureAccount` ou `Connect-AzureRmAccount`:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Esse erro ocorre se o nome do ativo da credencial é inválido. Esse erro também pode ocorrer se o nome de usuário e a senha usados para configurar o ativo da credencial de Automação são inválidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga os seguintes passos:

1. Não use caracteres especiais. Esses caracteres incluem a **\@** no nome do ativo da credencial de Automação que você está usando para se conectar ao Azure.
2. Verifique se você pode usar o nome de usuário e a senha que estão armazenados na credencial da Automação do Azure no editor do ISE do PowerShell local. Você pode verificar se o nome de usuário e a senha estão corretos executando os seguintes cmdlets no PowerShell ISE:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se a sua autenticação falhar localmente, isso significa que você não configurou as credenciais do Azure Active Directory corretamente. Confira a postagem de blog [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) (Autenticação no Azure usando o Azure Active Directory) para configurar corretamente a conta do Azure Active Directory.

4. Se parecer um erro temporário, tente adicionar lógica de nova tentativa à sua rotina de autenticação para tornar a autenticação mais robusta.

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

## <a name="child-runbook-object"></a>Referência de objeto não definida para uma instância do objeto

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao invocar um runbook filho com a opção `-Wait` e o fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Há um problema conhecido em que [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) não processa o fluxo de saída corretamente se ele contém objetos.

### <a name="resolution"></a>Resolução

Para resolver o problema, recomenda-se implementar uma lógica de sondagem e usar o cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para recuperar a saída. Um exemplo dessa lógica é definida no exemplo a seguir.

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

## <a name="fails-deserialized-object"></a>Cenário: O Runbook falha devido a objeto desserializado

### <a name="issue"></a>Problema

O runbook falhar com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o seu runbook for um Fluxo de Trabalho do PowerShell, ele armazenará objetos complexos em um formato desserializado para manter seu estado de runbook se o fluxo de trabalho estiver suspenso.

### <a name="resolution"></a>Resolução

Qualquer uma das seguintes três soluções corrigir esse problema:

* Se você estiver direcionando objetos complexos de um cmdlet para outro, encapsule os cmdlets em um InlineScript.
* Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
* Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

## <a name="quota-exceeded"></a>Cenário: O trabalho de Runbook falhou porque excedeu a cota alocada

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. Essa cota aplica-se a todos os tipos de tarefa de execução de trabalho. Algumas dessas tarefas podem ser testar um trabalho, iniciar um trabalho no portal, executar um trabalho usando webhooks ou agendar um trabalho para ser executado usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços para Automação, confira os [Preços de automação](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, você precisa alterar sua assinatura de gratuita camada para a camada básica. Para atualizar para o tipo Básico, execute as seguintes etapas:

1. Entre na sua assinatura do Azure
2. Selecione a conta de Automação que você deseja atualizar
3. Clique em **Configurações** > **Preço**.
4. Clique em **Habilitar** na parte inferior da página para atualizar sua conta para o tipo **Básico**.

## <a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando no seu runbook. Esse erro pode ocorrer porque o módulo que contém o cmdlet não está na conta, há um conflito de nome com um nome de runbook ou o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se você inseriu o nome do cmdlet corretamente.
* Verifique se o cmdlet existe em sua conta de Automação e se não há conflitos. Para verificar se o cmdlet estiver presente, abra um runbook no modo de edição e a pesquisa para o cmdlet que você deseja localizar na biblioteca ou execução `Get-Command <CommandName>`. Depois de validar que o cmdlet está disponível para a conta e que não há conflitos de nome com outros cmdlets ou runbooks, adicione-o à tela e verifique se está usando um conjunto de parâmetros válido no seu runbook.
* Se houver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, é possível resolver o problema usando o nome totalmente qualificado do cmdlet. Por exemplo, você pode usar **NomeDoMódulo\NomeDoCmdlet**.
* Se você estiver executando o runbook local em um grupo de trabalhadores híbrido, verifique se o módulo e cmdlet está instalado na máquina que hospeda o trabalhador híbrido.

## <a name="long-running-runbook"></a>Cenário: falha de conclusão de um runbook de execução longa

### <a name="issue"></a>Problema

Seu runbook é exibido em um estado **Parado** após a execução por 3 horas. Você também pode receber o erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Esse comportamento existe, por natureza, nas áreas restritas do Azure devido ao monitoramento de "Fração Justa" dos processos na Automação do Azure. Se for executado por mais de três horas, a Fração Justa interrompe automaticamente um runbook. O status de um runbook que ultrapassa o limite de tempo de compartilhamento justo difere por tipo de runbook. Os runbooks do PowerShell e do Python são definidos para um status **Parado**. Os runbooks do fluxo de trabalho do PowerShell estão definidos como **falha**.

### <a name="cause"></a>Causa

O runbook foi executado durante o limite de 3 horas permitido pelo compartilhamento justo em uma caixa de proteção do Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybrid Workers não são limitados pela [justa](../automation-runbook-execution.md#fair-share) limite de runbook de 3 horas que são áreas de segurança do Azure. Os runbooks executados em Hybrid Runbook Workers devem ser desenvolvidos para oferecer suporte a comportamentos de reinicialização, caso haja problemas de infraestrutura local inesperados.

Outra opção é otimizar o runbook criando [runbooks filho](../automation-child-runbooks.md). Se o seu runbook percorre continuamente a mesma função em vários recursos, como uma operação de banco de dados em vários bancos de dados, é possível mover essa função para um runbook filho. Cada um desses runbooks filho é executado em paralelo em processos separados. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir.

Os cmdlets do PowerShell que habilitam o cenário do runbook filho são:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) -este cmdlet permite que você iniciar um runbook e passar parâmetros para o runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob): se houver operações que precisem ser executadas depois que o runbook filho for concluído, esse cmdlet permite verificar o status do trabalho de cada filho.

## <a name="expired webhook"></a>Cenário: status: 400 solicitação inadequada ao chamar um webhook

### <a name="issue"></a>Problema

Ao tentar chamar um webhook para um runbook de Automação do Azure, você recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou.

### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você pode habilitar novamente o webhook por meio do portal do Azure. Se o webhook expirou, o webhook precisa ser excluído e recriado. Só é possível [renovar um webhook](../automation-webhooks.md#renew-webhook) se ele ainda não expirou.

## <a name="429"></a>Cenário: 429: a taxa de solicitação é muito grande no momento. Tente novamente

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao executar o cmdlet `Get-AzureRmAutomationJobOutput`:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tenha muitos [fluxos detalhados](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Há três maneiras de resolver esse erro:

* Edite o runbook e reduza o número de fluxos de trabalho que ele emite.
* Reduza o número de fluxos a ser recuperado ao executar o cmdlet. Para fazer isso, você pode especificar o parâmetro `-Stream Output` para o cmdlet `Get-AzureRmAutomationJobOutput` para recuperar apenas os fluxos de saída. 

## <a name="cannot-invoke-method"></a>Cenário: falha no trabalho do PowerShell com o erro: não é possível invocar o método

### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao iniciar um trabalho do PowerShell em um runbook em execução no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você inicia um trabalho do PowerShell em um runbook executado no Azure. Esse comportamento pode ocorrer porque os runbooks executados em uma área restrita do Azure podem não ser executados no [modo de linguagem completa](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há três maneiras de resolver esse erro:

* Em vez de usar `Start-Job`, use `Start-AzureRmAutomationRunbook` para iniciar um runbook
* Se o runbook tiver essa mensagem de erro, execute-a em um Hybrid Runbook Worker

Para saber mais sobre esse comportamento e outros comportamentos dos Runbooks de automação do Azure, consulte [comportamento do runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="other"></a>Meu problema não está listado acima

As seções a seguir listam outros erros comuns, além da documentação de suporte para ajudá-lo a resolver o problema.

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>O Hybrid Runbook Worker não executa trabalhos ou não está respondendo

Se você estiver executando trabalhos usando um Hybrid Worker em vez de na automação do Azure, talvez seja necessário [solucionar o problema do próprio trabalhador híbrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>O runbook falha com "Sem permissão" ou algo semelhante

As contas Executar como não podem ter as mesmas permissões da sua conta atual em relação aos recursos do Azure. Verifique se a conta Executar como [tem permissões para acessar recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usados no script.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam funcionando, mas de repente pararam

* Se os runbooks foram executados anteriormente, mas forem interrompidos, [Verifique se a conta Executar como não expirou](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se você está usando webhooks para iniciar runbooks, [verifique se o webhook não expirou](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="issues-passing-parameters-into-webhooks"></a>Problemas ao passar parâmetros para WebHooks

Para obter ajuda para passar parâmetros em WebHooks, consulte [Iniciar um runbook de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="issues-using-az-modules"></a>Problemas usando módulos AZ

Não há suporte para o uso de módulos Az e módulos do AzureRM na mesma Conta da Automação. Consulte [AZ modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules) para obter mais detalhes.

## <a name="inconsistent-behavior-in-runbooks"></a>Comportamento inconsistente em runbooks

Siga as diretrizes em [Execução de Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) para evitar problemas com trabalhos simultâneos, criação de recursos várias vezes ou outra lógica dependente de tempo em runbooks.

## <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>O runbook falha com os erros: nenhuma permissão, proibido, 403 ou alguma variação

As contas Executar como não podem ter as mesmas permissões da sua conta atual em relação aos recursos do Azure. Verifique se sua conta RunAs tem [permissões para acessar os recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usados em seu script.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam funcionando, mas de repente pararam

* Se os runbooks foram executados anteriormente, mas forem interrompidos, verifique se a conta Executar como [não expirou](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se você estiver usando WebHooks para iniciar runbooks, verifique se o webhook [não expirou](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="passing-parameters-into-webhooks"></a>Passando parâmetros a webhooks

Para obter ajuda para passar parâmetros em WebHooks, consulte [Iniciar um runbook de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="using-az-modules"></a>Usando módulos Az

Não há suporte para o uso de módulos Az e módulos do AzureRM na mesma Conta da Automação. Consulte [AZ modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules) para obter mais detalhes.

## <a name="using-self-signed-certificates"></a>Usar certificados autoassinados

Para usar certificados autoassinados, você deve seguir o guia em [criando um novo certificado](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Documentos Recomendados

* [Inicie um runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Execução de Runbook na Automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Próximos passos

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
