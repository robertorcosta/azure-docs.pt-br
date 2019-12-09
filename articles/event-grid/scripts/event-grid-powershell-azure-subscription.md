---
title: Azure PowerShell – criar uma assinatura do Azure
description: Exemplo de script do Azure PowerShell e da Grade de Eventos do Azure – criar uma assinatura do Azure
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: 8c90e39cef1420ec06b7f62ebf9982720ffebf94
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664124"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Inscrever-se em eventos de uma assinatura do Azure com o PowerShell

Este script cria uma assinatura de Grade de Eventos para os eventos de uma assinatura do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Exemplo de script - estável

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Exemplo de script – módulo de visualização

Este script de exemplo de versão prévia exige o módulo da Grade de Eventos. Para instalar, execute `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar a assinatura do evento. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Criar uma assinatura na Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
