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
ms.openlocfilehash: aef6a58d18c0d9c1b18915786dd6e3359c31eda0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855784"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Editando runbooks de texto na Automação do Azure

O editor de texto na Automação do Azure pode ser usado para editar os [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e os [runbooks do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Esse editor tem os recursos típicos de outros editores de código, como o IntelliSense. Ele também tem codificação de cores com recursos especiais adicionais para ajudá-lo a acessar recursos comuns a runbooks. 

O editor textual inclui um recurso para inserir código para cmdlets, ativos e runbooks filhos em um runbook. Em vez de digitar o código por conta própria, você pode selecionar em uma lista de recursos disponíveis e o editor insere o código apropriado no runbook.

Cada runbook da Automação do Azure tem duas versões, Rascunho e Publicado. Edite a versão de Rascunho do runbook e publique-a para que ela possa ser executada. A versão Publicado não pode ser editada. Para obter mais informações, consulte [publicar um runbook](manage-runbooks.md#publish-a-runbook).

Este artigo oferece as etapas detalhadas para a execução de funções diferentes com este editor. Eles não são aplicáveis a [runbooks gráficos](automation-runbook-types.md#graphical-runbooks). Para trabalhar com esses runbooks, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Editando um runbook com o portal do Azure

Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. Na portal do Azure, selecione sua conta de automação.
2. Em **automação de processo**, selecione **Runbooks** para abrir a lista de runbooks.
3. Escolha o runbook a ser editado e clique em **Editar**.
4. Edite o runbook.
5. Clique em **Salvar** quando as edições estiverem concluídas.
6. Clique em **publicar** se desejar publicar a versão de rascunho mais recente do runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Inserir um cmdlet em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar o cmdlet.
2. Expanda o nó **Cmdlets** no controle Biblioteca.
3. Expanda o módulo que contém o cmdlet a ser usado.
4. Clique com o botão direito do mouse no nome do cmdlet a ser inserido e selecione **Adicionar à tela**. Se o cmdlet tiver mais de um conjunto de parâmetros, o editor adicionará o conjunto padrão. Você também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. Observe que o código para o cmdlet é inserido com sua lista completa de parâmetros.
6. Forneça um valor apropriado no lugar do valor entre colchetes angulares (<>) para quaisquer parâmetros necessários. Remova todos os parâmetros desnecessários.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Inserir código para um runbook filho em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar o código para o [runbook filho](automation-child-runbooks.md).
2. Expanda o nó **Runbooks** no controle Biblioteca.
3. Clique com botão direito do mouse no runbook a ser inserido e selecione **Adicionar à tela**.
4. O código do runbook filho é inserido com todos os espaços reservados para os parâmetros de runbook.
5. Substitua os espaços reservados pelos valores adequados para cada parâmetro.

### <a name="insert-an-asset-into-a-runbook"></a>Inserir um ativo em um runbook

1. No controle Canvas do editor de texto, posicione o cursor onde você deseja colocar o código para o runbook filho.
2. Expanda o nó **Ativos** no controle Biblioteca.
3. Expanda o nó para o tipo de ativo desejado.
4. Clique com o botão direito do mouse no nome do ativo para inserir e selecione **Adicionar à tela**. Para [ativos variáveis](automation-variables.md), selecione **Adicionar "obter variável" para tela** ou **Adicionar "definir variável" à tela**, dependendo se você deseja obter ou definir a variável.
5. Observe que o código para o ativo é inserido no runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Editando um runbook de automação do Azure usando o Windows PowerShell

Para editar um runbook com o Windows PowerShell, use o editor de sua escolha e salve o runbook em um arquivo **. ps1** . Você pode usar o cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) para recuperar o conteúdo do runbook. Você pode usar o cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) para substituir o runbook de rascunho existente por um modificado.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recuperar o conteúdo de um runbook usando o Windows PowerShell

Os comandos de exemplo a seguir mostram como recuperar o script de um runbook e salvá-lo em um arquivo de script. Neste exemplo, a versão de Rascunho é recuperada. Também é possível recuperar a versão publicada do runbook, embora essa versão não possa ser alterada.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Alterar o conteúdo de um runbook usando o Windows PowerShell

Os seguintes comandos de exemplo mostram como substituir o conteúdo existente de um runbook pelo conteúdo de um arquivo de script. 

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
* [Conhecendo o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)
* [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Conexões](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)
* [Referência de cmdlet PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
