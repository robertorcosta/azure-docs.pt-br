---
title: Limites para recursos, SKUs, regiões
titleSuffix: Azure Kubernetes Service
description: Conheça as cotas padrão, os tamanhos de SKU de nó restrito e a disponibilidade da região do Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 054d6ff4fc105d84192ac81feda97515f6cfae49
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886765"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Cotas, restrições de tamanho de máquina virtual e disponibilidade de região no Azure Kubernetes Service (AKS)

Todos os serviços do Azure estabelecem limites de inadimplência e cotas para recursos e recursos. Algumas SKUs de máquina virtual (VM) também estão restritas para uso.

Este artigo detalha os limites de recursos padrão para os recursos do Azure Kubernetes Service (AKS) e a disponibilidade de AKS nas regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura provisionada

Todas as outras limitações de rede, computação e armazenamento se aplicam à infraestrutura provisionada. Para obter os limites de serviço e assinatura relevantes, consulte os limites de assinatura e serviço do [Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Quando você atualiza um cluster AKS, recursos adicionais são temporariamente consumidos. Esses recursos incluem endereços IP disponíveis em uma sub-rede virtual ou cota de vCPU de máquina virtual. Se você usar contêineres do Windows Server (atualmente em pré-visualização no AKS), a única abordagem endossada para aplicar as últimas atualizações aos números é executar uma operação de upgrade. Um processo de atualização de cluster com falha pode indicar que você não tem o espaço de endereço IP disponível ou a cota de vCPU para lidar com esses recursos temporários. Para obter mais informações sobre o processo de atualização do nó do Windows Server, consulte [Atualizar um pool de nós no AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tamanhos restritos de VM

Cada nó em um cluster AKS contém uma quantidade fixa de recursos computacionais, como vCPU e memória. Se um nó AKS contiver recursos computacionais insuficientes, os pods podem não funcionar corretamente. Para garantir que os pods do *sistema kube* e seus aplicativos possam ser programados de forma confiável, **não use as seguintes SKUs VM em AKS**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre os tipos de VM e seus recursos de computação, consulte [Tamanhos para máquinas virtuais no Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade de região

Para obter a lista mais recente de onde você pode implantar e executar clusters, consulte [a disponibilidade da região AKS][region-availability].

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Se o seu recurso suportar um aumento, solicite o aumento através de uma [solicitação de suporte do Azure][azure-support] (para o tipo **Demissão**, selecione **Cota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
