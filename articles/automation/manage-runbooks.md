---
title: Gerenciar runbooks na Automação do Azure
description: Este artigo descreve como gerenciar os runbooks na Automação do Azure. Ele abrange operações básicas e adiciona algumas práticas recomendadas.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 687579825c652888112ff8ddff7401b3305e3a8e
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871188"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerenciar runbooks na Automação do Azure

Você pode adicionar um runbook à automação do Azure criando um novo ou importando um existente de um arquivo ou da [Galeria de runbooks](automation-runbook-gallery.md). Este artigo fornece informações para gerenciar um runbook importado de um arquivo. Você pode encontrar todos os detalhes de como acessar runbooks e módulos da Comunidade em [galerias de runbook e de módulo para a automação do Azure](automation-runbook-gallery.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Criar um runbook

Crie um novo runbook na automação do Azure usando o portal do Azure ou o Windows PowerShell. Depois que o runbook tiver sido criado, você poderá editá-lo usando as informações em:

* [Editar runbook textual na automação do Azure](automation-edit-textual-runbook.md) 
* [Aprenda os principais conceitos de fluxo de trabalho do Windows PowerShell para runbooks de automação](automation-powershell-workflow.md)
* [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* [Gerenciar pacotes do Python 2 no Automação do Azure](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um runbook no portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. No Hub, selecione **Runbooks** em **automação de processo** para abrir a lista de runbooks.
3. Clique em **criar um runbook**.
4. Insira um nome para o runbook e selecione seu [tipo](automation-runbook-types.md). O nome do runbook deve começar com uma letra e pode conter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o runbook e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um runbook com o PowerShell

Use o cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) para criar um runbook vazio. Use o `Type` parâmetro para especificar um dos tipos de runbook definidos para `New-AzAutomationRunbook`.

O exemplo a seguir mostra como criar um novo runbook vazio.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um runbook

Você pode importar um script do PowerShell ou do fluxo de trabalho do PowerShell (**. ps1**), um runbook gráfico (**. graphrunbook**) ou um script Python 2 (**. py**) para criar seu próprio runbook.  Você precisa especificar o [tipo de runbook](automation-runbook-types.md) que é criado durante a importação, levando em consideração os aspectos a seguir.

* Você pode importar um arquivo **. ps1** que não contém um fluxo de trabalho para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou um runbook de fluxo de trabalho do [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se você importá-lo para um runbook de fluxo de trabalho do PowerShell, ele será convertido em um fluxo de trabalho. Nesse caso, os comentários são incluídos no runbook para descrever as alterações feitas.

* Você pode importar apenas um arquivo **. ps1** contendo um fluxo de trabalho do PowerShell para um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se o arquivo contiver vários fluxos de trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importá-los separadamente.

* Não importe um arquivo **. ps1** contendo um fluxo de trabalho do PowerShell para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), pois o mecanismo de script do PowerShell não pode reconhecê-lo.

* Importe apenas um arquivo **. graphrunbook** para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks). 

### <a name="import-a-runbook-from-the-azure-portal"></a>Importar um runbook do portal do Azure

Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure.

> [!NOTE]
> Você só pode importar um arquivo **. ps1** para um runbook de fluxo de trabalho do PowerShell usando o Portal.

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Clique em **importar um runbook**.
4. Clique em **arquivo de runbook** e selecione o arquivo a ser importado.
5. Se o campo **nome** estiver habilitado, você terá a opção de alterar o nome do runbook. O nome deve começar com uma letra e pode conter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas você pode alterá-lo depois levar as restrições aplicáveis em consideração.
7. Clique em **Criar**. O novo runbook é exibido na lista de runbooks da conta de Automação.
8. Será necessário [publicar o runbook](#publish-a-runbook) antes de executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico, você pode convertê-lo em outro tipo. No entanto, não é possível converter um runbook gráfico em um runbook textual.

### <a name="import-a-runbook-with-windows-powershell"></a>Importar um runbook com o Windows PowerShell

Use o cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) para importar um arquivo de script como um runbook de rascunho. Se o runbook já existir, a importação falhará, a menos `Force` que você use o parâmetro com o cmdlet.

O exemplo a seguir mostra como importar um arquivo de script para um runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Manipular recursos

Se o runbook criar um [recurso](automation-runbook-execution.md#resources), o script deverá verificar se o recurso já existe antes de tentar criá-lo. Aqui está um exemplo básico.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Recuperar detalhes do log de atividades

Você pode recuperar detalhes do runbook, como a pessoa ou a conta que iniciou um runbook, do [log de atividades](automation-runbook-execution.md#activity-logging) da conta de automação. O exemplo do PowerShell a seguir fornece o último usuário para executar o runbook especificado.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Controlar o progresso

É uma boa prática criar seus runbooks para serem modulares por natureza, com lógica que pode ser reutilizada e reiniciada facilmente. O rastreamento do progresso em um runbook garante que a lógica do runbook seja executada corretamente se houver problemas. 

Você pode acompanhar o progresso de um runbook usando uma fonte externa, como uma conta de armazenamento, um banco de dados ou arquivos compartilhados. Crie lógica em seu runbook para verificar primeiro o estado da última ação executada. Em seguida, com base nos resultados da verificação, a lógica pode ignorar ou continuar tarefas específicas no runbook.

## <a name="prevent-concurrent-jobs"></a>Evitar trabalhos simultâneos

Alguns runbooks se comportam de forma invariada se forem executados em vários trabalhos ao mesmo tempo. Nesse caso, é importante que um runbook implemente a lógica para determinar se já existe um trabalho em execução. Aqui está um exemplo básico.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Tratar erros transitórios em um script dependente de tempo

Seus runbooks devem ser robustos e capazes de lidar com [erros](automation-runbook-execution.md#errors), incluindo erros transitórios que podem causar a reinicialização ou a falha. Se um runbook falhar, a automação do Azure tentará novamente.

Se seu runbook normalmente for executado dentro de uma restrição de tempo, faça com que o script implemente a lógica para verificar o tempo de execução. Essa verificação garante a execução de operações como inicialização, desligamento ou expansão somente durante horários específicos.

> [!NOTE]
> A hora local no processo de área restrita do Azure é definida como UTC. Os cálculos para data e hora em seus runbooks devem levar esse fato em consideração.

## <a name="work-with-multiple-subscriptions"></a>Como trabalhar com várias assinaturas

Seu runbook deve ser capaz de trabalhar com [assinaturas](automation-runbook-execution.md#subscriptions). Por exemplo, para lidar com várias assinaturas, o runbook usa o cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) . Esse cmdlet garante que o contexto de autenticação não seja recuperado de outro runbook em execução na mesma área restrita. O runbook também usa o`AzContext` parâmetro nos cmdlets do módulo AZ e passa o contexto apropriado.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="work-with-a-custom-script"></a>Trabalhar com um script personalizado

> [!NOTE]
> Normalmente, você não pode executar os scripts personalizados e runbooks no host com um agente de Log Analytics instalado. 

Para usar um script personalizado:

1. Crie uma conta de automação e obtenha uma [função de colaborador](automation-role-based-access-control.md).
2. [Vincule a conta ao espaço de trabalho do Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection.md).
3. Habilite [Hybrid runbook Worker](automation-hybrid-runbook-worker.md), [Gerenciamento de atualizações](automation-update-management.md)ou outro recurso de automação. 
4. Se estiver em um computador Linux, você precisa de permissões altas. Faça logon para desativar as [verificações de assinatura](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testar um runbook

Quando você testa um runbook, a [Versão de rascunho](#publish-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos de [saída](automation-runbook-output-and-messages.md#output-stream) e de [aviso e de erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no painel saída de teste. As mensagens para o [fluxo detalhado](automation-runbook-output-and-messages.md#message-streams) serão exibidas no painel de saída somente se a variável [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) for definida `Continue`como.

Mesmo que a versão de rascunho esteja em execução, o runbook ainda será executado normalmente e executará qualquer ação nos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks nos recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo. Não há nenhuma diferença no teste entre o editor de texto e o editor gráfico no portal do Azure.

1. Abra a versão de rascunho do runbook no editor de [texto](automation-edit-textual-runbook.md) ou no [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique em **testar** para abrir a página de teste.
1. Se o runbook tiver parâmetros, eles serão listados no painel esquerdo, onde você pode fornecer valores a serem usados para o teste.
1. Se você quiser executar o teste em um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md), altere **as configurações de execução** para **Hybrid Worker** e selecione o nome do grupo de destino.  Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
1. Clique em **Iniciar** para iniciar o teste.
1. Você pode usar os botões no painel de saída para parar ou suspender um [fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou runbook [gráfico](automation-runbook-types.md#graphical-runbooks) enquanto ele está sendo testado. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
1. Inspecione a saída do runbook no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um runbook

Quando você criar ou importar um novo runbook, deverá publicá-lo antes de poder executá-lo. Cada runbook na automação do Azure tem uma versão de rascunho e uma versão publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de rascunho deve ser disponibilizada, você a publica, substituindo a versão publicada atual pela versão de rascunho.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publicar um runbook no portal do Azure

1. Abra o runbook no portal do Azure.
2. Clique em **Editar**.
3. Clique em **publicar** e em **Sim** em resposta à mensagem de verificação.

### <a name="publish-a-runbook-using-powershell"></a>Publicar um runbook usando o PowerShell

Use o cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) para publicar seu runbook. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Agendar um runbook no portal do Azure

Quando o runbook tiver sido publicado, você poderá agendá-lo para a operação:

1. Abra o runbook no portal do Azure.
2. Selecione **agendas** em **recursos**.
3. Selecione **Adicionar um agendamento**.
4. No painel agendar runbook, selecione **vincular um agendamento ao seu runbook**.
5. Escolha **criar um novo agendamento** no painel agendamento.
6. Insira um nome, uma descrição e outros parâmetros no painel novo agendamento. 
7. Depois que a agenda for criada, realce-a e clique em **OK**. Agora ele deve ser vinculado ao seu runbook.
8. Procure um email em sua caixa de correio para notificá-lo do status do runbook.

## <a name="obtain-job-statuses"></a>Obter status do trabalho

### <a name="view-statuses-in-the-azure-portal"></a>Exibir status no portal do Azure

Os detalhes do tratamento de trabalhos na automação do Azure são fornecidos em [trabalhos](automation-runbook-execution.md#jobs). Quando você estiver pronto para ver seus trabalhos de runbook, use portal do Azure e acesse sua conta de automação. À direita, você pode ver um resumo de todos os trabalhos de runbook em **Estatísticas de trabalho**. 

![Bloco Estatísticas de Trabalho](./media/manage-runbooks/automation-account-job-status-summary.png)

O resumo exibe uma contagem e representação gráfica do status do trabalho para cada trabalho executado.

Clicar no bloco apresenta a página Trabalhos, que contém uma lista resumida de todos os trabalhos executados. Esta página mostra o status, o nome do runbook, a hora de início e o tempo de conclusão de cada trabalho.

![Página Trabalhos da conta de automação](./media/manage-runbooks/automation-account-jobs-status-blade.png)

Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos**. Filtre um runbook específico, o status do trabalho ou uma opção na lista suspensa e forneça o intervalo de tempo para a pesquisa.

![Filtrar status do trabalho](./media/manage-runbooks/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na página Runbooks em sua conta de automação e, em seguida, selecionando **trabalhos**. Essa ação apresenta a página trabalhos. A partir daqui, você pode clicar em um registro de trabalho para exibir seus detalhes e saída.

![Página Trabalhos da conta de automação](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>Recuperar status do trabalho usando o PowerShell

Use o cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você iniciar um runbook usando `Start-AzAutomationRunbook`, ele retornará o trabalho resultante. Use [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) para recuperar a saída do trabalho.

O exemplo a seguir obtém o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída de um trabalho específico e retorna cada registro. Se houver uma [exceção](automation-runbook-execution.md#exceptions) para um dos registros, o script gravará a exceção em vez do valor. Esse comportamento é útil, pois as exceções podem fornecer informações adicionais que talvez não sejam registradas normalmente durante a saída.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o processamento de runbook, consulte [execução de runbook na automação do Azure](automation-runbook-execution.md).
* Para saber mais sobre como editar runbooks do PowerShell e do fluxo de trabalho do PowerShell com um editor de texto, consulte [Editar runbooks textuais na automação do Azure](automation-edit-textual-runbook.md).
* Para saber mais sobre a criação de runbook gráfico, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).
