---
title: Tutorial – reduzir os custos do Azure com recomendações de otimização | Microsoft Docs
description: Este tutorial o ajuda a reduzir os custos do Azure quando você age com base em recomendações de otimização.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229844"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Tutorial: Otimizar os custos usando recomendações

O Gerenciamento de Custos do Azure funciona com o Assistente do Azure para fornecer recomendações de otimização de custo. O Assistente do Azure ajuda você a otimizar e melhorar a eficiência identificando recursos ociosos e subutilizados. Este tutorial o conduz por um exemplo em que você identifica recursos do Azure subutilizados e, em seguida, você age para reduzir os custos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Exiba as recomendações de otimização de custos para exibir possíveis ineficiências de uso
> * Aja em uma recomendação para redimensionar uma máquina virtual para uma opção mais econômica
> * Verifique a ação para garantir que a máquina virtual foi redimensionada com êxito

## <a name="prerequisites"></a>Pré-requisitos
Recommendations are available for a variety of scopes and Azure account types. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Você precisa ter acesso de leitura a pelo menos um ou mais dos seguintes escopos para exibir os dados de custo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Subscription
- Resource group

Você deve ter máquinas virtuais ativas com pelo menos 14 dias de atividade.

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Exibir recomendações de otimização de custo

To view cost optimization recommendations for a subscription, open the desired scope in the Azure portal and select **Advisor recommendations**.

To view recommendations for a management group, open the desired scope in the Azure portal and select **Cost analysis** in the menu. Use the **Scope** pill to switch to a different scope, such as a management group. Select **Advisor recommendations** in the menu. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

![Recomendações do Consultor de Gerenciamento Custos mostradas no portal do Azure](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

A lista de recomendações identifica as ineficiências de uso ou mostra as recomendações de compra que podem ajudá-lo a economizar ainda mais dinheiro. As **economias anuais em potencial** totalizadas mostram o valor total que você poderá economizar se desligar ou desalocar todas as VMs que atendem às regras de recomendação. Se você não deseja desligá-las, deve considerar redimensioná-las para uma SKU de VM mais econômica.

A categoria **Impacto**, juntamente com **Economias anuais em potencial**, é projetada para ajudar a identificar as recomendações que têm o potencial de economizar o máximo possível.

High impact recommendations include:
- [Buy reserved virtual machine instances to save money over pay-as-you-go costs](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimize virtual machine spend by resizing or shutting down underutilized instances](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Use Standard Storage to store Managed Disks snapshots](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Medium impact recommendations include:
- [Delete Azure Data Factory pipelines that are failing](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduce costs by eliminating un-provisioned ExpressRoute circuits](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduce costs by deleting or reconfiguring idle virtual network gateways](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Agir com relação a uma recomendação

Azure Advisor monitors your virtual machine usage for seven days and then identifies underutilized virtual machines. As máquinas virtuais cuja utilização da CPU é de cinco por cento ou menos e cujo uso de rede é de sete MB ou menos durante quatro ou mais dias são consideradas máquinas virtuais de baixa utilização.

A configuração de utilização de CPU de 5% ou menos é padrão, mas você pode ajustar as configurações. Para obter mais informações sobre como ajustar a configuração, confira [Configurar a regra de utilização média da CPU para obter a recomendação de máquina virtual de baixo uso](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Embora alguns cenários possam resultar em baixa utilização por design, você geralmente pode economizar dinheiro alterando o tamanho de suas máquinas virtuais para tamanhos de menor custo. Suas economias reais poderão variar se você escolher uma ação de redimensionamento. Vamos examinar um exemplo de redimensionamento de uma máquina virtual.

Na lista de recomendações, clique na recomendação **Dimensionar corretamente ou desligar máquinas virtuais subutilizadas**. Na lista de candidatos a máquina virtual, escolha uma máquina virtual para redimensionar e, em seguida, clique na máquina virtual. Os detalhes da máquina virtual são mostrados para que você possa verificar as métricas de utilização. O valor **economias anuais em potencial** é o que você poderá economizar se desligar ou remover a VM. Redimensionar uma VM provavelmente economizará seu dinheiro, mas você não economizará o valor total das economias anuais em potencial.

![Exemplo dos detalhes da recomendação](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

Nos detalhes da VM, verifique a utilização da máquina virtual para confirmar se ela é um candidato adequado de redimensionamento.

![Detalhes da VM de exemplo mostrando a utilização histórica](./media/tutorial-acm-opt-recommendations/vm-details.png)

Observe o tamanho atual da máquina virtual. Depois de verificar que a máquina virtual deve ser redimensionada, feche os detalhes da VM para ver a lista de máquinas virtuais.

In the list of candidates to shut down or resize, select **Resize *&lt;FromVirtualMachineSKU&gt;* to *&lt;ToVirtualMachineSKU&gt;***.
![Recomendação de exemplo com a opção para redimensionar a máquina virtual](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Em seguida, será apresentada uma lista de opções de redimensionamento disponíveis. Escolha o que gerará o melhor desempenho e custo-benefício para seu cenário. In the following example, the option chosen resizes from **Standard_D8s_v3** to **Standard_D2s_v3**.

![Lista de exemplo dos tamanhos de VM disponíveis onde é possível escolher um tamanho](./media/tutorial-acm-opt-recommendations/choose-size.png)

After you choose a suitable size, click **Resize** to start the resize action.

O redimensionando requer uma máquina virtual ativamente em execução para reiniciar. Se a máquina virtual estiver em um ambiente de produção, recomendamos executar a operação de redimensionamento depois do horário comercial. O agendando do reinício pode reduzir interrupções causadas por indisponibilidade momentânea.

## <a name="verify-the-action"></a>Verifique a ação

Quando o redimensionamento da VM é concluído com êxito, uma notificação do Azure é mostrada.

![Notificação de êxito da máquina virtual redimensionada](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Próximos passos

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Exiba as recomendações de otimização de custos para exibir possíveis ineficiências de uso
> * Aja em uma recomendação para redimensionar uma máquina virtual para uma opção mais econômica
> * Verifique a ação para garantir que a máquina virtual foi redimensionada com êxito

Se você ainda não tiver lido o artigo de melhores práticas de Gerenciamento de Custos, saiba que ele apresenta diretrizes e princípios de alto nível a serem considerados para ajudar a gerenciar os custos.

> [!div class="nextstepaction"]
> [Práticas recomendadas de Gerenciamento de Custos](cost-mgt-best-practices.md)
