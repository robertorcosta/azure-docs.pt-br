---
title: Perguntas frequentes sobre a Automação do Azure | Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Automação do Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 1cd20e28d1b36167154059adf728a9cfdf8102bc
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836610"
---
# <a name="azure-automation-frequently-asked-questions"></a>Perguntas frequentes sobre a Automação do Azure

Essas perguntas frequentes da Microsoft são uma lista de perguntas comuns sobre a Automação do Azure. Se você tiver alguma pergunta adicional sobre os recursos, vá para o fórum de discussão e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="update-management"></a>Gerenciamento de atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso impedir atualizações de nível de sistema operacional inesperadas?

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, as atualizações no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações em que o número de versão do SO é alterado. Como o Gerenciamento de Atualizações usa os mesmos métodos para atualizar pacotes que um administrador usa localmente em um computador Linux, esse comportamento é intencional.

Para evitar atualização da versão do SO por meio de implantações do Gerenciamento de Atualizações, use o recurso **Exclusão**.

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que as atualizações críticas/de segurança não são aplicadas?

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Essa opção filtra as atualizações que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como o Gerenciamento de Atualizações realiza o enriquecimento de atualização na nuvem, você pode sinalizar algumas atualizações no Gerenciamento de Atualizações como tendo um impacto de segurança, embora o computador local não tenha essas informações. Se você aplicar atualizações críticas a um computador Linux, pode haver atualizações, que não estão marcadas como tendo um impacto de segurança nesse computador e, portanto, não são aplicadas. No entanto, o Gerenciamento de Atualizações ainda pode relatar que o computador não é compatível porque possui informações adicionais sobre a atualização relevante.

Implantar atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implantar corretamente atualizações para CentOS, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para SUSE, selecionar SOMENTE **Outras atualizações** como a classificação pode fazer algumas atualizações de segurança também serem instaladas se as atualizações de segurança relacionados ao zypper (gerenciamento de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, você pode requerer executar novamente a implantação da atualização e verificar a implantação por meio do log de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implantar atualizações em locatários do Azure?

Se você tiver computadores que precisam de correções em outro locatário do Azure relatando ao Gerenciamento de Atualizações, use a seguinte solução alternativa para agendá-las. Você pode usar o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) com o parâmetro `ForUpdateConfiguration` especificado para criar uma agenda. Use o cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passe os computadores no outro locatário para o parâmetro `NonAzureComputer`. O exemplo a seguir mostra como fazer isso.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Próximas etapas

Se a sua pergunta não estiver respondida aqui, confira as fontes a seguir para ver outras perguntas e respostas.

- [Automação do Azure](https://docs.microsoft.com/answers/topics/azure-automation.html)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)
