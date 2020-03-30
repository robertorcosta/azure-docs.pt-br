---
title: Gerenciar runbooks na Automação do Azure
description: Este artigo descreve como gerenciar os runbooks na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad8c05b3347ed4741d574a5e6bcc1d928db08411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366829"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerenciar runbooks na Automação do Azure

Você pode adicionar um runbook ao Azure Automation [criando um novo](#create-a-runbook) ou [importando um existente](#import-a-runbook) de um arquivo ou da Galeria [Runbook](automation-runbook-gallery.md). Este artigo oferece informações sobre a criação e a importação de runbooks de um arquivo. Você pode obter todos os detalhes de acesso a runbooks e módulos da comunidade no [Runbook e galerias de módulos para a Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Criar um runbook

Você pode criar um novo runbook na Automação do Azure usando um dos portais do Azure ou o Windows PowerShell. Quando o runbook tiver sido criado, você poderá editá-lo usando as informações das [Noções básicas sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md) e [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um runbook no portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. No hub, selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Clique **em Criar um runbook**.
4. Digite um nome para o livro de execução e selecione seu [Tipo](automation-runbook-types.md). O nome do runbook deve começar com uma letra e pode conter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o runbook e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um runbook com o PowerShell

Você pode usar o [cmdlet New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) para criar um [runbook powershell vazio](automation-runbook-types.md#powershell-workflow-runbooks). Use `Type` o parâmetro para especificar um dos `New-AzAutomationRunbook`tipos de runbook definidos para .

O exemplo a seguir mostra como criar um novo runbook vazio.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um runbook

Você pode criar um novo runbook no Azure Automation importando um script PowerShell ou PowerShell Workflow **(.ps1),** um runbook gráfico exportado **(.graphrunbook)** ou um script Python 2 **(.py).**  Você precisa especificar o [tipo de runbook](automation-runbook-types.md) que é criado durante a importação, levando em consideração os aspectos a seguir.

* Um arquivo **.ps1** que não contenha um fluxo de trabalho pode ser importado para um [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) ou um [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Se você importá-lo em um runbook PowerShell Workflow, ele será convertido em um fluxo de trabalho. Neste caso, os comentários são incluídos no manual para descrever as alterações que foram feitas.

* Um arquivo **.ps1** contendo um PowerShell Workflow só pode ser importado para um [runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Se o arquivo contiver vários fluxos de trabalho powershell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importá-los separadamente.

* Um arquivo **.ps1** contendo um PowerShell Workflow não deve ser importado para um [runbook PowerShell,](automation-runbook-types.md#powershell-runbooks)pois o mecanismo de script PowerShell não pode reconhecê-lo.

* Um arquivo **.graphrunbook** só pode ser importado para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks). Observe que você só pode criar um runbook gráfico a partir de um arquivo **.graphrunbook.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importe um runbook de um arquivo com o portal Azure

Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure.

> [!NOTE]
> Você só pode importar um arquivo **.ps1** em um runbook PowerShell Workflow usando o portal.

1. No portal do Azure, abra sua conta da Automação.
2. No hub, selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Clique **em Importar um runbook**.
4. Clique **em Executar o arquivo** e selecione o arquivo para importar.
5. Se o **campo Nome** estiver habilitado, você terá a opção de alterar o nome do livro de execução. O nome deve começar com uma letra e pode conter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas você pode alterá-lo depois levar as restrições aplicáveis em consideração.
7. Clique em **Criar**. O novo runbook é exibido na lista de runbooks da conta de Automação.
8. Será necessário [publicar o runbook](#publish-a-runbook) antes de executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook gráfico powershell workflow, você pode convertê-lo para outro tipo. No entanto, você não pode converter um desses runbooks gráficos em um runbook textual.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importar um runbook de um arquivo de script com o Windows PowerShell

Use o [cmdlet Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) para importar um arquivo de script como um resumo do runbook PowerShell Workflow. Se o runbook já existir, a `Force` importação falhará a menos que você use o parâmetro com o cmdlet.

O exemplo a seguir mostra como importar um arquivo de script em um runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testar um runbook

Quando você testa um runbook, a [Versão de rascunho](#publish-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos [de saída](automation-runbook-output-and-messages.md#output-stream) e aviso [e erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no painel de saída de teste. As mensagens para o [fluxo Verbose](automation-runbook-output-and-messages.md#message-streams) são exibidas `VerbosePreference` no painel Saída somente se a variável](automation-runbook-output-and-messages.md#preference-variables) for definida como Continuar.

Mesmo que a versão de rascunho esteja em execução, o runbook ainda será executado normalmente e executará qualquer ação nos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks nos recursos de não produção.

O procedimento para testar cada [tipo de livro de execução](automation-runbook-types.md) é o mesmo. Não há diferença nos testes entre o editor textual e o editor gráfico do portal Azure.

1. Abra a versão Rascunho do livro de execução no [editor textual](automation-edit-textual-runbook.md) ou no [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique no botão **Testar** para abrir a página de Teste.
1. Se o runbook tiver parâmetros, eles estão listados no painel esquerdo, onde você pode fornecer valores a serem usados para o teste.
1. Se você quiser executar o teste em um [Trabalhador de runbook híbrido,](automation-hybrid-runbook-worker.md)altere **as configurações de execução** para **Hybrid Worker** e selecione o nome do grupo de destino.  Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
1. Clique no botão **Iniciar** para iniciar o teste.
1. Você pode usar os botões o painel Saída para parar ou suspender um [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou um runbook [gráfico](automation-runbook-types.md#graphical-runbooks) enquanto ele está sendo testado. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
1. Inspecione a saída do caderneta no painel Saída.

## <a name="publish-a-runbook"></a>Publicar um runbook

Quando você criar ou importar um novo runbook, deverá publicá-lo antes de poder executá-lo. Cada runbook no Azure Automation tem uma versão do Rascunho e uma versão publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão do Rascunho deve ser disponibilizada, você publica, substituindo a versão atual publicada com a versão do Rascunho.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publique um runbook no portal Azure

1. Abra o runbook no portal do Azure.
2. Clique em **Editar**.
3. Clique **em Publicar** e, em seguida, **Sim** em resposta à mensagem de verificação.

### <a name="publish-a-runbook-using-powershell"></a>Publicar um runbook usando o PowerShell

Use o [cmdlet Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) para publicar um runbook com o Windows PowerShell. O exemplo a seguir mostra como publicar um runbook de amostras.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Próximas etapas

* Para saber como você pode se beneficiar da Galeria de Módulos Runbook e PowerShell, consulte [Runbook e galerias de módulos para Azure Automation](automation-runbook-gallery.md).
* Para saber mais sobre a edição de runbooks PowerShell e PowerShell Workflow com um editor textual, consulte [Edição de runbooks texuais no Azure Automation](automation-edit-textual-runbook.md).
* Para saber mais sobre a autoria de runbook gráfico, consulte [Azure Automation](automation-graphical-authoring-intro.md).
