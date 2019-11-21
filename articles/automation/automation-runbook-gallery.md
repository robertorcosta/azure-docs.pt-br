---
title: Galerias de runbook e de módulos para a Automação do Azure
description: Os runbooks e os módulos da Microsoft e da comunidade estão disponíveis para instalação e uso em seu ambiente da Automação do Azure.  Este artigo descreve como você pode acessar esses recursos e contribuir com seus runbooks para a Galeria.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 86eebf33f870780871e4c873936e491772c73b63
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231621"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerias de runbook e de módulos para a Automação do Azure

Em vez de criar seus próprios runbooks e módulos na Automação do Azure, você pode acessar cenários que já foram desenvolvidos pela Microsoft e pela comunidade.

You can get PowerShell runbooks and [modules](#modules-in-powershell-gallery) from the PowerShell Gallery and [Python runbooks](#python-runbooks) from the Script Center Gallery. You can also contribute to the community by sharing scenarios that you develop, see Adding a runbook to the gallery

## <a name="runbooks-in-powershell-gallery"></a>Runbooks in PowerShell Gallery

The [PowerShell Gallery](https://www.powershellgallery.com/packages) provides a variety of runbooks from Microsoft and the community that you can import into Azure Automation. To use one, download a runbook from the gallery, or you can directly import runbooks from the gallery, or from your Automation Account in the Azure portal.

You can only import directly from the PowerShell Gallery using the Azure portal. You cannot perform this function using PowerShell.

> [!NOTE]
> You should validate the contents of any runbooks that you get from the PowerShell Gallery and use extreme caution in installing and running them in a production environment.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>To import a PowerShell runbook from the Runbook Gallery with the Azure portal

1. No portal do Azure, abra sua conta da Automação.
2. Em **Automação de Processo**, clique em **Galeria de Runbooks**
3. Select **Source: PowerShell Gallery**.
4. Localize o item da galeria desejado e selecione-o para exibir seus detalhes. À esquerda, você pode inserir parâmetros de pesquisa adicionais para o tipo e o fornecedor.

   ![Procurar na galeria](media/automation-runbook-gallery/browse-gallery.png)

5. Clique em **Exibir projeto de origem** para exibir o item no [Script Center do TechNet](https://gallery.technet.microsoft.com/).
6. Para importar um item, clique nele para exibir seus detalhes e clique no botão **Importar** .

   ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
8. O runbook aparece na guia **Runbooks** da Conta de Automação.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Adding a PowerShell runbook to the gallery

Microsoft encourages you to add runbooks to the PowerShell Gallery that you think would be useful to other customers. The PowerShell Gallery accepts PowerShell modules and PowerShell scripts. You can add a runbook by [uploading it to the PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Graphical runbooks are not supported in PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria do PowerShell

Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks, e os módulos existentes que podem ser instalados na Automação do Azure estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com). Você pode iniciar a galeria no portal do Azure e instalá-los diretamente na Automação do Azure. Você também pode baixá-los e instalá-los manualmente.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Para importar um módulo da Galeria de Módulos de Automação com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Módulos** em **Recursos Compartilhados** para abrir a lista de módulos.
3. Clique em **Procurar na Galeria** na parte superior da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página **Procurar na Galeria**, é possível pesquisar pelos seguintes campos:

   * Nome do Módulo
   * Marcas
   * Autor
   * Nome do recurso do DSC/cmdlet

5. Localize o módulo em que você está interessado e selecione-o para exibir seus detalhes.

   Quando você analisar um módulo específico, poderá exibir mais informações. Essas informações incluem um link para a Galeria do PowerShell, para as dependências necessárias e para todos os cmdlets ou recursos de DSC que o módulo contém.

   ![Detalhes do módulo do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na Automação do Azure, clique no botão **Importar** .
7. Ao clicar no botão Importar, no painel **Importar**, você verá o nome do módulo que está prestes a importar. Se todas as dependências estiverem instaladas, o botão **OK** será ativado. Em caso de dependências ausentes, será necessário importar tais dependências antes de importar o módulo.
8. Na página **Importar**, clique em **OK** para importar o módulo. Enquanto a Automação do Azure importa um módulo para sua conta, ela extrai os metadados sobre o módulo e os cmdlets. A ação acima pode demorar alguns minutos, pois cada atividade precisa ser extraída.
9. Você recebe uma notificação inicial informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.
10. Depois que o módulo é importado, você pode ver as atividades disponíveis. É possível usar os recursos em seus runbooks e Desired State Configuration.

> [!NOTE]
> Os módulos que suportam apenas o núcleo do PowerShell não são suportados na Automação do Azure e não podem ser importados no portal do Azure ou implantados diretamente da Galeria do PowerShell.

## <a name="python-runbooks"></a>Runbooks Python

Runbooks Python estão disponíveis na [Galeria do Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). You can contribute Python runbooks to the Script Center gallery by clicking **Upload a contribution**. Quando você fizer, certifique-se de que você adicione a marca **Python** ao carregar sua contribuição.

> [!NOTE]
> In order to upload content to [Script Center](https://gallery.technet.microsoft.com/scriptcenter) a minimum of 100 points is required.

## <a name="requesting-a-runbook-or-module"></a>Solicitando um runbook ou um módulo

Você pode enviar solicitações para a [Voz do Usuário](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um runbook ou se tiver dúvidas em relação ao PowerShell, poste uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Common solutions available in the runbook gallery

The list below contains a few runbooks that provide solutions to common scenarios. For a full list of runbooks created by the Azure Automation team, see [AzureAutomationTeam profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Imports the latest version on PowerShell Gallery of all modules in an Automation account.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - This script configures Azure Diagnostics and Log Analytics to receive Azure Automation logs containing job status and job streams.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - This runbook copies a remote file from a Windows Azure virtual machine.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - This runbook copies a local file to an Azure virtual machine.

## <a name="next-steps"></a>Próximos passos

* Para se familiarizar com os runbooks, confira [Gerenciar runbook na Automação do Azure](manage-runbooks.md)
* Para entender as diferenças entre o PowerShell e o os fluxos de trabalho do PowerShell com runbooks, confira [Aprendendo sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)
* For more information on PowerShell, including language reference and learning modules, refer to the [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
