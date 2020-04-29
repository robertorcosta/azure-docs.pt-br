---
title: Perguntas frequentes sobre automação do Azure | Microsoft Docs
description: Respostas para perguntas frequentes sobre a automação do Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405956"
---
# <a name="azure-automation-frequently-asked-questions"></a>Perguntas frequentes sobre a automação do Azure

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre a automação do Azure. Se você tiver outras dúvidas sobre seus recursos, vá para o fórum de discussão e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Solução Gerenciamento de Atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso evitar atualizações inesperadas no nível do sistema operacional?

Em algumas variantes do Linux, como Red Hat Enterprise Linux, as atualizações no nível do sistema operacional podem ocorrer por meio de pacotes. Isso pode levar a Gerenciamento de Atualizações execuções nas quais o número de versão do sistema operacional é alterado. Como Gerenciamento de Atualizações usa os mesmos métodos para atualizar os pacotes que um administrador usa localmente em um computador Linux, esse comportamento é intencional.

Para evitar a atualização da versão do sistema operacional por meio de implantações Gerenciamento de Atualizações, use o recurso de **exclusão** .

Em Red Hat Enterprise Linux, o nome do pacote a ser `redhat-release-server.x86_64`excluído é.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que as atualizações críticas/de segurança não são aplicadas?

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Essa opção filtra as atualizações que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como Gerenciamento de Atualizações executa o enriquecimento de atualização na nuvem, você pode sinalizar algumas atualizações em Gerenciamento de Atualizações como tendo um impacto de segurança, mesmo que o computador local não tenha essas informações. Se você aplicar atualizações críticas a um computador Linux, pode haver atualizações que não estão marcadas como tendo um impacto de segurança nesse computador e, portanto, não são aplicadas. No entanto, Gerenciamento de Atualizações ainda pode relatar esse computador como não compatível porque ele tem informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implantar atualizações para CentOS corretamente, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para o SUSE, a seleção de apenas **outras atualizações** como a classificação pode fazer com que algumas atualizações de segurança adicionais sejam instaladas se as atualizações de segurança relacionadas ao zypper (Gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, talvez seja necessário executar novamente a implantação da atualização e, em seguida, verificar a implantação por meio do log de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implantar atualizações em locatários do Azure?

Se você tiver computadores que precisam de aplicação de patch em outro relatório de locatário do Azure para Gerenciamento de Atualizações, você deve usar uma solução alternativa a seguir para obtê-los agendados. Você pode usar o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro especificado para criar uma agenda. Você pode usar o cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passar os computadores no outro locatário para o `NonAzureComputer` parâmetro. O exemplo a seguir mostra como fazer isso.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Próximas etapas

Se sua pergunta não for respondida aqui, você poderá consultar as seguintes fontes para obter mais perguntas e respostas.

- [Automação do Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)
