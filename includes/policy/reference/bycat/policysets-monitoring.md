---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0d6f01a0a8854293d5bc6cd6532f28ab2ef3d067
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022306"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Habilitar o Azure Monitor em Conjuntos de Dimensionamento de Máquinas Virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Habilite o Azure Monitor para os Conjuntos de Dimensionamento de Máquinas Virtuais no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. Observação: se o conjunto de dimensionamento upgradePolicy for definido como Manual, você precisará aplicar a extensão para todas as VMs no conjunto ao chamar o upgrade nelas. Na CLI, isso seria az vmss update-instances. |6 |1.0.1 |
|[Habilitar o Azure Monitor para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Habilite o Azure Monitor para as VMs (máquinas virtuais) no escopo (grupo de gerenciamento, assinatura ou grupo de recursos) especificado. Usa o espaço de trabalho do Log Analytics como parâmetro. |10 |2.0.0 |
