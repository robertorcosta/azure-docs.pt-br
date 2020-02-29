---
title: Perguntas frequentes sobre automação do Azure | Microsoft Docs
description: Respostas para perguntas frequentes sobre a automação do Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925807"
---
# <a name="azure-automation-frequently-asked-questions"></a>Perguntas frequentes sobre a automação do Azure

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre a automação do Azure. Se você tiver outras dúvidas sobre seus recursos, vá para o fórum de discussão e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="update-management-solution"></a>Solução Gerenciamento de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar atualizações inesperadas no nível do sistema operacional?

Em algumas variantes do Linux, como Red Hat Enterprise Linux, as atualizações no nível do sistema operacional podem ocorrer por meio de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações onde o número de versão do SO é alterado. Como Gerenciamento de Atualizações usa os mesmos métodos para atualizar os pacotes que um administrador usaria localmente no computador Linux, esse comportamento é intencional.

Para evitar a atualização da versão do sistema operacional por meio de implantações Gerenciamento de Atualizações, use o recurso de **exclusão** .

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que as atualizações críticas/de segurança não são aplicadas?

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Essa opção filtra as atualizações que são aplicadas ao computador que atende aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como Gerenciamento de Atualizações executa o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas em Gerenciamento de Atualizações como tendo um impacto de segurança, mesmo que o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, poderá haver atualizações que não estejam marcadas como tendo um impacto de segurança nesse computador e, portanto, as atualizações não serão aplicadas. No entanto, Gerenciamento de Atualizações ainda pode relatar esse computador como não compatível, pois ele tem informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implantar atualizações para CentOS corretamente, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para o SUSE, *a seleção de* **outras atualizações** como a classificação pode fazer com que algumas atualizações de segurança também sejam instaladas se as atualizações de segurança relacionadas ao zypper (Gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, talvez seja necessário executar novamente a implantação da atualização. Para verificar, veja o log de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implantar atualizações em locatários do Azure?

Se você tiver computadores em outro relatório de locatário do Azure para Gerenciamento de Atualizações que você precisa aplicar patch, você terá que usar a solução alternativa a seguir para tê-los agendados. Você pode usar o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar uma agenda e usar o cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar os computadores no outro locatário para o parâmetro `-NonAzureComputer`. O exemplo a seguir mostra como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Se sua pergunta não for respondida aqui, você poderá consultar o fórum a seguir para obter mais perguntas e respostas.

- [Automação do Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Para obter comentários gerais sobre a solução de Gerenciamento de Atualizações, visite o [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management).