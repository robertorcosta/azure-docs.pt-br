---
title: Cota de máquina virtual spot - Azure
description: Aumente os limites de cotas para VMs spot, que fornecem um modelo de uso do Azure que permite que você assuma custos mais baixos em troca de permitir que o Azure remova as VMs conforme necessário.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842745"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Cota spot: Limite de aumento para todas as séries de VM

As máquinas virtuais spot (VMs) fornecem um modelo diferente de uso do Azure. Eles permitem que você assuma custos mais baixos em troca de permitir que o Azure remova máquinas virtuais conforme necessário para implantações de instâncias de VM reservadas. Para obter mais informações sobre VMs spot, consulte [VMs spot do Azure para conjuntos de escala de máquinas virtuais](../../virtual-machine-scale-sets/use-spot.md).

O Azure Resource Manager suporta dois tipos de cotas de vCPU para máquinas virtuais:

* *VMs pay-as-you-go* e *instâncias vm reservadas* estão sujeitas a uma *cota padrão de vCPU*.
* *As VMs spot* estão sujeitas a uma *cota spot de vCPU*.

Para o tipo de cota spot vCPU, as cotas de vCPU do Gerenciador de Recursos são aplicadas em todas as séries de máquinas virtuais disponíveis como um único limite regional.

Sempre que você implantar uma nova VM spot, o uso total de vCPU novo e existente para todas as instâncias spot de VM não deve exceder o limite de cota spot vCPU aprovado. Se a cota de pontos for excedida, a implantação do VM local não é permitida.

Este artigo discute como solicitar um aumento no limite de cotas spot vCPU usando o portal Azure.

Para saber mais sobre as cotas padrão de vCPU, consulte [cotas de vCPU de máquinas virtuais](../../virtual-machines/windows/quotas.md) e limites de assinatura e serviço do [Azure, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Para saber mais sobre o aumento do limite de vCPU por região, consulte [Cota padrão: Aumentar limites por região](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Solicite um aumento do limite de cota do help + suporte

Para solicitar um aumento do limite de cota spot para todas as séries de máquinas virtuais usando **ajuda + suporte:**

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

1. Em **detalhes de Cotas,** faça as seguintes etapas:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **Locais,** selecione um local.

      ![Fornecer detalhes adicionais de cotas](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para o local selecionado, em **Tipos,** em **Selecionar um tipo,** escolha **Local**.

      ![Selecione o tipo de ponto](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Em **Tipos,** você pode solicitar tipos de cotas padrão e spot de um único caso de suporte através de suporte multisseletivo.

       Para obter mais informações, consulte [Cota padrão: Aumentar os limites por séries VM](per-vm-quota-requests.md).

   1. Digite o novo limite de cotas que você deseja para esta assinatura.

      ![Selecione uma nova cota para vm spot](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **Locais**e selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Especifique locais adicionais em detalhes de cotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Salvar e continuar** criando a solicitação de suporte.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Solicite um aumento do limite de cotas do painel Assinaturas

Para solicitar um aumento do limite de cota spot para todas as séries de VM do painel **Assinaturas:**

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

1. Selecione **A seguir: Soluções** para abrir **DETALHES DO PROBLEMA**. Selecione **Forneça detalhes** para inserir informações adicionais. Em **Detalhes de Cota,** digite as seguintes informações:

   1. Para **o modelo de implantação,** selecione o modelo apropriado e, para **Locais,** selecione um local.

      ![Fornecer detalhes da cota](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Para o local selecionado, em **Tipos,** em **Selecionar um tipo,** escolha **Local**.

      ![Selecione o tipo Spot](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Para obter mais informações, consulte [Cota padrão: Aumentar os limites por séries VM](per-vm-quota-requests.md).

   1. Digite o novo limite de cotas que você deseja para esta assinatura.

      ![Digite um novo valor para o limite de vCPU](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Para solicitar um aumento de cota para mais de um local, selecione um local adicional em **Locais**e selecione um tipo de VM apropriado. Em seguida, você pode inserir um limite que se aplica ao local adicional.

   ![Selecione locais adicionais em detalhes de cotas](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Selecione **Salvar e continuar** criando a solicitação de suporte.
