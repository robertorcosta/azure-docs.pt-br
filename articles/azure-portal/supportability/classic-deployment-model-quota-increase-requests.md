---
title: modelo de implantação clássico do Azure
description: O modelo de implantação Classic, agora substituído pelo modelo Resource Manager, impõe um limite global de cotas de vCPU para VMs e conjuntos de escala de máquinas virtuais.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835516"
---
# <a name="classic-deployment-model"></a>Modelo de implantação clássica

O modelo de implantação Classic é o modelo de implantação da geração mais antiga do Azure. Ele impõe um limite global de cotas de vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais. O modelo de implantação Classic não é mais recomendado e agora é substituído pelo modelo Resource Manager.

Para saber mais sobre esses dois modelos de implantação e as vantagens de usar o Resource Manager, consulte [Gerenciador de recursos e implantação clássica](../../azure-resource-manager/management/deployment-models.md).

Quando uma nova assinatura é criada, uma cota padrão de vCPUs é atribuída a ela. Sempre que uma nova máquina virtual for implantada usando o modelo de implantação Classic, a soma do uso de vCPU novo e existente em todas as regiões não deve exceder a cota de vCPU aprovada para o modelo de implantação Classic.

Para saber mais sobre cotas, consulte os limites de assinatura e serviço do [Azure, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Você pode solicitar um aumento no limite de cotas de vCPU para o modelo de implantação Classic. Use **ajuda + suporte** ou **uso + cotas** no portal Azure.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Solicitação por série VM vCPU aumento de cota em nível de assinatura usando ajuda + suporte

Siga as instruções abaixo para criar uma solicitação de suporte usando o **suporte Help +** no portal Azure.

1. No menu do [portal Azure,](https://portal.azure.com) selecione **Ajuda + suporte**.

   ![Selecione ajuda + suporte no portal Azure](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Selecione **Nova solicitação de suporte**.

   ![Crie uma nova solicitação de suporte no portal Azure](./media/resource-manager-core-quotas-request/new-support-request.png)

1. No **tipo Emissão,** escolha **Limites de serviço e assinatura (cotas)**.

   ![Selecione cotas como o tipo de problema](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione a assinatura para a qual aumentar uma cota](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Para **o tipo quota,** **selecione Computação -VM (núcleos-vCPUs) aumentos do limite de assinatura**.

   ![Selecione o tipo de cota para aumentar](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **A seguir: Soluções** para abrir **DETALHES DO PROBLEMA**. Selecione **Forneça detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para ajudar sua solicitação junto](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes de Cotas,** selecione **Clássico** e selecione **um Local**.

   ![Adicionar detalhes, incluindo modelo de implantação e localização](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Para **a família SKU,** selecione uma ou mais famílias SKU para aumentar.

   ![Especifique a família SKU para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o SKU da **família SKU** ou selecione o ícone descarte "X". Depois de inserir uma cota para cada família SKU, **selecione Salvar e Continuar** em **Cotas** para continuar com a solicitação de suporte.

   ![Solicite novos limites](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Solicitação por série VM vCPU aumento de cota em nível de assinatura usando uso + cotas

Siga as instruções abaixo para criar uma solicitação de suporte usando **use + cotas** no portal Azure.

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Assinaturas**.

   ![Acesse as assinaturas do portal Azure](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Selecione a assinatura cuja cota você deseja aumentar.

   ![Selecione a assinatura para modificar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Selecione **Uso + cotas**.

   ![Selecione o uso e as cotas para uma assinatura](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. No canto superior direito, selecione **Solicitar Aumento**.

   ![Selecione para aumentar a cota](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Selecione **O limite de assinatura de Compute-VM (núcleos-vCPUs) aumenta** à medida que o tipo de **Cota**.

   ![Selecione o tipo de cota](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Selecione **A seguir: Soluções** para abrir **DETALHES DO PROBLEMA**. Selecione **Forneça detalhes** para fornecer informações adicionais.

   ![Forneça detalhes para sua solicitação](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. Em **detalhes de Cotas,** selecione **Clássico** e um **Local**.

   ![Selecione detalhes de cotas, incluindo modelo de implantação e localização](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Selecione uma ou mais famílias SKU para um aumento.

   ![Selecione a família SKU para aumentar](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Insira os novos limites desejados na assinatura. Para remover uma linha, desmarque o SKU da **família SKU** ou selecione o ícone descarte "X". Depois de inserir uma cota para cada família SKU, **selecione Salvar e Continuar** em **Cotas** para continuar com a solicitação de suporte.

   ![Insira nova cota](./media/resource-manager-core-quotas-request/new-limits-classic.png)

