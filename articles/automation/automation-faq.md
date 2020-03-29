---
title: Faq de automação azure | Microsoft Docs
description: Respostas para perguntas frequentes sobre a Automação Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925807"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation frequentemente fez perguntas

Este FaQ da Microsoft é uma lista de perguntas comumente feitas sobre o Azure Automation. Se você tiver alguma dúvida adicional sobre suas capacidades, vá ao fórum de discussão e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="update-management-solution"></a>Solução Gerenciamento de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar upgrades inesperados no nível do SO?

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, upgrades no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações onde o número de versão do SO é alterado. Como o Update Management usa os mesmos métodos para atualizar pacotes que um administrador usaria localmente na máquina Linux, esse comportamento é intencional.

Para evitar atualizar a versão do Sistema Operacional através de implantações do Update Management, use o recurso **Exclusão.**

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que as atualizações críticas/de segurança não são aplicadas?

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Esta opção filtra as atualizações que são aplicadas à máquina que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como o Update Management realiza o enriquecimento de atualizações na nuvem, algumas atualizações podem ser sinalizadas no Update Management como tendo um impacto de segurança, mesmo que a máquina local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança naquela máquina e, portanto, as atualizações não são aplicadas. No entanto, o Update Management ainda pode reportar essa máquina como incompatível porque ela tem informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização não funciona nas versões RTM do CentOS. Para implantar corretamente as atualizações do CentOS, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para SUSE, selecionar *apenas* **outras atualizações,** pois a classificação pode fazer com que algumas atualizações de segurança também sejam instaladas se as atualizações de segurança relacionadas ao zypper (gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, você pode ser obrigado a executar a implantação da atualização. Para verificar, veja o log de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implantar atualizações entre os inquilinos do Azure?

Se você tiver máquinas em outro inquilino do Azure reportando ao Update Management que você precisa corrigir, você terá que usar a solução a seguir para agendar. Você pode usar o [cmdlet New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com o `-ForUpdate` switch para criar um cronograma e usar o [cmdlet New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar as máquinas no outro inquilino para o `-NonAzureComputer` parâmetro. O exemplo a seguir mostra como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Próximas etapas

Se sua pergunta não for respondida aqui, você pode consultar o seguinte fórum para obter mais perguntas e respostas.

- [Automação Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Para obter feedback geral sobre a solução de Gerenciamento de Atualizações, visite o [fórum de feedback](https://feedback.azure.com/forums/905242-update-management).