---
title: Planejar e gerenciar custos para o armazenamento do Azure
description: Saiba como planejar e gerenciar os custos do armazenamento do Azure usando a análise de custo no portal do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78304518"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planejar e gerenciar custos para o armazenamento do Azure

Este artigo descreve como planejar e gerenciar os custos do armazenamento do Azure. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos de armazenamento antes de adicionar qualquer recurso. Depois de começar a usar os recursos de armazenamento do Azure, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e monitorar as tendências de gastos para identificar as áreas em que talvez queira agir.

Tenha em mente que os custos do armazenamento do Azure são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar os custos do armazenamento do Azure, você será cobrado por todos os serviços e recursos do Azure usados para sua assinatura do Azure, incluindo os serviços de terceiros. Depois de estar familiarizado com o gerenciamento de custos para o armazenamento do Azure, você pode aplicar métodos semelhantes para gerenciar os custos de todos os serviços do Azure usados em sua assinatura.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte a diferentes tipos de conta do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../../cost-management-billing/costs/understand-cost-mgt-data.md). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Estimar os custos antes de criar uma conta de armazenamento do Azure

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar e começar a transferir dados para uma conta de armazenamento do Azure.

1. Na página [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , escolha o bloco **contas de armazenamento** .

2. Role a página e localize a seção **contas de armazenamento** da sua estimativa.

3. Escolha opções nas listas suspensas. 

   Conforme você modifica o valor dessas listas suspensas, a estimativa de custo é alterada. Essa estimativa aparece no canto superior, bem como na parte inferior da estimativa. 
    
   ![Monitorar custos com o painel de análise de custo](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Conforme você altera o valor da lista suspensa **tipo** , outras opções que aparecem nessa planilha também são alteradas. Use os links na seção **mais informações** para saber mais sobre o que cada opção significa e como essas opções afetam o preço das operações relacionadas ao armazenamento. 

4. Modifique as opções restantes para ver seus efeitos em sua estimativa.

## <a name="use-budgets-and-cost-alerts"></a>Usar orçamentos e alertas de custo

É possível criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) para gerenciar custos e criar alertas que notificam automaticamente os stakeholders de anomalias de gastos e risco de gastos em excesso. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. No entanto, eles podem ter funcionalidade limitada para gerenciar os custos de serviço do Azure individuais como o custo do armazenamento do Azure, pois eles foram projetados para controlar os custos em um nível mais alto.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar os recursos do Azure com o armazenamento do Azure, você incorre em custos. Os custos da unidade de uso de recursos variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade (bytes, megabytes e assim por diante). Os custos são incorridos assim que o uso do armazenamento do Azure é iniciado. Você pode ver os custos no painel [análise de custo](../../cost-management-billing/costs/quick-acm-cost-analysis.md) na portal do Azure.

Ao usar a análise de custo, você pode exibir os custos de armazenamento do Azure em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também pode exibir os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.

Para exibir os custos de armazenamento do Azure na análise de custo:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Abra a janela **Gerenciamento de custos + cobrança** , selecione **Gerenciamento de custos** no menu e, em seguida, selecione análise de **custo**. Em seguida, você pode alterar o escopo de uma assinatura específica da lista suspensa **escopo** .

   ![Monitorar custos com o painel de análise de custo](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Para exibir apenas os custos do armazenamento do Azure, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, escolha **armazenamento** na lista. 

   Veja um exemplo que mostra os custos apenas para o armazenamento do Azure:

   ![Monitorar os custos de armazenamento com o painel análise de custo](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

No exemplo anterior, você verá o custo atual do serviço. Os custos pelas regiões do Azure (locais) e por grupo de recursos também são exibidos.  

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como gerenciar custos com [análise de custo](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Consulte os artigos a seguir para saber mais sobre como os preços funcionam com o armazenamento do Azure:

- [Preço da visão geral do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Otimizar custos para o armazenamento de blobs com capacidade reservada](../blobs/storage-blob-reserved-capacity.md)
