---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: f06a78039217d7bc6b03c9196ac6824477ea977e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79382145"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Habilitar o Azure Monitor em Conjuntos de Dimensionamento de Máquinas Virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Habilite o Azure Monitor para os Conjuntos de Dimensionamento de Máquinas Virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Observação: se o conjunto de dimensionamento upgradePolicy for definido como Manual, você precisará aplicar a extensão para todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso seria az vmss update-instances. |6 |1.0.1 |
|[Habilitar o Azure Monitor para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Habilite o Azure Monitor para as VMs (Máquinas Virtuais) no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. |6 |1.0.1 |
