---
title: Reparando automaticamente os nós do AKS (serviço kubernetes do Azure)
description: Saiba mais sobre a funcionalidade de reparo automático do nó e como o AKS corrige os nós de trabalho interrompidos.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 7fcb7b380f3694aaf34328019c3e09f5157c9e64
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542035"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Reparo automático do nó do AKS (serviço de kubernetes do Azure)

O AKS verifica continuamente o estado de integridade dos nós de trabalho e executa o reparo automático dos nós se eles se tornarem não íntegros. Este documento informa os operadores sobre como a funcionalidade automática de reparo de nó se comporta. Além dos reparos do AKS, a plataforma de VM do Azure [executa a manutenção em máquinas virtuais][vm-updates] que também têm problemas. O AKS e as VMs do Azure trabalham em conjunto para minimizar as interrupções de serviço para clusters.

## <a name="limitations"></a>Limitações

* Atualmente, não há suporte para pools de nós do Windows.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como o AKS verifica se há nós não íntegros

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

Se um nó for considerado não íntegro com base nas regras acima e permanecer não íntegro por 10 minutos consecutivos, o AKS reinicializará o nó. Se os nós permanecerem não íntegros após a operação de reparo inicial, as correções adicionais serão investigadas por engenheiros de AKS.
  
Se vários nós não estiverem íntegros durante uma verificação de integridade, cada nó será reparado individualmente antes do início de outro reparo.

## <a name="next-steps"></a>Próximas etapas

Use as [Zonas de Disponibilidade][availability-zones] para aumentar a HA com suas cargas de trabalho de cluster do AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
