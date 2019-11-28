---
title: Tutorial – Prever despesas com o Cloudyn no Azure | Microsoft Docs
description: Neste tutorial, você aprende a prever despesas usando o histórico de uso e dados de gastos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: f0656d9454483c7ee76660050bbd067bef4c9296
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219073"
---
# <a name="tutorial-forecast-future-spending"></a>Tutorial: Prever despesas futuras

O Cloudyn ajuda você a prever as despesas futuras usando o histórico de uso e os dados de gastos. Você pode usar os relatórios da Cloudyn para exibir todos os dados de projeção de custos. Os exemplos neste tutorial orientarão na revisão das projeções de custos usando os relatórios. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prever despesas futuras

Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

- Você deve ter uma conta do Azure.
- Você precisa ter um registro de avaliação ou uma assinatura paga do Cloudyn.

## <a name="forecast-future-spending"></a>Prever despesas futuras

A Cloudyn inclui relatórios de projeção de custos para ajudá-lo a prever os gastos com base no seu uso ao longo do tempo. A finalidade principal dos relatórios é ajudá-lo a garantir que as tendências de custos não excedam as expectativas de sua organização. Os relatórios que você usa são o Custo projetado do mês atual e Custo projetado anual. Ambos mostram a projeção dos gastos futuros se o seu uso permanece relativamente consistente nos últimos 30 dias de uso.

O relatório de Custo projetado do mês atual mostra os custos dos seus serviços. Ele usa os custos desde o início do mês e os do mês anterior para mostrar o custo projetado. No menu de relatórios, na parte superior do portal, clique em **Custos** > **Projeção e Orçamento** > **Custo Projetado do Mês Atual**. A imagem a seguir mostra um exemplo.

![Informações de exemplo mostradas no relatório de custo projetado do mês atual](./media/tutorial-forecast-spending/project-month01.png)

No exemplo, você pode ver quais serviços gastaram mais. Os custos do Azure foram menores do que os custos do AWS. Se você quiser ver os detalhes da projeção de custos de VMs do Azure, na lista **Filtro**, selecione **Azure/VM**.

![Exemplo que mostra o Custo projetado do mês atual da VM do Azure](./media/tutorial-forecast-spending/project-month02.png)

Siga as mesmas etapas básicas anteriores para ver projeções de custo mensais de outros serviços nos quais você tenha interesse.

O relatório de Custo projetado anual mostra uma extrapolação do custo de seus serviços nos próximos 12 meses.

No menu de relatórios, na parte superior do portal, clique em **Custo** > **Projeção e Orçamento** > **Custo Projetado Anual**. A imagem a seguir mostra um exemplo.

![Exemplo mostrando o Relatório de custo anual projetado](./media/tutorial-forecast-spending/project-annual01.png)

No exemplo, você pode ver quais serviços gastaram mais. Assim como no exemplo mensal, os custos do Azure foram menores que os custos do AWS. Se você quiser ver os detalhes da projeção de custos de VMs do Azure, na lista **Filtro**, selecione **Azure/VM**.

![Exemplo mostrando o Custo anual projetado de VMs](./media/tutorial-forecast-spending/project-annual02.png)

Na imagem acima, o custo projetado anual das VMs do Azure é de $28.374.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Prever despesas futuras


Avança para o próximo tutorial para aprender como gerenciar os custos com relatórios de análise e de alocação de custos.

> [!div class="nextstepaction"]
> [Gerenciar os custos com relatórios de análise e de alocação de custos](tutorial-manage-costs.md)
