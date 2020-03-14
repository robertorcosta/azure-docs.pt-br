---
title: Execução de runbook na Automação do Azure
description: Descreve os detalhes de como um runbook na Automação do Azure é processado.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367135"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure

Os Runbooks são executados com base na lógica definida dentro deles. Se um runbook é interrompido, ele é reiniciado no início. Esse comportamento exige que você grave runbooks que dão suporte à reinicialização se ocorrerem problemas transitórios.

Iniciar um runbook na automação do Azure cria um trabalho. Um trabalho é uma instância de execução única do runbook. Cada trabalho tem acesso aos recursos do Azure fazendo uma conexão com sua assinatura do Azure. O trabalho só tem acesso aos recursos em seu datacenter se esses recursos estiverem acessíveis da nuvem pública.

A automação do Azure atribui um trabalho para executar cada trabalho durante a execução do runbook. Enquanto os trabalhadores são compartilhados por muitas contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não tem controle sobre qual trabalhador serviços de sua solicitação de trabalho.

Quando você exibe a lista de runbooks no portal do Azure, ele mostra o status de cada trabalho que foi iniciado para cada runbook. A automação do Azure armazena logs de trabalho por um máximo de 30 dias. 

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks), [runbooks gráficos](automation-runbook-types.md#graphical-runbooks)e [runbooks de workflow do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Onde executar seus runbooks

Os Runbooks na automação do Azure podem ser executados em uma área restrita do Azure ou em um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md). A maioria dos runbooks pode ser facilmente executada em uma área restrita do Azure, um ambiente compartilhado que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são restringidos pelas limitações de recurso da área restrita.

Você pode usar um Hybrid Runbook Worker para executar runbooks diretamente no computador que hospeda a função e em relação aos recursos locais no ambiente. A automação do Azure armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores atribuídos.

A tabela a seguir lista algumas tarefas de execução de runbook com o ambiente de execução recomendado listado para cada uma.

|Tarefa|Melhor opção|Observações|
|---|---|---|
|Integração com serviços do Azure|Área restrita do Azure|Hospedado no Azure, a autenticação é mais simples. Se você estiver usando uma Hybrid Runbook Worker em uma VM do Azure, poderá usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Obter o desempenho ideal para gerenciar recursos do Azure|Área restrita do Azure|O script é executado no mesmo ambiente, que tem menos latência.|
|Redução de custos operacionais|Área restrita do Azure|Não há nenhuma sobrecarga de computação e nenhuma necessidade de uma VM.|
|Executar script de execução longa|Hybrid Runbook Worker|As áreas restritas do Azure têm [limitações nos recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagir com serviços locais|Hybrid Runbook Worker|Pode ter acesso diretamente ao computador host.|
|Exigir software de terceiros e executáveis|Hybrid Runbook Worker|Você gerencia o sistema operacional e pode instalar o software.|
|Monitoramento de um arquivo ou uma pasta com um runbook|Hybrid Runbook Worker|Use uma [tarefa do Inspetor](automation-watchers-tutorial.md) em um Hybrid runbook Worker.|
|Executar um script com uso intensivo de recursos|Hybrid Runbook Worker| As áreas restritas do Azure têm [limitações nos recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Usar módulos com requisitos específicos| Hybrid Runbook Worker|Alguns exemplos são:</br> WinSCP-dependência em WinSCP. exe </br> IISAdministration-dependência na habilitação do IIS.|
|Instalar um módulo com um instalador|Hybrid Runbook Worker|Os módulos para área restrita devem dar suporte à cópia.|
|Usar runbooks ou módulos que exigem .NET Framework versão diferente do 4.7.2|Hybrid Runbook Worker|As áreas restritas de automação têm .NET Framework 4.7.2 e não há como atualizá-la.|
|Executar scripts que exigem elevação|Hybrid Runbook Worker|As áreas restritas não permitem elevação. Com um Hybrid Runbook Worker, você pode desativar o UAC e usar o **Invoke-Command** ao executar o comando que requer elevação.|
|Executar scripts que exigem acesso ao WMI|Hybrid Runbook Worker|Os trabalhos em execução em áreas restritas na nuvem não têm acesso ao WMI. |

## <a name="runbook-behavior"></a>Comportamento do runbook

### <a name="creating-resources"></a>Criar recursos

Se o runbook criar um recurso, o script deverá verificar se o recurso já existe antes de tentar criá-lo. Aqui está um exemplo básico.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Suporte a scripts dependentes de tempo

Seus runbooks devem ser robustos e capazes de lidar com erros transitórios que podem causar a reinicialização ou a falha. Se um runbook falhar, a automação do Azure tentará novamente.

Se seu runbook normalmente for executado dentro de uma restrição de tempo, faça com que o script implemente a lógica para verificar o tempo de execução. Essa verificação garante a execução de operações como inicialização, desligamento ou expansão somente durante horários específicos.

> [!NOTE]
> A hora local no processo de área restrita do Azure é definida como UTC. Os cálculos para data e hora em seus runbooks devem levar esse fato em consideração.

### <a name="tracking-progress"></a>Acompanhar o progresso

É uma boa prática criar seus runbooks para serem modulares por natureza, estruturando a lógica do runbook para que ela possa ser reutilizada e reiniciada com facilidade. Acompanhar o progresso em um runbook é uma boa maneira de garantir que a lógica do runbook seja executada corretamente se houver problemas. É possível acompanhar o progresso de um runbook usando uma fonte externa, como uma conta de armazenamento, um banco de dados ou arquivos compartilhados. Você pode criar lógica em seu runbook para verificar primeiro o estado da última ação executada. Em seguida, com base no resultado da verificação, a lógica pode ignorar ou continuar tarefas específicas no runbook.

### <a name="preventing-concurrent-jobs"></a>Impedindo trabalhos simultâneos

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

### <a name="working-with-multiple-subscriptions"></a>Trabalhando com várias assinaturas

Para lidar com várias assinaturas, seu runbook deve usar o cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) para garantir que o contexto de autenticação não seja recuperado de outro runbook em execução na mesma área restrita. O runbook também usa o parâmetro`AzContext` nos cmdlets do módulo AZ e passa o contexto apropriado.

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

### <a name="handling-exceptions"></a>Tratamento de exceções

Esta seção descreve algumas maneiras de lidar com exceções ou problemas intermitentes em seus runbooks.

#### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro de não finalização. Os erros de encerramento sempre são encerrados e não são afetados pelo *ErrorActionPreference*.

Quando o runbook usa `ErrorActionPreference`, um erro normalmente não conclusivo, como **PathNotFound** , do cmdlet `Get-ChildItem`, interrompe a conclusão do runbook. O exemplo a seguir mostra o uso de `ErrorActionPreference`. O comando final `Write-Output` nunca é executado, pois o script é interrompido.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Tentar capturar finalmente

[Tentar catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é usado em scripts do PowerShell para lidar com erros de encerramento. O script pode usar esse mecanismo para capturar exceções específicas ou exceções gerais. A instrução `catch` deve ser usada para rastrear ou tentar lidar com erros. O exemplo a seguir tenta baixar um arquivo que não existe. Ele captura a exceção `System.Net.WebException` e retorna o último valor para qualquer outra exceção.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de encerramento. Esse mecanismo pode ser útil ao definir sua própria lógica em um runbook. Se o script atender a um critério que deve parar, ele poderá usar a instrução `throw` para parar. O exemplo a seguir usa essa instrução para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Usar executáveis ou chamar processos

Os Runbooks que são executados em áreas restritas do Azure não oferecem suporte a processos de chamada, como executáveis (arquivos **. exe** ) ou subprocessos.  O motivo disso é que uma área restrita do Azure é um processo compartilhado executado em um contêiner que pode não ter acesso a todas as APIs subjacentes. Para cenários que exigem software de terceiros ou chamadas para subprocessos, é recomendável executar um runbook em um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Acessando características do dispositivo e do aplicativo

Os trabalhos de runbook que são executados em áreas restritas do Azure não têm acesso a nenhuma característica de dispositivo ou aplicativo. A API mais comum usada para consultar as métricas de desempenho no Windows é o WMI, com algumas das métricas comuns de uso de memória e CPU. No entanto, não importa qual API é usada, pois os trabalhos em execução na nuvem não têm acesso ao Microsoft implementation of Web-Based Enterprise Management (WBEM). Essa plataforma se baseia no modelo CIM (CIM), fornecendo os padrões do setor para definir características de dispositivo e aplicativo.

## <a name="handling-errors"></a>Tratando erros

Seus runbooks devem ser capazes de lidar com erros. O PowerShell tem dois tipos de erros, encerrando e não encerrando. Os erros de encerramento param a execução do runbook quando eles ocorrem. O runbook pára com um status de trabalho de falha.

Os erros de não finalização permitem que um script continue mesmo depois que eles ocorrerem. Um exemplo de um erro de não finalização é aquele que ocorre quando um runbook usa o cmdlet `Get-ChildItem` com um caminho que não existe. O PowerShell vê que o caminho não existe, gera um erro e continua até a próxima pasta. O erro nesse caso não define o status do status do trabalho de runbook como Failed e o trabalho pode até ser concluído. Para forçar um runbook a parar se houver um erro sem finalização, você pode usar `-ErrorAction Stop` no cmdlet.

## <a name="handling-jobs"></a>Manipulando trabalhos

Você pode reutilizar o ambiente de execução para trabalhos da mesma conta de automação. Um único runbook pode ter muitos trabalhos em execução ao mesmo tempo. Quanto mais trabalhos você executar ao mesmo tempo, mais frequentemente eles poderão ser enviados à mesma área restrita.

Os trabalhos em execução no mesmo processo de área restrita podem afetar os outros. Um exemplo é a execução do cmdlet `Disconnect-AzAccount`. A execução desse cmdlet desconecta cada trabalho de runbook no processo de área restrita compartilhada.

Os trabalhos do PowerShell iniciados a partir de um runbook que é executado em uma área restrita do Azure podem não ser executados no modo de linguagem completa. Para saber mais sobre os modos de linguagem do PowerShell, confira [modos de linguagem do PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Para obter detalhes adicionais sobre como interagir com trabalhos na automação do Azure, consulte [recuperando o status do trabalho com o PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Status de trabalho

A tabela a seguir descreve os status que são possíveis para um trabalho.

| Status | DESCRIÇÃO |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Falhou |Falha na compilação de um runbook de fluxo de trabalho gráfico ou do PowerShell. Falha ao iniciar um runbook de script do PowerShell ou o trabalho tinha uma exceção. Consulte [tipos de runbook de automação do Azure](automation-runbook-types.md).|
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fair-share) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando que os recursos em um trabalho de automação fiquem disponíveis para que possam ser iniciados. |
| Iniciando |O trabalho foi atribuído a um trabalhador e o sistema está iniciando-o. |
| Continuando |O sistema está retomando o trabalho depois que ele ter sido suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado porque atingiu o limite de compartilhamento justo. Ele continuará em breve a partir de seu último ponto de verificação. |
| Parado |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está parando o trabalho. |
| Suspenso |Aplica-se somente a [runbooks de fluxo de trabalho gráfico e do PowerShell](automation-runbook-types.md) . O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Se um runbook não tiver um ponto de verificação, ele começará do início. Se ele tiver um ponto de verificação, poderá iniciar novamente e retomar no último ponto de verificação. O sistema suspende apenas o runbook quando ocorre uma exceção. Por padrão, a variável `ErrorActionPreference` é definida como continuar, indicando que o trabalho continua em execução em caso de erro. Se a variável de preferência for definida como parar, o trabalho será suspenso em um erro.  |
| Suspensão |Aplica-se somente a [runbooks de fluxo de trabalho gráfico e do PowerShell](automation-runbook-types.md) . O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já tiver passado seu último ponto de verificação, ele será concluído antes que possa ser suspenso. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure

Você pode exibir um status resumido de todos os trabalhos do runbook ou analisar os detalhes de um trabalho específico do runbook no portal do Azure. Você também pode configurar a integração com o seu espaço de trabalho do Log Analytics a fim de encaminhar fluxos de trabalho e status do trabalho do runbook. Para obter mais informações sobre como integrar com logs de Azure Monitor, consulte [encaminhar status do trabalho e fluxos de trabalho de automação para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).

À direita de sua conta de automação selecionada, você pode ver um resumo de todos os trabalhos de runbook no bloco **Estatísticas de trabalho** .

![Bloco Estatísticas de Trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Esse bloco exibe uma contagem e representação gráfica do status do trabalho para cada trabalho executado.

Clicar no bloco apresenta a página trabalhos, que inclui uma lista resumida de todos os trabalhos executados. Esta página mostra o status, o nome do runbook, a hora de início e o tempo de conclusão de cada trabalho.

![Página Trabalhos da conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos**. Filtre um runbook específico, o status do trabalho ou uma opção na lista suspensa e forneça o intervalo de tempo para a pesquisa.

![Filtrar status do trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na página Runbooks em sua conta de automação e, em seguida, selecionando o bloco **trabalhos** . Essa ação apresenta a página trabalhos. Aqui, você pode clicar no registro de trabalho para exibir seus detalhes e saída.

![Página Trabalhos da conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Exibindo o resumo do trabalho

O resumo do trabalho descrito acima permite que você examine uma lista de todos os trabalhos que foram criados para um determinado runbook e seu status mais recente. Para ver informações detalhadas e a saída de um trabalho, clique em seu nome na lista. A exibição detalhada do trabalho inclui os valores para os parâmetros de runbook que foram fornecidos para esse trabalho.

Você pode usar as etapas a seguir para exibir os trabalhos de um runbook.

1. No Portal do Azure, selecione **Automação** e, em seguida, selecione no nome de uma Conta de automação.
2. No Hub, selecione **Runbooks** em **automação de processo**.
3. Na página Runbooks, selecione um runbook na lista.
3. Na página do runbook selecionado, clique no bloco **Trabalhos**.
4. Clique em um dos trabalhos na lista e exiba seus detalhes e saída na página de detalhes do trabalho de runbook.

### <a name="retrieving-job-status-using-powershell"></a>Recuperando o status do trabalho usando o PowerShell

Use o cmdlet `Get-AzAutomationJob` para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você iniciar um runbook com o PowerShell usando `Start-AzAutomationRunbook`, ele retornará o trabalho resultante. Use [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) para recuperar a saída do trabalho.

O exemplo a seguir obtém o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída de um trabalho específico e retorna cada registro. Se houver uma exceção para um dos registros, o script gravará a exceção em vez do valor. Esse comportamento é útil, pois as exceções podem fornecer informações adicionais que talvez não sejam registradas normalmente durante a saída.

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

## <a name="getting-details-from-the-activity-log"></a>Obtendo detalhes do log de atividades

Você pode recuperar detalhes do runbook, como a pessoa ou a conta que iniciou o runbook, do log de atividades da conta de automação. O exemplo do PowerShell a seguir fornece o último usuário para executar o runbook especificado.

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

## <a name="fair-share"></a>Compartilhando recursos entre runbooks

Para compartilhar recursos entre todos os runbooks na nuvem, a automação do Azure descarrega temporariamente ou interrompe qualquer trabalho que tenha sido executado por mais de três horas. Os trabalhos para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e Runbooks do [Python](automation-runbook-types.md#python-runbooks) são interrompidos e não reiniciados e o status do trabalho se torna parado.

Para tarefas de execução longa, é recomendável usar uma Hybrid Runbook Worker. Os Hybrid Runbook Workers não são limitados por fração justa e não limitam o tempo de execução de um runbook. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) do trabalho se aplicam a áreas restritas do Azure e ao Hybrid Runbook Workers. Embora Hybrid runbook Workers não sejam limitados pelo limite de compartilhamento justo de 3 horas, você deve desenvolver runbooks para executar em trabalhos que dão suporte a reinicializações de problemas de infraestrutura local inesperados.

Outra opção é otimizar um runbook usando runbooks filho. Por exemplo, seu runbook pode executar um loop por meio da mesma função em vários recursos, como uma operação de banco de dados em vários bancos. Você pode mover essa função para um [runbook filho](automation-child-runbooks.md) e fazer com que seu runbook a chame usando `Start-AzAutomationRunbook`. Os runbooks filho são executados em paralelo em processos separados.

O uso de runbooks filho diminui a quantidade total de tempo que o runbook pai deve concluir. O runbook pode usar o cmdlet `Get-AzAutomationJob` para verificar o status do trabalho para um runbook filho se ele ainda tiver operações a serem executadas após a conclusão do filho.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os métodos que podem ser usados para iniciar um runbook na automação do Azure, consulte [iniciando um runbook na automação do Azure](automation-starting-a-runbook.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
