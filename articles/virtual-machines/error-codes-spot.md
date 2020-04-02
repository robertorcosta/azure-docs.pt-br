---
title: Códigos de erro para VMs do Azure Spot e instâncias de conjuntos de escala
description: Saiba mais sobre códigos de erro que você pode ver ao usar VMs spot e instâncias de conjunto de escala.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547808"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Mensagens de erro para VMs spot e conjuntos de escala

Aqui estão alguns possíveis códigos de erro que você poderia receber ao usar VMs spot e conjuntos de escala.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | O nível solicitado para\<\>recurso ' recurso ' não\<\>está atualmente\<disponível\>no local ' localização ' para assinatura ' subscriptionID '. Por favor, tente outro nível ou implante em um local diferente. | Não há capacidade suficiente do Azure Spot neste local para criar sua VM ou instância de conjunto de escalas. |
| Política de despejoCanBeSetOnlyOnAzureSpotMáquinas virtuais  |  A política de despejo só pode ser definida em Máquinas Virtuais do Azure Spot. | Esta VM não é uma VM spot, então você não pode definir a política de despejo. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  A Máquina Virtual do Azure Spot não é suportada no Conjunto de Disponibilidade. | Você precisa optar por usar uma VM spot ou usar uma VM em um conjunto de disponibilidade, você não pode escolher ambos. |
| AzureSpotFeatureNotEnabledForSubscription  |  Assinatura não ativada com recurso Do Azure Spot. | Use uma assinatura que suporte VMs Spot. |
| VMPriorityNãoBeAaplicado  |  O valor de{0}prioridade especificado ' ' não{1}pode ser aplicado à Máquina Virtual ' ' uma vez que nenhuma prioridade foi especificada quando a Máquina Virtual foi criada. | Especifique a prioridade quando a VM for criada. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Incapaz de realizar{0}a operação ' '{1} uma vez que o preço{2} máximo fornecido ' USD ' é{3}menor do que o preço spot atual ' USD ' para o tamanho do Azure Spot VM ' ' ' ' . | Selecione um preço máximo mais alto. Para obter mais informações, consulte informações sobre preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de preço máximo inválido. Os únicos valores suportados para o preço máximo são -1 ou um decimal maior que zero. O valor máximo do preço de -1 indica que a máquina virtual Azure Spot não será despejada por razões de preço. | Digite um preço máximo válido. Para obter mais informações, consulte preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | A mudança de preço máxima não{0}é permitida quando o VM ' ' está atualmente alocado. Por favor, desaloque e tente novamente. | Pare\Deloque o VM para que você possa alterar o preço máximo. |
| MaxPriceChangeNão permitido | A variação máxima de preço não é permitida. | Você não pode alterar o preço máximo para esta VM. |
| AzureSpotNãoésuportadoParaEstaAPIVersion  |  O Azure Spot não é suportado para esta versão da API. | A versão aPI precisa ser 2019-03-01. |
| AzureSpotNãoésuportadoParaIssoVMSize  |  O Azure Spot não é suportado para este tamanho {0}de VM . | Selecione outro tamanho de VM. Para obter mais informações, consulte [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotMáquinas virtuais  |  O preço máximo é suportado apenas para a Azure Spot Virtual Machines. | Para obter mais informações, consulte [Spot Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  A solicitação de recursos do Move contém uma máquina virtual Do Azure Spot. Não há suporte para esse recurso no momento. Verifique os detalhes de erro para ids de máquina virtual. | Você não pode mover VMs spot. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  A solicitação de recursos do Move contém um conjunto de escala de máquina virtual Do Azure Spot. Não há suporte para esse recurso no momento. Verifique os detalhes de erro para ids de conjunto de escala de máquina virtual. | Não é possível mover instâncias de conjunto de escala sumida do Spot. |
| EfêmerOsDiscosNãosuportadosParaSpotVMs | Os discos efêmeros do sistema operacional não são suportados para VMs spot. | Use um disco de sistema operacional regular para o Spot VM. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A Máquina Virtual do Azure Spot não é suportada no Virtual Machine Scale Set com o modo VM Orchestration. | Defina o modo de orquestração como conjunto de escala de máquina virtual para usar instâncias spot. |


**Próximos passos** Para obter mais informações, consulte [spot Virtual Machines](./linux/spot-vms.md).