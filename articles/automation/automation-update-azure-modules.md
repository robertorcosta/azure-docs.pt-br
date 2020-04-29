---
title: Atualizar módulos Azure PowerShell na automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769660"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Atualizar módulos Azure PowerShell na automação do Azure

Para atualizar os módulos do Azure em sua conta de automação, você precisa usar o [runbook atualizar módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código-fonte aberto. Para começar a usar o runbook **Update-AutomationAzureModulesForAccount** para atualizar os módulos do Azure, baixe-o do [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, você pode importá-lo para sua conta de automação ou executá-lo como um script. Para saber como importar um runbook em sua conta de automação, consulte [importar um runbook](manage-runbooks.md#importing-a-runbook).

Os módulos mais comuns do PowerShell são fornecidos por padrão em cada conta de automação. A equipe do Azure atualiza os módulos do Azure regularmente. Portanto, para manter os módulos em suas contas de automação atualizados, você deve usar o runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essas alterações, por exemplo, renomear um parâmetro ou substituir totalmente um cmdlet, podem afetar negativamente seus runbooks.

Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell.

Se você desenvolver seus scripts localmente, é recomendável ter as mesmas versões de módulo localmente que você tem em sua conta de automação ao testar para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

> [!NOTE]
> Você não poderá excluir módulos globais, que são módulos que a automação fornece pronta para uso.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Considerações

Veja a seguir algumas considerações a serem levadas em conta ao usar este artigo para atualizar seus módulos do Azure:

* O runbook descrito neste artigo dá suporte à atualização dos módulos Azure, AzureRM e AZ por padrão. Examine o [Leiame do runbook atualizar módulos do Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre como atualizar os módulos AZ. Automation com este runbook. Há fatores importantes adicionais que você precisa levar em conta ao usar os módulos AZ em sua conta de automação. Para saber mais, consulte [gerenciar módulos na automação do Azure](shared-resources/modules.md).

* Antes de iniciar esse runbook, verifique se sua conta de Automação tem uma [credencial da conta Executar como do Azure](manage-runas-account.md) criada.

* Você pode usar esse código como um script do PowerShell regular em vez de um runbook: basta entrar no Azure usando o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) primeiro e, `-Login $false` em seguida, passar para o script.

* Para usar esse runbook em nuvens soberanas, use o parâmetro `AzEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são AzureCloud (nuvem pública do Azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Esses valores podem ser recuperados `Get-AzEnvironment | select Name`usando. Se você não passar um valor para esse cmdlet, o runbook será padronizado como AzureCloud.

* Se você quiser usar uma versão específica do módulo Azure PowerShell em vez do módulo mais recente disponível no Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AutomationAzureModulesForAccount** . Para obter exemplos, confira o runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é igual ao botão **Atualizar Módulos do Azure**.

## <a name="next-steps"></a>Próximas etapas

Visite o [runbook de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de software livre para saber mais sobre ele.
