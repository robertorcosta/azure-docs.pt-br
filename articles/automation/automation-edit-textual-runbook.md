---
title: Editando runbooks de texto na Automação do Azure
description: Este artigo fornece procedimentos diferentes para trabalhar com runbooks do PowerShell e do Fluxo de Trabalho do PowerShell na Automação do Azure usando o editor de texto.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3bbfe26f336a25ee85f2223226d6eb513ae21736
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632151"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Editando runbooks de texto na Automação do Azure

O editor de texto na Automação do Azure pode ser usado para editar os [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e os [runbooks do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Este editor tem as características típicas de outros editores de código, como o IntelliSense. Ele também tem codificação de cores com recursos especiais adicionais para ajudá-lo a acessar recursos comuns a runbooks. 

O editor textual inclui um recurso para inserir código para cmdlets, ativos e runbooks filhos em um runbook. Em vez de digitar o código você mesmo, você pode selecionar a partir de uma lista de recursos disponíveis e o editor insere o código apropriado no runbook.

Cada runbook da Automação do Azure tem duas versões, Rascunho e Publicado. Edite a versão de Rascunho do runbook e publique-a para que ela possa ser executada. A versão Publicado não pode ser editada. Para obter mais informações, consulte [Publicar um runbook](manage-runbooks.md#publishing-a-runbook).

Este artigo oferece as etapas detalhadas para a execução de funções diferentes com este editor. Estes não são aplicáveis a [runbooks gráficos](automation-runbook-types.md#graphical-runbooks). Para trabalhar com esses runbooks, consulte [a autoria gráfica no Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Edição de um runbook com o portal Azure

Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. No portal Azure, selecione sua conta de Automação.
2. Em **PROCESS AUTOMATION,** selecione **Runbooks** para abrir a lista de runbooks.
3. Escolha o runbook para editar e clique em **Editar**.
4. Edite o runbook.
5. Clique em **Salvar** quando as edições estiverem concluídas.
6. Clique **em Publicar** se quiser publicar a versão de rascunho mais recente do livro de execução.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Insira um cmdlet em um runbook

1. Na tela do editor textual, posicione o cursor onde deseja colocar o cmdlet.
2. Expanda o nó **Cmdlets** no controle Biblioteca.
3. Expanda o módulo contendo o cmdlet para usar.
4. Clique com o botão direito do mouse no nome do cmdlet para inserir e selecionar **Adicionar à tela**. Se o cmdlet tiver mais de um parâmetro definido, o editor adicionará o conjunto padrão. Você também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. Observe que o código do cmdlet está inserido com toda a sua lista de parâmetros.
6. Fornecer um valor apropriado no lugar do valor cercado por suportes angulares (<>) para quaisquer parâmetros necessários. Remova todos os parâmetros que você não precisa.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Insira código para um livro de execução de crianças em um runbook

1. Na tela do editor textual, posicione o cursor onde deseja colocar o código para o [livro de execução infantil](automation-child-runbooks.md).
2. Expanda o nó **Runbooks** no controle Biblioteca.
3. Clique com botão direito do mouse no runbook a ser inserido e selecione **Adicionar à tela**.
4. O código do runbook filho é inserido com todos os espaços reservados para os parâmetros de runbook.
5. Substitua os espaços reservados pelos valores adequados para cada parâmetro.

### <a name="insert-an-asset-into-a-runbook"></a>Insira um ativo em um runbook

1. No controle do Canvas do editor textual, posicione o cursor onde deseja colocar o código para o manual do filho.
2. Expanda o nó **Ativos** no controle Biblioteca.
3. Expanda o nó para o tipo de ativo desejado.
4. Clique com o botão direito do mouse no nome do ativo para inserir e selecionar **Adicionar à tela**. Para [ativos variáveis,](automation-variables.md) **selecione Adicionar "Obter variável" à tela** ou adicionar **"Definir variável" à tela,** dependendo se você deseja obter ou definir a variável.
5. Observe que o código do ativo está inserido no manual.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Edição de um runbook de automação do Azure usando o Windows PowerShell

Para editar um runbook com o Windows PowerShell, use o editor de sua escolha e salve o runbook em um arquivo **.ps1.** Você pode usar o [cmdlet Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) para recuperar o conteúdo do runbook. Você pode usar o [cmdlet Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) para substituir o manual de rascunho existente pelo do modificado.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recupere o conteúdo de um runbook usando o Windows PowerShell

Os comandos de exemplo a seguir mostram como recuperar o script de um runbook e salvá-lo em um arquivo de script. Neste exemplo, a versão de Rascunho é recuperada. Também é possível recuperar a versão publicada do runbook, embora esta versão não possa ser alterada.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Alterar o conteúdo de um runbook usando o Windows PowerShell

Os seguintes comandos de exemplo mostram como substituir o conteúdo existente de um runbook pelo conteúdo de um arquivo de script. Esse é o mesmo procedimento de exemplo mostrado em [Para importar um runbook de um arquivo de script com o Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Artigos relacionados

* [Gerenciar runbooks na Automação do Azure](manage-runbooks.md)
* [Aprendizado do fluxo de trabalho PowerShell](automation-powershell-workflow.md)
* [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Conexões](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)

