---
title: Solicite um aumento nos limites de cotas de vCPU por série Azure VM
description: Como solicitar um aumento no limite de cota de vCPU para uma série VM no portal Azure, o que aumenta o limite total de vCPU regional pelo mesmo valor.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843694"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Cota padrão: Aumentar os limites por séries VM

O Azure Resource Manager suporta dois tipos de cotas de vCPU para máquinas virtuais:

* *VMs pay-as-you-go* e *instâncias vm reservadas* estão sujeitas a uma *cota padrão de vCPU*.
* *As VMs spot* estão sujeitas a uma *cota spot de vCPU*.

A cota padrão de vCPU para instâncias de máquinas virtuais pay-as-you-go e reservadas é aplicada em dois níveis para cada assinatura em cada região:

* O primeiro nível é o *limite total de vCPUs regionais,* em todas as séries de VM.
* O segundo nível é o *limite vCPUs da série vCPUs por VM,* como os vCPUs da série Dv3.

Sempre que você implantar uma nova VM spot, o uso total de vCPU novo e existente para essa série VM não deve exceder a cota de vCPU aprovada para essa série VM em particular. Além disso, o número total de vCPUs novos e existentes que são implantados em todas as séries de VM não deve exceder a cota total de vCPU regional aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série VM usando o portal Azure. Um aumento na cota da série VM aumenta automaticamente o limite total de vCPU regional pelo mesmo valor.

Para saber mais sobre as cotas padrão de vCPU, consulte [cotas de vCPU de máquinas virtuais](../../virtual-machines/windows/quotas.md) e limites de assinatura e serviço do [Azure](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests).

Para saber mais sobre o aumento do limite de vCPU por região para cota padrão, consulte [Cota padrão: Aumentar limites por região](regional-quota-requests.md).

Para saber mais sobre o aumento dos limites spot vM vCPU, consulte [Cota spot: Aumentar os limites para todas as séries de VM](low-priority-quota.md).

Você pode solicitar um aumento nos limites padrão de cotas de vCPU por série VM em qualquer uma das duas maneiras, conforme descrito nas seções a seguir.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Solicite um aumento padrão de cota do suporte ajuda +

Para solicitar um aumento padrão da cota vCPU por série VM a partir do **suporte Help +**:

> [!NOTE]
> Você também pode solicitar um aumento do limite de cotas para várias regiões através de um único caso de suporte. Para mais detalhes, consulte o passo 8.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link ajuda + suporte](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Nova solicitação de suporte**.

    ![Crie uma nova solicitação de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para uma cota aumentada](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para **o tipo quota,** **selecione os aumentos do limite de assinatura de Computação-VM (núcleos-vCPUs).**

   ![Selecione um tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **A seguir: Soluções** para abrir **DETALHES DO PROBLEMA**. Selecione **Forneça detalhes** para inserir informações adicionais.

   ![O link "Fornecer detalhes"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Nos **detalhes da Cota,** faça as seguintes etapas:

   ![TFornecer detalhes adicionais de cotas](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para **o modelo de implantação,** selecione o modelo apropriado.

   1. Para **Locais,** selecione um local. Para o local selecionado, em **Tipos,** em **Selecionar um tipo,** escolha **Padrão**.

      ![Detalhes de cotas - tipos de cotas](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **Tipos,** você pode solicitar tipos de cotas padrão e spot de um único caso de suporte através de suporte multisseletivo.

      Para obter mais informações sobre o aumento dos limites de cotas spot, consulte [VMs spot do Azure para conjuntos de escala de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

   1. Em **Padrão,** selecione a série SKU para aumentar as cotas.

      ![Detalhes da cota - Série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Digite os novos limites de cotas que você deseja para esta assinatura. Para remover um SKU da sua lista, limpe a caixa de seleção ao lado do SKU ou selecione o ícone descarte "X".

      ![Selecione um novo limite de vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **Locais**e selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Especifique locais adicionais em detalhes de cotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Salvar e continuar** criando a solicitação de suporte.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Solicite um aumento padrão de cotas de Assinaturas

Para solicitar um aumento padrão da cota de vCPU por série VM a partir de **Assinaturas**:

> [!NOTE]
> Você também pode solicitar um aumento do limite de cotas para várias regiões através de um único caso de suporte. Para mais detalhes, consulte o passo 7.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas**.

   ![Assinaturas na pesquisa do portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Assinaturas para selecionar para alterações](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, selecione **Uso + cotas**.

   ![O link "Uso + cotas"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, **selecione Aumentar solicitação**.

   ![Selecione para aumentar a cota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Para **o tipo quota,** **selecione os aumentos do limite de assinatura de Computação-VM (núcleos-vCPUs).**

   ![Selecione um tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Nos **detalhes da Cota,** faça as seguintes etapas:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **Locais,** selecione um local.

      ![Fornecer detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. Para o local selecionado, em **Tipos,** **selecione Selecionar um tipo**e, em seguida, selecione **Padrão**.

      ![Selecione o tipo Padrão](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Em **Tipos,** você pode solicitar tipos de cotas padrão e spot de um único caso de suporte através de suporte multisseletivo.

      Para obter mais informações sobre o aumento dos limites de cotas spot, consulte [VMs spot do Azure para conjuntos de escala de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

   1. Para **Padrão,** selecione a série SKU cujas cotas você deseja aumentar.

      ![Detalhes da cota - Série SKU](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Digite os novos limites de cotas que você deseja para esta assinatura. Para remover um SKU da sua lista, desmarque a caixa de seleção ao lado do SKU ou selecione o ícone descarte "X".

      ![Selecione um novo limite de vCPU](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **Locais**e selecione um tipo de VM apropriado.

   Esta etapa pré-carrega a série SKU que você selecionou para locais anteriores. Digite os limites de cota que deseja aplicar à série adicional.

   ![Selecione locais adicionais em detalhes de cotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Salvar e continuar** criando a solicitação de suporte.
