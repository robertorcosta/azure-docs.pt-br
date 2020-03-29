---
title: Solicite um aumento nos limites regionais de cotas de vCPU do Azure
description: Como solicitar um aumento no limite de cota de vCPU para uma região no portal Azure.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843677"
---
# <a name="standard-quota-increase-limits-by-region"></a>Cota padrão: Aumentar os limites por região

O Azure Resource Manager suporta dois tipos de cotas de vCPU para máquinas virtuais:

* *VMs pay-as-you-go* e *instâncias vm reservadas* estão sujeitas a uma *cota padrão de vCPU*.
* *As VMs spot* estão sujeitas a uma *cota spot de vCPU*.

A cota padrão de vCPU para instâncias de máquinas virtuais pay-as-you-go e reservadas é aplicada em dois níveis para cada assinatura em cada região:

* O primeiro nível é o *limite total de vCPUs regionais,* em todas as séries de VM.
* O segundo nível é o *limite vCPUs da série vCPUs por VM,* como os vCPUs da série D.

Sempre que você implantar uma nova VM spot, o uso total de vCPU novo e existente para essa série VM não deve exceder a cota de vCPU aprovada para essa série VM em particular. Além disso, o número total de vCPUs novos e existentes que são implantados em todas as séries de VM não deve exceder a cota total de vCPU regional aprovada para a assinatura. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida.

Você pode solicitar um aumento no limite de cota de vCPU para a série VM usando o portal Azure. Um aumento na cota da série VM aumenta automaticamente o limite total de vCPU regional pelo mesmo valor.

Quando você cria uma nova assinatura, o número total padrão de vCPUs regionais pode não ser igual à cota total de vCPU padrão para todas as séries de VM individuais. Essa discrepância pode resultar em uma assinatura com cota suficiente para cada série de VM individual que você deseja implantar. Mas pode não haver cota suficiente para acomodar o total de vCPUs regionais para todas as implantações. Neste caso, você deve apresentar uma solicitação para aumentar explicitamente o limite do número total de vCPUs regionais. O limite total de vCPU regional não pode exceder o contingente total aprovado em todas as séries de VM para a região.

Para saber mais sobre as cotas padrão de vCPU, consulte [cotas de vCPU de máquinas virtuais](../../virtual-machines/windows/quotas.md) e limites de assinatura e serviço do [Azure, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Para saber mais sobre o aumento dos limites spot vM vCPU, consulte [Cota spot: Aumentar os limites para todas as séries de VM](low-priority-quota.md).

Você pode solicitar um aumento no limite de cotas padrão vCPU por região em qualquer uma das duas maneiras.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Solicite um aumento de cota por região a partir do suporte Ajuda +

Para solicitar um aumento da cota de vCPU por região a partir do **suporte ajuda + :**

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![O link "Ajuda + suporte"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Em **Ajuda + suporte,** selecione **Nova solicitação de suporte**.

    ![Nova solicitação de suporte](./media/resource-manager-core-quotas-request/new-support-request.png)

1. Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)**.

   ![Selecione um tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Para **Assinatura**, selecione a assinatura cuja cota você deseja aumentar.

   ![Selecionar uma assinatura](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para o **tipo Cota,** selecione **Outras solicitações**.

   ![Selecione um tipo de cota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **A seguir: Soluções** para abrir **DETALHES DO PROBLEMA**. Em **Descrição,** forneça as seguintes informações:

    1. Para **o modelo de implantação,** especifique o **gerenciador de recursos**.  
    1. Para **Região,** especifique sua região necessária, por exemplo, **Leste dos EUA 2**.  
    1. Para **Novo Limite,** especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para séries Individuais SKU para esta assinatura.

    ![Insira detalhes para a solicitação de cotas](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **'Revisar + criar para** continuar criando a solicitação de suporte'.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Solicite um aumento de cota por região a partir de Assinaturas

Para solicitar um aumento da cota de vCPU por região a partir de **Assinaturas**:

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas**.

   ![Acesse as assinaturas do portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione uma assinatura para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. No painel esquerdo, selecione **Uso + cotas**.

   ![Siga o link Uso e cotas](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, **selecione Aumentar solicitação**.

   ![Selecione para aumentar a cota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Do **tipo Cota,** selecione **Outras solicitações**.

   ![Selecione o tipo de cota](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Selecione **A seguir: Soluções** para abrir **DETALHES DO PROBLEMA**. Na caixa **Descrição,** forneça as seguintes informações adicionais:

    1. Para **o modelo de implantação,** especifique o **gerenciador de recursos**.  
    1. Para **Região,** especifique sua região necessária, por exemplo, **Leste dos EUA 2**.  
    1. Para **Novo Limite,** especifique um novo limite de vCPU para a região. Esse valor não deve exceder a soma das cotas aprovadas para séries Individuais SKU para esta assinatura.

    ![Insira informações em detalhes](./media/resource-manager-core-quotas-request/regional-details.png)

1. Selecione **'Revisar + criar para** continuar criando a solicitação de suporte'.
