---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 504005d9d0a037640a959ca4861971d0ca90823c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233972"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Habilitar o Azure Monitor em Conjuntos de Dimensionamento de Máquinas Virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Habilite o Azure Monitor para os Conjuntos de Dimensionamento de Máquinas Virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Observação: se o conjunto de dimensionamento upgradePolicy for definido como Manual, você precisará aplicar a extensão para todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso seria az vmss update-instances. |6 |1.0.1 |
|[Habilitar o Azure Monitor para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Habilite o Azure Monitor para as VMs (máquinas virtuais) no escopo (grupo de gerenciamento, assinatura ou grupo de recursos) especificado. Usa o espaço de trabalho do Log Analytics como parâmetro. |10 |2.0.0 |
