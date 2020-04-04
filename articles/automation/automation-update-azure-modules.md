---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 721bb3579fec10df88be471d67f68c0846aa9432
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632121"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Para atualizar os módulos do Azure em sua Conta de Automação, você precisa usar o [runbook de módulos Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código aberto. Para começar a usar o runbook **Update-AutomationAzureModulesForAccount** para atualizar os módulos do Azure, baixe-o do [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, você pode importá-lo para sua conta de Automação ou executá-lo como um script. Para saber como importar um runbook em sua Conta de Automação, consulte [Importar um runbook](manage-runbooks.md#importing-a-runbook).

Os módulos PowerShell mais comuns do AzureRM são fornecidos por padrão em cada conta de Automação. A equipe do Azure atualiza os módulos do Azure regularmente, portanto, para se manter atualizado, você vai querer usar o runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) para atualizar os módulos em suas contas de Automação.

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essa ação pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente.

Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell.

Se você desenvolve seus scripts localmente, é recomendável ter as mesmas versões do módulo localmente que você tem em sua Conta de Automação quando os testes para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

> [!NOTE]
> Você não será capaz de excluir módulos globais - módulos que a Automação fornece fora da caixa.

## <a name="considerations"></a>Considerações

A seguir estão algumas considerações que se deve fazer ao usar esse processo para atualizar seus módulos do Azure:

* Este runbook suporta a atualização dos módulos **Azure** e **AzureRm** por padrão. Este runbook também suporta atualizar os módulos **Az.** Revise o [runbook de módulos Update Azure README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre a atualização de `Az` módulos com este runbook. Existem fatores importantes adicionais que você `Az` precisa levar em conta ao usar os módulos em sua Conta de Automação, para saber mais, consulte [Usando módulos Az em sua Conta de Automação](az-modules.md).

* Antes de iniciar esse runbook, verifique se sua conta de Automação tem uma [credencial da conta Executar como do Azure](manage-runas-account.md) criada.

* Você pode usar esse código como um script PowerShell normal em vez de um runbook: basta entrar no `-Login $false` Azure usando o comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) primeiro e depois passar para o script.

* Para usar esse runbook em nuvens soberanas, use o parâmetro `AzureRmEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Esses valores podem ser recuperados usando `Get-AzureRmEnvironment | select Name`. Se você não passar um valor para esse parâmetro, o padrão do runbook será a nuvem pública do Azure **AzureCloud**

* Se você desejar usar uma versão específica do módulo Azure PowerShell, em vez da versão mais recente disponível na Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AutomationAzureModulesForAccount**. Para obter exemplos, confira o runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é igual ao botão **Atualizar Módulos do Azure**.

## <a name="next-steps"></a>Próximas etapas

Visite o [runbook de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de software livre para saber mais sobre ele.
