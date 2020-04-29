---
title: Galerias de runbook e de módulos para a Automação do Azure
description: Os runbooks e os módulos da Microsoft e da comunidade estão disponíveis para instalação e uso em seu ambiente da Automação do Azure.  Este artigo descreve como você pode acessar esses recursos e contribuir com seus runbooks para a galeria.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535546"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerias de runbook e de módulos para a Automação do Azure

Em vez de criar seus próprios runbooks e módulos na Automação do Azure, você pode acessar cenários que já foram desenvolvidos pela Microsoft e pela comunidade. Você pode obter runbooks e [módulos](#modules-in-powershell-gallery) do PowerShell dos runbooks Galeria do PowerShell e [Python](#use-python-runbooks) na galeria do script Center. Você também pode contribuir para a Comunidade compartilhando [cenários que você desenvolve](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Runbooks em Galeria do PowerShell

O [Galeria do PowerShell](https://www.powershellgallery.com/packages) fornece uma variedade de Runbooks da Microsoft e da Comunidade que você pode importar para a automação do Azure. Para usar um, baixe um runbook da galeria ou você pode importar runbooks diretamente da galeria ou da sua conta de automação no portal do Azure.

> [!NOTE]
> Não há suporte para runbooks gráficos no Galeria do PowerShell.

Você só pode importar diretamente do Galeria do PowerShell usando o portal do Azure. Você não pode executar essa função usando o PowerShell.

> [!NOTE]
> Você deve validar o conteúdo de todos os runbooks obtidos da Galeria do PowerShell e ter muito cuidado ao instalá-los e executá-los em um ambiente de produção.

## <a name="modules-in-powershell-gallery"></a>Módulos na Galeria do PowerShell

Os módulos do PowerShell contêm cmdlets que você pode usar em seus runbooks, e os módulos existentes que podem ser instalados na Automação do Azure estão disponíveis na [Galeria do PowerShell](https://www.powershellgallery.com). Você pode iniciar a galeria no portal do Azure e instalá-los diretamente na Automação do Azure. Você também pode baixá-los e instalá-los manualmente.

## <a name="common-solutions-available-in-powershell-gallery"></a>Soluções comuns disponíveis no Galeria do PowerShell

A lista a seguir contém alguns runbooks que fornecem soluções para cenários comuns. Para obter uma lista completa de runbooks criados pela equipe de automação do Azure, consulte [AzureAutomationTeam Profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -importa a versão mais recente de todos os módulos em uma conta de automação do Galeria do PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) -configura Diagnóstico do Azure e log Analytics para receber logs de automação do Azure que contêm o status do trabalho e os fluxos de trabalho.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -copia um arquivo remoto de uma máquina virtual do Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) -copia um arquivo local para uma máquina virtual do Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importar um runbook do PowerShell da Galeria de runbooks com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Galeria de Runbooks** em **automação de processo**.
3. Selecione o **código-fonte: Galeria do PowerShell**.
4. Localize o item da galeria desejado e selecione-o para exibir seus detalhes. À esquerda, você pode inserir parâmetros de pesquisa adicionais para o tipo e o fornecedor.

   ![Procurar na galeria](media/automation-runbook-gallery/browse-gallery.png)

5. Clique em **Exibir projeto de origem** para exibir o item no [Script Center do TechNet](https://gallery.technet.microsoft.com/).
6. Para importar um item, clique nele para exibir seus detalhes e, em seguida, clique em **importar**.

   ![Botão Importar](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcionalmente, altere o nome do runbook e clique em **OK** para importar o runbook.
8. O runbook aparece na guia **Runbooks** da conta de automação.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Adicionar um runbook do PowerShell à Galeria

A Microsoft incentiva você a adicionar runbooks ao Galeria do PowerShell que você considera úteis para outros clientes. A Galeria do PowerShell aceita módulos do PowerShell e scripts do PowerShell. Você pode adicionar um runbook [carregando-o no Galeria do PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importar um módulo da Galeria de módulos com o portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Módulos** em **Recursos Compartilhados** para abrir a lista de módulos.
3. Clique em **Procurar na Galeria** na parte superior da página.

   ![Galeria de módulos](media/automation-runbook-gallery/modules-blade.png)

4. Na página Procurar na Galeria, é possível pesquisar pelos seguintes campos:

   * Nome do módulo
   * Marcas
   * Autor
   * Nome do recurso do DSC/cmdlet

5. Localize o módulo em que você está interessado e selecione-o para exibir seus detalhes.

   Quando você analisar um módulo específico, poderá exibir mais informações. Essas informações incluem um link para a Galeria do PowerShell, para as dependências necessárias e para todos os cmdlets ou recursos de DSC que o módulo contém.

   ![Detalhes do módulo do PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Para instalar o módulo diretamente na automação do Azure, clique em **importar**.
7. No painel importar, você pode ver o nome do módulo a ser importado. Se todas as dependências estiverem instaladas, o botão **OK** será ativado. Em caso de dependências ausentes, será necessário importar tais dependências antes de importar o módulo.
8. No painel importar, clique em **OK** para importar o módulo. Enquanto a Automação do Azure importa um módulo para sua conta, ela extrai os metadados sobre o módulo e os cmdlets. Essa ação pode levar alguns minutos, pois cada atividade precisa ser extraída.
9. Você recebe uma notificação inicial informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.
10. Depois que o módulo é importado, você pode ver as atividades disponíveis. Você pode usar recursos de módulo em seus runbooks e recursos de DSC.

> [!NOTE]
> Os módulos que suportam apenas o núcleo do PowerShell não são suportados na Automação do Azure e não podem ser importados no portal do Azure ou implantados diretamente da Galeria do PowerShell.

## <a name="use-python-runbooks"></a>Usar runbooks do Python

Runbooks Python estão disponíveis na [Galeria do Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Você pode contribuir com os runbooks do Python para a galeria do script Center clicando em **carregar uma contribuição**. Ao fazer isso, certifique-se de adicionar a `Python` marca ao carregar sua contribuição.

> [!NOTE]
> Para carregar conteúdo no [Script Center](https://gallery.technet.microsoft.com/scriptcenter), você precisa de um mínimo de 100 pontos.

## <a name="request-a-runbook-or-module"></a>Solicitar um runbook ou módulo

Você pode enviar solicitações para a [Voz do Usuário](https://feedback.azure.com/forums/246290-azure-automation/).  Se precisar de ajuda para escrever um runbook ou se tiver dúvidas em relação ao PowerShell, poste uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks, consulte [gerenciar runbook na automação do Azure](manage-runbooks.md).
* Para entender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell com runbooks, consulte [aprendendo o fluxo de trabalho do PowerShell](automation-powershell-workflow.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
