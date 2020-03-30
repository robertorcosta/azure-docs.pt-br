---
title: Reparando automaticamente os áldenos do Azure Kubernetes Service (AKS)
description: Saiba mais sobre a funcionalidade de reparo automático do nó e como o AKS corrige os nódulos de trabalhador quebrados.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284833"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Reparo automático do nó Azure Kubernetes Service (AKS)

A AKS verifica continuamente o estado de saúde dos nódulos dos trabalhadores e realiza o reparo automático dos nódulos se eles se tornarem insalubres. Esta documentação descreve como o Azure Kubernetes Service (AKS) monitora os nódulos dos trabalhadores e repara os nódulos de trabalhadores insalubres.  A documentação é para informar os operadores da AKS sobre o comportamento da funcionalidade de reparo do nó. Também é importante notar que a plataforma Azure [realiza manutenção em Máquinas Virtuais][vm-updates] que experimentam problemas. AKS e a Azure trabalham juntas para minimizar interrupções de serviço para seus clusters.

> [!Important]
> A funcionalidade de reparo automático do nó não é suportada no momento para pools de nó do Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como a AKS verifica os nódulos insalubres

> [!Note]
> A KS toma medidas de reparo em nomes com a conta de usuário **aks-remediator**.

A AKS usa regras para determinar se um nó é um estado insalubre e precisa ser reparado. A AKS usa as seguintes regras para determinar se o reparo automático é necessário.

* O nó informa o status do **NotReady** em verificações consecutivas dentro de um prazo de 10 minutos
* O nó não informa um status dentro de 10 minutos

Você pode verificar manualmente o estado de saúde de seus nódulos com kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como funciona o reparo automático

> [!Note]
> A KS toma medidas de reparo em nomes com a conta de usuário **aks-remediator**.

Este comportamento é para **conjuntos de escala de máquinas virtuais**.  O reparo automático dá várias etapas para reparar um nódulo quebrado.  Se um nó é determinado como insalubre, a AKS tenta várias etapas de remediação.  As etapas são executadas nesta ordem:

1. Depois que o tempo de execução do contêiner fica sem resposta por 10 minutos, os serviços de tempo de execução falhando são reiniciados no nó.
2. Se o nó não estiver pronto dentro de 10 minutos, o nó será reiniciado.
3. Se o nó não estiver pronto dentro de 30 minutos, o nó será re-re-imagem.

> [!Note]
> Se vários nós não são saudáveis, eles são reparados um por um

## <a name="next-steps"></a>Próximas etapas

Use [Zonas de disponibilidade][availability-zones] para aumentar a alta disponibilidade com suas cargas de trabalho de cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
