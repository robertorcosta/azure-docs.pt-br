---
title: Script de exemplo do Azure PowerShell – criar um espaço de trabalho do Log Analytics | Microsoft Docs
description: Script de exemplo do Azure PowerShell – criar um espaço de trabalho do Log Analytics
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: fc60be2364e80c288300d78cc5dd23eed4ea9e62
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658059"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar um espaço de trabalho do Log Analytics com o PowerShell

Esse script coloca rapidamente em funcionamento um espaço de trabalho do Azure Log Analytics, o que é necessário se você deseja começar a coletar, analisar e executar ações nos dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um novo espaço de trabalho do Log Analytics em sua assinatura. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Obtém informações sobre um workspace existente. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Cria um workspace no grupo de recursos e no local especificados. |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

