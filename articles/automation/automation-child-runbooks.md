---
title: Runbooks filhos na Automação do Azure
description: Descreve os diferentes métodos para iniciar um runbook na Automação do Azure de outro runbook e compartilhar informações entre eles.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367356"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks filhos na Automação do Azure

É uma prática recomendada no Azure Automation para escrever runbooks modulares reutilizáveis com uma função discreta que é chamada por outros runbooks. Um runbook pai geralmente chama um ou mais runbooks filho para executar a funcionalidade necessária. Existem duas maneiras de chamar um livro de jogadas infantil, e há diferenças distintas que você deve entender para ser capaz de determinar qual é o melhor para seus cenários.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook filho usando a execução embutida

Para invocar um runbook em linha de outro runbook, use o nome do runbook e forneça valores para seus parâmetros, assim como você usaria uma atividade ou um cmdlet. Todos os runbooks na mesma conta de automação estão disponíveis para todos os outros para serem usados dessa maneira. O runbook pai espera que o manual do filho seja concluído antes de passar para a próxima linha, e qualquer saída retorna diretamente para o pai.

Quando você chama um runbook embutido, ele é executado no mesmo trabalho que o runbook pai. Não há nenhuma indicação no histórico de trabalho do livro infantil. Quaisquer exceções e quaisquer saídas de fluxo do manual filho estão associadas ao pai. Esse comportamento resulta em menos empregos e torna-os mais fáceis de rastrear e solucionar problemas.

Quando um runbook é publicado, todos os runbooks filhos que ele chamar já devem ter sido publicados. A razão é que a Azure Automation constrói uma associação com quaisquer livros de execução infantil quando compila um runbook. Se os livros de execução de crianças ainda não foram publicados, o runbook dos pais parece ser publicado corretamente, mas gera uma exceção quando é iniciado. Se isso acontecer, você poderá publicar o runbook pai novamente para referenciar de forma correta os runbooks filho. Você não precisa republicar o manual dos pais se algum livro de execução de crianças for alterado porque a associação já foi criada.

Os parâmetros de um livro infantil chamado inline podem ser de qualquer tipo de dados, incluindo objetos complexos. Não há [serialização json,](start-runbooks.md#runbook-parameters)como há quando você inicia o runbook usando o portal Azure ou com o [cmdlet Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Tipos de runbook

Quais tipos de runbook podem se chamar?

* Um [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) e um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) podem se chamar de inline, já que ambos são baseados em PowerShell.
* Um [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) e um runbook gráfico PowerShell Workflow podem se chamar de inline, já que ambos são baseados no PowerShell Workflow.
* Os tipos PowerShell e PowerShell Workflow não podem se chamar `Start-AzAutomationRunbook`inline e devem usar .

Quando publicar a ordem importa?

A ordem de publicação de runbooks só importa para powershell workflow e gráficopowershell workflow runbooks.

Quando o seu runbook chama um manual de criança gráfico ou PowerShell Workflow usando execução inline, ele usa o nome do runbook. O nome deve `.\\` começar para especificar se o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um manual infantil de teste que aceita um objeto complexo, um valor inteiro e um valor booleano. A saída do runbook filho é atribuída a uma variável. Nesse caso, o runbook filho é um runbook de Fluxo de Trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Aqui está o mesmo exemplo usando um runbook PowerShell como a criança.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Iniciar um manual infantil usando um cmdlet

> [!IMPORTANT]
> Se o seu manual de execução `Start-AzAutomationRunbook` invocar um `Wait` manual infantil com o cmdlet com o parâmetro e o manual do filho produzir um resultado de objeto, a operação pode encontrar um erro. Para contornar o erro, consulte [livros de execução do Child com saída de objeto](troubleshoot/runbooks.md#child-runbook-object) para aprender a implementar a lógica para votar os resultados usando o cmdlet [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Você pode `Start-AzAutomationRunbook` usar para iniciar um runbook conforme descrito em [Para iniciar um runbook com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Há dois modos de uso para esse cmdlet. Em um modo, o cmdlet retorna o ID de trabalho quando o trabalho é criado para o manual do filho. No outro modo, que o script permite especificando o parâmetro *Wait,* o cmdlet espera até que o trabalho filho termine e retorna a saída do manual do filho.

O trabalho de um livro de crianças começou com um cmdlet executado separadamente do trabalho de runbook dos pais. Esse comportamento resulta em mais empregos do que iniciar o runbook inline, e torna os trabalhos mais difíceis de rastrear. Os pais podem iniciar mais de um livro de execução de crianças de forma assíncrona sem esperar que cada um seja concluído. Para esta execução paralela chamando os livros de execução infantil inline, o runbook pai deve usar a [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

A saída do livro de execução de crianças não retorna ao manual dos pais de forma confiável devido ao tempo. Além disso, variáveis `$VerbosePreference`como `$WarningPreference`, e outras podem não ser propagadas para os livros infantis. Para evitar esses problemas, você pode iniciar os `Start-AzAutomationRunbook` livros `Wait` de execução infantil como trabalhos separados de Automação usando com o parâmetro. Esta técnica bloqueia o manual dos pais até que o manual da criança esteja completo.

Se você não quiser que o manual dos pais seja bloqueado na espera, você pode iniciar o manual do filho usando `Start-AzAutomationRunbook` sem o `Wait` parâmetro. Neste caso, seu manual deve usar [o Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para aguardar a conclusão do trabalho. Ele também deve usar [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) para recuperar os resultados.

Os parâmetros para um manual infantil iniciado com um cmdlet são fornecidos como um hashtable, conforme descrito nos [parâmetros do Runbook](start-runbooks.md#runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexos, ele deve ser chamado embutido.

O contexto da assinatura pode ser perdido ao iniciar runbooks filho como trabalhos separados. Para que o manual filho execute cmdlets do módulo Az contra uma assinatura específica do Azure, a criança deve autenticar esta assinatura independentemente do manual dos pais.

Se os trabalhos dentro da mesma conta de Automação funcionarem com mais de uma assinatura, selecionar uma assinatura em um trabalho pode alterar o contexto de assinatura atualmente selecionado para outros trabalhos. Para evitar essa `Disable-AzContextAutosave –Scope Process` situação, use no início de cada runbook. Essa ação apenas salva o contexto nessa execução de runbook.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um livro de execução infantil `Start-AzAutomationRunbook` com parâmetros `Wait` e, em seguida, espera que ele seja concluído usando o cmdlet com o parâmetro. Uma vez concluído, o exemplo coleta a saída de cmdlet do manual infantil. Para `Start-AzAutomationRunbook`usar, o script deve autenticar a sua assinatura do Azure.

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
| Conta de Automação |O runbook dos pais só pode usar o manual do filho na mesma conta de Automação. |Os runbooks dos pais podem usar um manual de crianças de qualquer conta de Automação, da mesma assinatura do Azure e até mesmo de uma assinatura diferente à qual você tem uma conexão. |
| Publicação |O runbook filho deve ser publicado antes da publicação do runbook pai. |O livro de execução de crianças é publicado a qualquer momento antes do runbook dos pais ser iniciado. |

## <a name="next-steps"></a>Próximas etapas

* [Como iniciar um Runbook na Automação do Azure](start-runbooks.md)
* [Saída de runbook e mensagens no Azure Automation](automation-runbook-output-and-messages.md)