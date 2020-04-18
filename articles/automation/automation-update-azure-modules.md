---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617467"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Para atualizar os módulos do Azure em sua conta de Automação, você precisa usar o [runbook de módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código aberto. Para começar a usar o runbook **Update-AutomationAzureModulesForAccount** para atualizar os módulos do Azure, baixe-o do [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, você pode importá-lo em sua conta de Automação ou executá-lo como um script. Para saber como importar um runbook em sua conta de Automação, consulte [Importar um runbook](manage-runbooks.md#importing-a-runbook).

Os módulos PowerShell mais comuns são fornecidos por padrão em cada conta de Automação. A equipe do Azure atualiza os módulos do Azure regularmente. Portanto, para manter os módulos em suas contas de Automação atualizados, você deve usar o [runbook Update-AutomationAzureModulesForAccount.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essas alterações, por exemplo, renomear um parâmetro ou depreciar um cmdlet inteiramente, podem afetar negativamente seus runbooks.

Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Este teste deve incluir alterações iterativas, como a atualização dos módulos PowerShell.

Se você desenvolver seus scripts localmente, é recomendável ter as mesmas versões de módulo localmente que você tem em sua conta de Automação ao testar para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

> [!NOTE]
> Você não será capaz de excluir módulos globais, que são módulos que a Automação fornece fora da caixa.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Considerações

A seguir, algumas considerações a serem consideradas ao usar este artigo para atualizar seus Módulos Azure:

* O manual descrito neste artigo suporta a atualização dos módulos Azure, AzureRM e Az por padrão. Revise o [runbook de módulos Update Azure README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre a atualização de módulos Az.Automation com este runbook. Existem fatores importantes adicionais que você precisa levar em conta ao usar os módulos Az em sua conta de Automação. Para saber mais, consulte [Usando módulos Az em sua conta de Automação](az-modules.md).

* Antes de iniciar esse runbook, verifique se sua conta de Automação tem uma [credencial da conta Executar como do Azure](manage-runas-account.md) criada.

* Você pode usar esse código como um script PowerShell normal em vez de um runbook: basta entrar no `-Login $false` Azure usando o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) primeiro e depois passar para o script.

* Para usar esse runbook em nuvens soberanas, use o parâmetro `AzEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são AzureCloud (nuvem pública azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Esses valores podem `Get-AzEnvironment | select Name`ser recuperados usando . Se você não passar um valor para este cmdlet, o runbook será padrão para o AzureCloud.

* Se você quiser usar uma versão específica do módulo Azure PowerShell em vez do módulo `ModuleVersionOverrides` mais recente disponível na Galeria PowerShell, passe essas versões para o parâmetro opcional do runbook **Update-AutomationAzureModulesForAccount.** Para obter exemplos, confira o runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é igual ao botão **Atualizar Módulos do Azure**.

## <a name="next-steps"></a>Próximas etapas

Visite o [runbook de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de software livre para saber mais sobre ele.
