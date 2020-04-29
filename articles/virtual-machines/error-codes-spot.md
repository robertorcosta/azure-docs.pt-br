---
title: Códigos de erro para VMs de ponto do Azure e instâncias de conjuntos de dimensionamento
description: Saiba mais sobre os códigos de erro que você poderia ver ao usar VMs Spot e instâncias do conjunto de dimensionamento.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547808"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Mensagens de erro para VMs Spot e conjuntos de dimensionamento

Aqui estão alguns códigos de erro possíveis que você pode receber ao usar VMs Spot e conjuntos de dimensionamento.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | A camada solicitada para o\<recurso\>' Resource ' não está disponível no momento\<no\>local ' location '\<para a\>assinatura ' SubscriptionId '. Tente outra camada ou implante em um local diferente. | Não há capacidade do Azure Spot suficiente neste local para criar sua instância de conjunto de dimensionamento ou VM. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A política de remoção pode ser definida somente em máquinas virtuais de ponto do Azure. | Essa VM não é uma VM Spot, portanto, você não pode definir a política de remoção. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Não há suporte para a máquina virtual do Azure spot no conjunto de disponibilidade. | Você precisa optar por usar uma VM spot ou usar uma VM em um conjunto de disponibilidade, não pode escolher ambas. |
| AzureSpotFeatureNotEnabledForSubscription  |  Assinatura não habilitada com o recurso Azure Spot. | Use uma assinatura que dê suporte a VMs pontuais. |
| VMPriorityCannotBeApplied  |  O valor de prioridade especificado{0}' ' não pode ser aplicado à máquina virtual{1}' ', pois nenhuma prioridade foi especificada quando a máquina virtual foi criada. | Especifique a prioridade quando a VM for criada. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Não é possível executar a{0}operação ' ', pois o preço{1} máximo fornecido ' USD ' é inferior ao preço spot{2} atual ' USD ' para o tamanho de{3}VM do Azure spot ' '. | Selecione um preço máximo mais alto. Para obter mais informações, consulte informações de preço para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de preço máximo inválido. Os únicos valores com suporte para o preço máximo são-1 ou um decimal maior que zero. O valor de preço máximo de-1 indica que a máquina virtual do Azure Spot não será removida por motivos de preço. | Insira um preço máximo válido. Para obter mais informações, consulte preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | A alteração máxima de preço não é permitida quando a{0}VM ' ' está alocada no momento. Desaloque e tente novamente. | Stop\Deallocate a VM para que você possa alterar o preço máximo. |
| MaxPriceChangeNotAllowed | A alteração máxima de preço não é permitida. | Você não pode alterar o preço máximo desta VM. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Não há suporte para o Azure Spot nesta versão de API. | A versão da API precisa ser 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  O Azure Spot não tem suporte para esse tamanho {0}de VM. | Selecione outro tamanho de VM. Para obter mais informações, consulte [máquinas virtuais Spot](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  O preço máximo tem suporte apenas para máquinas virtuais do Azure Spot. | Para obter mais informações, consulte [máquinas virtuais Spot](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  A solicitação mover recursos contém uma máquina virtual de ponto do Azure. Não há suporte para esse recurso no momento. Verifique os detalhes do erro para obter as IDs da máquina virtual. | Não é possível mover VMs pontuais. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  A solicitação mover recursos contém um conjunto de dimensionamento de máquinas virtuais do Azure Spot. Não há suporte para esse recurso no momento. Verifique os detalhes do erro para as IDs do conjunto de dimensionamento de máquinas virtuais. | Não é possível mover as instâncias do conjunto de escala Spot. |
| EphemeralOSDisksNotSupportedForSpotVMs | Discos do sistema operacional efêmero não têm suporte para VMs Spot. | Use um disco do sistema operacional normal para sua VM Spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Não há suporte para a máquina virtual de ponto do Azure no conjunto de dimensionamento de máquinas virtuais com o modo de orquestração de VM. | Defina o modo de orquestração como conjunto de dimensionamento de máquinas virtuais para usar instâncias especiais. |


**Próximas etapas** Para obter mais informações, consulte [máquinas virtuais Spot](./linux/spot-vms.md).