---
title: Execução de runbook na Automação do Azure
description: Descreve os detalhes de como um runbook na Automação do Azure é processado.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: a7dd9de1f2ae41b20d94cf31de48e92fbb71ca6a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405634"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure

A automação de processos no Azure Automation permite criar e gerenciar powershell, powershell workflow e runbooks gráficos. Para obter detalhes, consulte [os runbooks da Azure Automation](automation-runbook-types.md). 

A automação executa seus runbooks com base na lógica definida dentro deles. Se um runbook é interrompido, ele reinicia no início. Esse comportamento exige que você escreva runbooks que suportam ser reiniciados se ocorrerem problemas transitórios.

Iniciar um runbook no Azure Automation cria um trabalho, que é uma única instância de execução do runbook. Cada trabalho acessa os recursos do Azure fazendo uma conexão com sua assinatura do Azure. O trabalho só pode acessar recursos em seu data center se esses recursos estiverem acessíveis a partir da nuvem pública.

A Azure Automation designa um trabalhador para executar cada trabalho durante a execução do runbook. Enquanto os trabalhadores são compartilhados por muitas contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não pode controlar qual trabalhador atende seus pedidos de trabalho.

Quando você vê a lista de runbooks no portal Azure, ele mostra o status de cada trabalho que foi iniciado para cada runbook. A Azure Automation armazena registros de trabalho por um máximo de 30 dias.

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks PowerShell,](automation-runbook-types.md#powershell-runbooks) [runbooks powershell workflow](automation-runbook-types.md#powershell-workflow-runbooks)e [runbooks gráficos](automation-runbook-types.md#graphical-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Onde executar seus runbooks

Os runbooks no Azure Automation podem ser executados em uma caixa de areia Azure ou em um [Trabalhador de Runbook Híbrido](automation-hybrid-runbook-worker.md). Quando os runbooks são projetados para autenticar e executar contra recursos no Azure, eles são executados em uma caixa de areia Azure, que é um ambiente compartilhado que vários trabalhos podem usar. Os trabalhos que usam a mesma área restrita são restringidos pelas limitações de recurso da área restrita.

>[!NOTE]
>O ambiente de caixa de areia Azure não suporta operações interativas. Ele também requer o uso de arquivos MOF locais para runbooks que fazem chamadas Win32.

Você pode usar um Hybrid Runbook Worker para executar runbooks diretamente no computador que hospeda a função e contra os recursos locais no ambiente. A Azure Automation armazena e gerencia runbooks e, em seguida, os entrega a um ou mais computadores atribuídos.

A tabela a seguir lista algumas tarefas de execução de runbook com o ambiente de execução recomendado listado para cada um.

|Tarefa|Recomendação|Observações|
|---|---|---|
|Integração com serviços do Azure|Área restrita do Azure|Hospedado no Azure, a autenticação é mais simples. Se você estiver usando um Trabalhador de Runbook Híbrido em uma VM Azure, você pode usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Obtenha o desempenho ideal para gerenciar os recursos do Azure|Área restrita do Azure|O script é executado no mesmo ambiente, que tem menos latência.|
|Redução de custos operacionais|Área restrita do Azure|Não há sobrecarga de cálculo e não há necessidade de uma VM.|
|Execute o script de longa duração|Hybrid Runbook Worker|As caixas de areia do Azure têm [limites de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interaja com serviços locais|Hybrid Runbook Worker|Pode acessar diretamente a máquina host, ou recursos em outros ambientes em nuvem, ou em seu ambiente local. |
|Exigir software de terceiros e executáveis|Hybrid Runbook Worker|Você gerencia o sistema operacional e pode instalar o software.|
|Monitoramento de um arquivo ou uma pasta com um runbook|Hybrid Runbook Worker|Use uma [tarefa watcher](automation-watchers-tutorial.md) em um trabalhador híbrido de runbook.|
|Execute um script com uso intensivo de recursos|Hybrid Runbook Worker| As caixas de areia do Azure têm [limites de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Use módulos com requisitos específicos| Hybrid Runbook Worker|Alguns exemplos são:</br> WinSCP: dependência de winscp.exe </br> Administração IIS - dependência de habilitação ou gerenciamento do IIS.|
|Instale um módulo com um instalador|Hybrid Runbook Worker|Os módulos para caixa de areia devem suportar a cópia.|
|Use runbooks ou módulos que requerem a versão .NET Framework diferente de 4.7.2|Hybrid Runbook Worker|O suporte a caixas de areia de automação .NET Framework 4.7.2 e a atualização para uma versão diferente não é suportada.|
|Executar scripts que requerem elevação|Hybrid Runbook Worker|Caixas de areia não permitem elevação. Com um Trabalhador de Runbook Híbrido, você pode desligar o UAC e usar [o Comando Invocar](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) ao executar o comando que requer elevação.|
|Executar scripts que requerem acesso ao WMI (Windows Management Instrumentation, instrumentação de gerenciamento do Windows)|Hybrid Runbook Worker|Empregos em caixas de areia na nuvem não podem acessar o provedor WMI. |

## <a name="runbook-behavior"></a>Comportamento do runbook

### <a name="creating-resources"></a>Criar recursos

Se o seu runbook criar um recurso, o script deve verificar se o recurso já existe antes de tentar criá-lo. Aqui está um exemplo básico.

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

### <a name="supporting-time-dependent-scripts"></a>Suporte a scripts dependentes de tempo

Seus runbooks devem ser robustos e capazes de lidar com erros transitórios que podem fazê-los reiniciar ou falhar. Se um runbook falhar, a Azure Automation o reina.

Se o seu runbook normalmente for executado dentro de uma restrição de tempo, faça com que o script implemente a lógica para verificar o tempo de execução. Essa verificação garante o funcionamento de operações como inicialização, desligamento ou escala somente em horários específicos.

> [!NOTE]
> A hora local no processo de caixa de areia do Azure está definida como UTC. Os cálculos de data e hora em seus livros de execução devem levar esse fato em consideração.

### <a name="tracking-progress"></a>Acompanhar o progresso

É uma boa prática escrever seus livros de execução para serem modulares na natureza, com lógica que pode ser reutilizada e reiniciada facilmente. Acompanhar o progresso em um runbook é uma boa maneira de garantir que a lógica do runbook seja executada corretamente se houver problemas. É possível rastrear o progresso de um runbook usando uma fonte externa, como uma conta de armazenamento, um banco de dados ou arquivos compartilhados. Você pode criar lógica em seu manual para primeiro verificar o estado da última ação tomada. Então, com base no resultado da verificação, a lógica pode pular ou continuar tarefas específicas no manual.

### <a name="preventing-concurrent-jobs"></a>Prevenção de empregos simultâneos

Alguns runbooks se comportam estranhamente se eles se deparam com vários trabalhos ao mesmo tempo. Neste caso, é importante que um runbook implemente a lógica para determinar se já existe um trabalho em execução. Aqui está um exemplo básico.

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

Para lidar com várias assinaturas, seu manual deve usar o cmdlet [Disable-AzContextAutosave.](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) Este cmdlet garante que o contexto de autenticação não seja recuperado de outro runbook em execução na mesma caixa de areia. O runbook também`AzContext` usa o parâmetro nos cmdlets do módulo Az e passa-o pelo contexto adequado.

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

Esta seção descreve algumas maneiras de lidar com exceções ou problemas intermitentes em seus livros de execução.

#### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro não-terminativo. Erros de terminação sempre terminam `ErrorActionPreference`e não são afetados por .

Quando o runbook usa `ErrorActionPreference`, um erro `PathNotFound` normalmente não terminante, como no cmdlet [Get-ChildItem,](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) impede que o runbook seja concluído. O exemplo a seguir demonstra o uso de `ErrorActionPreference`. O comando final [Write-Output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) nunca é executado, pois o script pára.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Tente pegar finalmente

[Tente pegar Finalmente](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é usado em scripts PowerShell para lidar com erros de terminação. O script pode usar este mecanismo para capturar exceções específicas ou exceções gerais. A `catch` declaração deve ser usada para rastrear ou tentar lidar com erros. O exemplo a seguir tenta baixar um arquivo que não existe. Ele pega `System.Net.WebException` a exceção e retorna o último valor para qualquer outra exceção.

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

[O lançamento](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de terminação. Este mecanismo pode ser útil ao definir sua própria lógica em um runbook. Se o script atender a um critério que `throw` deve pará-lo, ele pode usar a declaração para parar. O exemplo a seguir usa esta declaração para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Usar executáveis ou chamar processos

Os runbooks que são executados em caixas de areia do Azure não suportam processos de chamada, como executáveis (arquivos **.exe)** ou subprocessos. A razão para isso é que uma caixa de areia Azure é um processo compartilhado executado em um contêiner que pode não ser capaz de acessar todas as APIs subjacentes. Para cenários que exijam software de terceiros ou chamadas para subprocessos, você deve executar um runbook em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Acessando características de dispositivos e aplicativos

Os trabalhos de runbook que são executados em caixas de areia do Azure não podem acessar nenhuma característica de dispositivo ou aplicativo. A API mais comum usada para consultar métricas de desempenho no Windows é o WMI, com algumas das métricas comuns sendo o uso de memória e CPU. No entanto, não importa qual API seja usada, já que os empregos em execução na nuvem não podem acessar a implementação da Microsoft do Web-Based Enterprise Management (WBEM). Esta plataforma é construída sobre o Common Information Model (CIM), fornecendo os padrões do setor para definir características de dispositivos e aplicativos.

## <a name="handling-errors"></a>Tratando erros

Seus runbooks devem ser capazes de lidar com erros. O PowerShell tem dois tipos de erros, terminando e não terminando. Erros de terminação interrompem a execução do runbook quando ocorrem. O runbook pára com um status de trabalho de Failed.

Erros não terminais permitem que um script continue mesmo após o ocorrido. Um exemplo de erro não-terminante é aquele que `Get-ChildItem` ocorre quando um runbook usa o cmdlet com um caminho que não existe. O PowerShell vê que o caminho não existe, gera um erro e continua até a próxima pasta. O erro neste caso não define o status do trabalho de runbook como Falhou, e o trabalho pode até mesmo ser concluído. Para forçar um runbook a parar se houver um erro sem finalização, você pode usar `ErrorAction Stop` no cmdlet.

## <a name="handling-jobs"></a>Manuseio de trabalhos

Você pode reutilizar o ambiente de execução para trabalhos da mesma conta de Automação. Um único runbook pode ter muitos trabalhos em execução ao mesmo tempo. Quanto mais trabalhos você executar ao mesmo tempo, mais frequentemente eles poderão ser enviados à mesma área restrita.

Trabalhos executados no mesmo processo de caixa de areia podem afetar uns aos outros. Um exemplo é a execução do cmdlet [Disconnect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) A execução deste cmdlet desconecta cada trabalho de runbook no processo de caixa de areia compartilhada.

Os trabalhos do PowerShell iniciados a partir de um runbook que é executado em uma caixa de areia do Azure podem não ser executados no modo de [linguagem PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes)completo . Para obter mais informações sobre como interagir com trabalhos no Azure Automation, consulte [Recuperando o status do trabalho com o PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Status de trabalho

A tabela a seguir descreve os status que são possíveis para um trabalho.

| Status | Descrição |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Falhou |Um runbook gráfico ou PowerShell Workflow não foi compilado. Um manual de script do PowerShell não foi iniciante ou o trabalho teve uma exceção. Consulte [os tipos de runbook da Azure Automation](automation-runbook-types.md).|
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fair-share) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está à espera de recursos em um trabalhador de Automação para que ele possa ser iniciado. |
| Iniciando |O trabalho foi atribuído a um trabalhador e o sistema está iniciando-o. |
| Continuando |O sistema está retomando o trabalho depois que ele ter sido suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado, pois atingiu o limite de fração justa . Ele continuará em breve a partir de seu último ponto de verificação. |
| Parado |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está parando o trabalho. |
| Suspenso |Aplica-se apenas aos [runbooks gráficos e powershell workflow.](automation-runbook-types.md) O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Se um runbook não tem um ponto de verificação, começa desde o início. Se ele tiver um ponto de verificação, poderá iniciar novamente e retomar no último ponto de verificação. O sistema só suspende o manual quando ocorre uma exceção. Por padrão, `ErrorActionPreference` a variável é definida como Continuar, indicando que o trabalho continua sendo executado por um erro. Se a variável de preferência estiver definida como Stop, o trabalho será suspenso por um erro.  |
| Suspensão |Aplica-se apenas aos [runbooks gráficos e powershell workflow.](automation-runbook-types.md) O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já passou pelo seu último posto de controle, ele completa antes de ser suspenso. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure

Você pode exibir um resumo de status para todos os trabalhos de runbook ou detalhar detalhes de um trabalho específico de runbook no portal Azure. Você também pode configurar a integração com o seu espaço de trabalho do Log Analytics a fim de encaminhar fluxos de trabalho e status do trabalho do runbook. Para obter mais informações sobre a integração com os logs do Monitor do Azure, consulte [Avançar no status do trabalho e nos fluxos de trabalho dos registros de automação para o Monitor do Azure](automation-manage-send-joblogs-log-analytics.md).

À direita da sua conta de Automação selecionada, você pode ver um resumo de todos os trabalhos de runbook sob o bloco **Job Statistics.**

![Bloco Estatísticas de Trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Este azulejo exibe uma contagem e representação gráfica do status do trabalho para cada trabalho executado.

Clicar no bloco apresenta a página Trabalhos, que contém uma lista resumida de todos os trabalhos executados. Esta página mostra o status, o nome do runbook, o tempo de início e o tempo de conclusão de cada trabalho.

![Página Trabalhos da conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Você pode filtrar a lista de trabalhos selecionando **trabalhos de filtro**. Filtre em um manual específico, status do trabalho ou uma escolha da lista de parada e forneça o intervalo de tempo para a pesquisa.

![Filtrar o status do trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternativamente, você pode visualizar os detalhes do resumo do trabalho para um runbook específico selecionando esse runbook na página Runbooks em sua conta de Automação e, em seguida, selecionando o bloco **Empregos.** Esta ação apresenta a página Jobs. A partir daqui, você pode clicar no registro de trabalho para ver seus detalhes e saída.

![Página Trabalhos da conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Ver o resumo do trabalho

O resumo do trabalho descrito acima permite que você veja uma lista de todos os trabalhos que foram criados para um determinado runbook e seus status mais recentes. Para ver informações detalhadas e saída para um trabalho, clique em seu nome na lista. A visão detalhada do trabalho inclui os valores para os parâmetros de runbook que foram fornecidos para esse trabalho.

Você pode usar as etapas a seguir para exibir os trabalhos de um runbook.

1. No Portal do Azure, selecione **Automação** e, em seguida, selecione no nome de uma Conta de automação.
2. No hub, selecione **Runbooks** em **Automação de Processos**.
3. Na página Runbooks, selecione um runbook da lista.
3. Na página do runbook selecionado, clique no bloco **Trabalhos**.
4. Clique em um dos trabalhos da lista e veja seus detalhes e saída na página de detalhes do trabalho do runbook.

### <a name="retrieving-job-status-using-powershell"></a>Recuperando o status do trabalho usando o PowerShell

Use o [cmdlet Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) para recuperar os empregos criados para um runbook e os detalhes de um trabalho específico. Se você iniciar um runbook `Start-AzAutomationRunbook`com o Uso do PowerShell, ele reaverá o trabalho resultante. Use [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) para recuperar a saída de trabalho.

O exemplo a seguir obtém o último trabalho para um runbook de exemplo e exibe seu status, os valores fornecidos para os parâmetros do livro de execução e a saída de trabalho.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída para um trabalho específico e retorna cada registro. Se houver uma exceção para um dos registros, o script escreverá a exceção em vez do valor. Esse comportamento é útil, uma vez que as exceções podem fornecer informações adicionais que podem não ser registradas normalmente durante a saída.

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

## <a name="getting-details-from-the-activity-log"></a>Obtendo detalhes do registro de atividades

Você pode recuperar detalhes do runbook, como a pessoa ou conta que iniciou o manual, a partir do registro de atividades da conta Automação. O exemplo do PowerShell a seguir fornece o último usuário a executar o runbook especificado.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Compartilhamento de recursos entre runbooks

Para compartilhar recursos entre todos os runbooks na nuvem, a Azure Automation descarrega ou interrompe temporariamente qualquer trabalho que tenha sido executado por mais de três horas. Os [trabalhos para runbooks powershell](automation-runbook-types.md#powershell-runbooks) e [runbooks Python](automation-runbook-types.md#python-runbooks) são interrompidos e não reiniciados, e o status do trabalho torna-se interrompido.

Para tarefas de longa duração, recomenda-se usar um Trabalhador de Runbook Híbrido. Os Hybrid Runbook Workers não são limitados por fração justa e não limitam o tempo de execução de um runbook. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) do trabalho se aplicam a áreas restritas do Azure e ao Hybrid Runbook Workers. Embora os Trabalhadores do Runbook Híbrido não sejam limitados pelo limite de 3 horas de participação justa, você deve desenvolver runbooks para executar os trabalhadores que suportam reinicializações de problemas inesperados de infra-estrutura local.

Outra opção é otimizar um runbook usando livros de execução infantil. Por exemplo, seu runbook pode fazer loop através da mesma função em vários recursos, como uma operação de banco de dados em vários bancos de dados. Você pode mover essa função para um [manual infantil](automation-child-runbooks.md) e fazer com que seu manual de execução o chame usando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Os livros de execução infantil são executados em paralelo em processos separados.

O uso de livros de execução infantil diminui o tempo total para que o manual dos pais seja concluído. Seu runbook pode usar o [cmdlet Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) para verificar o status do trabalho de um livro de crianças se ele ainda tiver mais operações após a criança ser concluída.

## <a name="next-steps"></a>Próximas etapas

* Para saber como trabalhar com um runbook, consulte [Gerenciar runbooks no Azure Automation](manage-runbooks.md).
* Para saber mais sobre os métodos que podem ser usados para iniciar um runbook no Azure Automation, consulte [Iniciar um runbook no Azure Automation](automation-starting-a-runbook.md).
* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
