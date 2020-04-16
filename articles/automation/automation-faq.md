---
title: Faq de automação azure | Microsoft Docs
description: Respostas para perguntas frequentes sobre a Automação Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405956"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation frequentemente fez perguntas

Este FaQ da Microsoft é uma lista de perguntas comumente feitas sobre o Azure Automation. Se você tiver alguma dúvida adicional sobre suas capacidades, vá ao fórum de discussão e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Solução Gerenciamento de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar upgrades inesperados no nível do SO?

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, upgrades no nível do SO podem ocorrer através de pacotes. Isso pode levar à correção do Update Management, na qual o número da versão do Sistema Operacional muda. Como o Update Management usa os mesmos métodos para atualizar pacotes que um administrador usa localmente em uma máquina Linux, esse comportamento é intencional.

Para evitar atualizar a versão do Sistema Operacional através de implantações do Update Management, use o recurso **Exclusão.**

No Red Hat Enterprise Linux, o `redhat-release-server.x86_64`nome do pacote a ser excluir é .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que as atualizações críticas/de segurança não são aplicadas?

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Esta opção filtra as atualizações que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como o Update Management realiza o enriquecimento de atualizações na nuvem, você pode sinalizar algumas atualizações no Gerenciamento de Atualizações como tendo um impacto de segurança, mesmo que a máquina local não tenha essas informações. Se você aplicar atualizações críticas a uma máquina Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança naquela máquina e, portanto, não são aplicadas. No entanto, o Update Management ainda pode reportar essa máquina como incompatível porque ela tem informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização não funciona nas versões RTM do CentOS. Para implantar corretamente as atualizações do CentOS, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para SUSE, selecionar SOMENTE **outras atualizações,** pois a classificação pode fazer com que algumas atualizações adicionais de segurança sejam instaladas se as atualizações de segurança relacionadas ao zypper (gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, você pode ser obrigado a executar novamente a implantação da atualização e, em seguida, verificar a implantação através do registro de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implantar atualizações entre os inquilinos do Azure?

Se você tem máquinas que precisam ser corrigidas em outro inquilino do Azure reportando ao Update Management, você deve usar uma solução para agendar. Você pode usar o [cmdlet New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro especificado para criar um cronograma. Você pode usar o [cmdlet New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passar `NonAzureComputer` as máquinas no outro inquilino para o parâmetro. O exemplo a seguir mostra como fazer isso.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Próximas etapas

Se sua pergunta não for respondida aqui, você pode consultar as seguintes fontes para obter mais perguntas e respostas.

- [Automação do Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)
