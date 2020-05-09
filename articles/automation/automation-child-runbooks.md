---
title: Runbooks filhos na Automação do Azure
description: Descreve os diferentes métodos para iniciar um runbook na Automação do Azure de outro runbook e compartilhar informações entre eles.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 21dc14362fed2abf80c2c5ecf57f688541c9c639
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994789"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks filhos na Automação do Azure

É uma prática recomendada na automação do Azure escrever runbooks reutilizáveis e modulares com uma função discreta que é chamada por outros runbooks. Um runbook pai geralmente chama um ou mais runbooks filho para executar a funcionalidade necessária. Há duas maneiras de chamar um runbook filho, e há diferenças distintas que você deve entender para poder determinar qual é a melhor para seus cenários.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook filho usando a execução embutida

Para invocar um runbook embutido de outro runbook, use o nome do runbook e forneça valores para seus parâmetros, assim como você usaria uma atividade ou um cmdlet. Todos os runbooks na mesma conta de automação estão disponíveis para todos os outros para serem usados dessa maneira. O runbook pai aguarda que o runbook filho seja concluído antes de passar para a próxima linha e qualquer saída retorna diretamente para o pai.

Quando você chama um runbook embutido, ele é executado no mesmo trabalho que o runbook pai. Não há nenhuma indicação no histórico de trabalhos do runbook filho. Quaisquer exceções e saídas de fluxo do runbook filho são associadas ao pai. Esse comportamento resulta em menos trabalhos e torna mais fácil controlar e solucionar problemas.

Quando um runbook é publicado, todos os runbooks filhos que ele chamar já devem ter sido publicados. O motivo é que a automação do Azure cria uma associação com quaisquer runbooks filho ao compilar um runbook. Se os runbooks filho ainda não tiverem sido publicados, o runbook pai parecerá publicar corretamente, mas gerará uma exceção quando ele for iniciado. Se isso acontecer, você poderá publicar o runbook pai novamente para referenciar de forma correta os runbooks filho. Você não precisará republicar o runbook pai se algum runbook filho for alterado porque a associação já foi criada.

Os parâmetros de um runbook filho chamado Inline podem ser de qualquer tipo de dados, incluindo objetos complexos. Não há [serialização JSON](start-runbooks.md#runbook-parameters), pois há quando você inicia o runbook usando o portal do Azure ou com o cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Tipos de runbook

Quais tipos de runbook podem chamar uns aos outros?

* Um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) e um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) podem chamar entre si embutidos, pois ambos são baseados no PowerShell.
* Um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e um runbook de fluxo de trabalho gráfico do PowerShell podem chamar uns aos outros, pois ambos são baseados no fluxo de trabalho do PowerShell.
* Os tipos do PowerShell e os tipos de fluxo de trabalho do PowerShell não podem chamar uns `Start-AzAutomationRunbook`aos outros embutidos e devem usar.

Quando a ordem de publicação é importante?

A ordem de publicação de runbooks só é importante para o fluxo de trabalho do PowerShell e runbooks gráficos de fluxo de trabalho do PowerShell.

Quando o runbook chama um runbook filho de fluxo de trabalho gráfico ou do PowerShell usando a execução embutida, ele usa o nome do runbook. O nome deve começar com `.\\` para especificar que o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho de teste que aceita um objeto complexo, um valor inteiro e um valor booliano. A saída do runbook filho é atribuída a uma variável. Nesse caso, o runbook filho é um runbook de Fluxo de Trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Aqui está o mesmo exemplo usando um runbook do PowerShell como o filho.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Iniciando um runbook filho usando um cmdlet

> [!IMPORTANT]
> Se o seu runbook invocar um runbook filho com `Start-AzAutomationRunbook` o cmdlet com `Wait` o parâmetro e o runbook filho produzir um resultado de objeto, a operação poderá encontrar um erro. Para contornar o erro, consulte [Runbooks filho com a saída do objeto](troubleshoot/runbooks.md#child-runbook-object) para saber como implementar a lógica para sondar os resultados usando o cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Você pode usar `Start-AzAutomationRunbook` o para iniciar um runbook, conforme descrito em [para iniciar um runbook com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Há dois modos de uso para esse cmdlet. Em um modo, o cmdlet retorna a ID do trabalho quando o trabalho é criado para o runbook filho. No outro modo, que o script permite especificando o parâmetro *Wait* , o cmdlet aguarda até que o trabalho filho seja concluído e retorna a saída do runbook filho.

O trabalho de um runbook filho iniciado com um cmdlet é executado separadamente do trabalho de runbook pai. Esse comportamento resulta em mais trabalhos do que iniciar o runbook embutido e torna os trabalhos mais difíceis de rastrear. O pai pode iniciar mais de um runbook filho de forma assíncrona sem aguardar a conclusão de cada um. Para essa execução paralela chamando os runbooks filho embutidos, o runbook pai deve usar a [palavra-chave Parallel](automation-powershell-workflow.md#parallel-processing).

A saída do runbook filho não retorna ao runbook pai de forma confiável devido ao tempo. Além disso, variáveis como `$VerbosePreference`, `$WarningPreference`e outras, podem não ser propagadas para os runbooks filho. Para evitar esses problemas, você pode iniciar os runbooks filho como trabalhos de automação separados `Start-AzAutomationRunbook` usando o `Wait` com o parâmetro. Essa técnica bloqueia o runbook pai até que o runbook filho seja concluído.

Se você não quiser que o runbook pai seja bloqueado na espera, você pode iniciar o runbook filho usando `Start-AzAutomationRunbook` sem o `Wait` parâmetro. Nesse caso, o runbook deve usar [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para aguardar a conclusão do trabalho. Ele também deve usar [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) para recuperar os resultados.

Os parâmetros de um runbook filho iniciados com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [parâmetros de runbook](start-runbooks.md#runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexos, ele deve ser chamado embutido.

O contexto da assinatura pode ser perdido ao iniciar runbooks filho como trabalhos separados. Para que o runbook filho execute cmdlets de módulo AZ em uma assinatura específica do Azure, o filho deve se autenticar nessa assinatura independentemente do runbook pai.

Se os trabalhos dentro da mesma conta de automação funcionarem com mais de uma assinatura, selecionar uma assinatura em um trabalho poderá alterar o contexto de assinatura selecionado no momento para outros trabalhos. Para evitar essa situação, use `Disable-AzContextAutosave –Scope Process` no início de cada runbook. Essa ação apenas salva o contexto nessa execução de runbook.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho com parâmetros e, em seguida, aguarda que ele seja `Start-AzAutomationRunbook` concluído usando o `Wait` cmdlet com o parâmetro. Depois de concluído, o exemplo coleta a saída do cmdlet do runbook filho. Para usar `Start-AzAutomationRunbook`o, o script deve se autenticar na sua assinatura do Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook filho

A tabela a seguir resume as diferenças entre as duas maneiras de chamar um runbook de outro runbook.

|  | Embutido | Cmdlet |
|:--- |:--- |:--- |
| Trabalho |Os runbooks filhos são executados no mesmo trabalho que o pai. |Um trabalho separado é criado para o runbook filho. |
| Execução |O runbook pai aguarda a conclusão do runbook filho antes de continuar. |O runbook pai continua imediatamente após o runbook filho ser iniciado *ou* o runbook pai aguarda a conclusão do trabalho filho. |
| Saída |O runbook pai pode obter saída diretamente do runbook filho. |O runbook pai deve recuperar a saída do trabalho do runbook filho *ou* o runbook pai pode obter a saída diretamente do runbook filho. |
| Parâmetros |Os valores para os parâmetros de runbook filho são especificados separadamente e podem usar qualquer tipo de dados. |Os valores para os parâmetros do runbook filho devem ser combinados em uma única tabela de hash. Essa tabela de hash pode apenas incluir tipos de dados simples, de matriz e de objeto que usam a serialização JSON. |
| Conta de Automação |O runbook pai só pode usar o runbook filho na mesma conta de automação. |Os runbooks pai podem usar um runbook filho de qualquer conta de automação, da mesma assinatura do Azure e até mesmo de uma assinatura diferente à qual você tem uma conexão. |
| Publicação |O runbook filho deve ser publicado antes da publicação do runbook pai. |O runbook filho é publicado a qualquer momento antes do runbook pai ser iniciado. |

## <a name="next-steps"></a>Próximas etapas

* [Como iniciar um Runbook na Automação do Azure](start-runbooks.md)
* [Saída e mensagens do runbook na automação do Azure](automation-runbook-output-and-messages.md)