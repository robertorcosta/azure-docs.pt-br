---
title: Reparando automaticamente os nós do AKS (serviço kubernetes do Azure)
description: Saiba mais sobre a funcionalidade de reparo automático do nó e como o AKS corrige os nós de trabalho interrompidos.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284833"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Reparo automático do nó do AKS (serviço de kubernetes do Azure)

O AKS verifica continuamente o estado de integridade dos nós de trabalho e executa o reparo automático dos nós se eles se tornarem não íntegros. Esta documentação descreve como o serviço de kubernetes do Azure (AKS) monitora os nós de trabalho e repara nós de trabalho não íntegros.  A documentação é informar os operadores de AKS sobre o comportamento da funcionalidade de reparo de nó. Também é importante observar que a plataforma Azure [realiza a manutenção em máquinas virtuais][vm-updates] que apresentam problemas. O AKS e o Azure trabalham juntos para minimizar as interrupções de serviço para seus clusters.

> [!Important]
> A funcionalidade de reparo automático do nó não tem suporte atualmente para pools de nós do Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como o AKS verifica se há nós não íntegros

> [!Note]
> AKS faz a ação de reparo nos nós com a conta de usuário **AKs-remediator**.

O AKS usa regras para determinar se um nó é um estado não íntegro e precisa de reparo. O AKS usa as regras a seguir para determinar se o reparo automático é necessário.

* O nó relata o status de **não legível** em verificações consecutivas dentro de um período de 10 minutos
* O nó não relata um status dentro de 10 minutos

Você pode verificar manualmente o estado de integridade de seus nós com kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como o reparo automático funciona

> [!Note]
> AKS faz a ação de reparo nos nós com a conta de usuário **AKs-remediator**.

Esse comportamento é para **conjuntos de dimensionamento de máquinas virtuais**.  O reparo automático leva várias etapas para reparar um nó quebrado.  Se um nó for determinado como não íntegro, o AKS tentará várias etapas de correção.  As etapas são executadas nesta ordem:

1. Depois que o tempo de execução do contêiner ficar sem resposta por 10 minutos, os serviços de tempo de execução com falha serão reiniciados no nó.
2. Se o nó não estiver pronto em 10 minutos, o nó será reinicializado.
3. Se o nó não estiver pronto em 30 minutos, o nó será recriado.

> [!Note]
> Se vários nós não estiverem íntegros, eles serão reparados um a um

## <a name="next-steps"></a>Próximas etapas

Use [zonas de disponibilidade][availability-zones] para aumentar a alta disponibilidade com suas cargas de trabalho de cluster do AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
