---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: e6ac52694dd4a786714b571f50dc5adaf79dff06
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668533"
---
|{1&gt;Nome&lt;1} |Descrição |Políticas |{1&gt;Version&lt;1} |
|---|---|---|---|
|[Habilitar Azure Monitor para conjuntos de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Habilite Azure Monitor para os conjuntos de dimensionamento de máquinas virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Observação: se o conjunto de dimensionamento upgradePolicy for definido como Manual, você precisará aplicar a extensão para todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso seria az vmss update-instances. |6 |1.0.0 – versão prévia |
|[Habilitar o Azure Monitor para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Habilite o Azure Monitor para as VMs (Máquinas Virtuais) no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. |6 |1.0.0 – versão prévia |
