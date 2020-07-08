---
title: Reparando automaticamente os nós do AKS (serviço kubernetes do Azure)
description: Saiba mais sobre a funcionalidade de reparo automático do nó e como o AKS corrige os nós de trabalho interrompidos.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735618"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Reparo automático do nó do AKS (serviço de kubernetes do Azure)

O AKS verifica continuamente o estado de integridade dos nós de trabalho e executa o reparo automático dos nós se eles se tornarem não íntegros. Este documento informa os operadores sobre como a funcionalidade automática de reparo de nó se comporta. Além dos reparos do AKS, a plataforma de VM do Azure [executa a manutenção em máquinas virtuais][vm-updates] que também têm problemas. O AKS e as VMs do Azure trabalham em conjunto para minimizar as interrupções de serviço para clusters.

> [!Important]
> A funcionalidade de reparo automático do nó não tem suporte atualmente para pools de nós do Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como o AKS verifica se há nós não íntegros

> [!Note]
> AKS faz a ação de reparo nos nós com a conta de usuário **AKs-remediator**.

O AKS usa regras para determinar se um nó não está íntegro e precisa de reparo. O AKS usa as regras a seguir para determinar se o reparo automático é necessário.

* O nó relata o status de **não legível** em verificações consecutivas dentro de um período de 10 minutos
* O nó não relata um status dentro de 10 minutos

Você pode verificar manualmente o estado de integridade de seus nós com kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como o reparo automático funciona

> [!Note]
> AKS inicia as operações de reparo com a conta de usuário **AKs-remediator**.

O reparo automático tem suporte por padrão para clusters com um tipo de conjunto de VMs dos **conjuntos de dimensionamento de máquinas virtuais**. Se um nó for considerado não íntegro com base nas regras acima, o AKS reinicializará o nó após 10 minutos consecutivos não íntegros. Se os nós permanecerem não íntegros após a operação de reparo inicial, as correções adicionais serão investigadas por engenheiros de AKS.
  
Se vários nós não estiverem íntegros durante uma verificação de integridade, cada nó será reparado individualmente antes do início de outro reparo.

## <a name="next-steps"></a>Próximas etapas

Use as [Zonas de Disponibilidade][availability-zones] para aumentar a HA com suas cargas de trabalho de cluster do AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
