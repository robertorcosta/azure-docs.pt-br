---
title: Planejar e gerenciar custos para Azure Cosmos DB
description: Saiba como planejar e gerenciar custos para Azure Cosmos DB usando a análise de custo no portal do Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152577"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planejar e gerenciar custos para Azure Cosmos DB

Este artigo descreve como você pode planejar e gerenciar custos para Azure Cosmos DB. Primeiro, você usa a calculadora de capacidade de Azure Cosmos DB para ajudar a planejar os custos antes de adicionar qualquer recurso. Em seguida, ao adicionar os recursos do Azure, você pode examinar os custos estimados. Depois de começar a usar Azure Cosmos DB recursos, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir.

Entenda que os custos de Azure Cosmos DB são apenas uma parte dos custos mensais em sua fatura do Azure. Se estiver usando outros serviços do Azure, você será cobrado por todos os serviços e recursos do Azure usados na sua assinatura do Azure, incluindo os serviços de terceiros. Este artigo explica como planejar e gerenciar custos para Azure Cosmos DB. Depois de estar familiarizado com o gerenciamento de custos para Azure Cosmos DB, você pode aplicar métodos semelhantes para gerenciar os custos de todos os serviços do Azure usados em sua assinatura.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte a diferentes tipos de conta do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Examinar os custos estimados com a calculadora de capacidade

Use a [calculadora de capacidade de Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) para estimar os custos antes de criar os recursos em uma conta do Azure Cosmos. A calculadora de capacidade é usada para obter uma estimativa da taxa de transferência e custo necessários de sua carga de trabalho. Configurar seus bancos de dados do Azure Cosmos e contêineres com a quantidade certa de taxa de transferência provisionada ou de [unidades de solicitação (ru/s)](request-units.md)para sua carga de trabalho é essencial para otimizar o custo e o desempenho. Você precisa inserir detalhes como tipo de API, número de regiões, tamanho do item, solicitações de leitura/gravação por segundo, total de dados armazenados para obter uma estimativa de custo. Para saber mais sobre a calculadora de capacidade, consulte o artigo de [estimativa](estimate-ru-with-capacity-planner.md) .

A captura de tela a seguir mostra a taxa de transferência e estimativa de custo usando a calculadora de capacidade:

![Estimativa de custo na calculadora de capacidade Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Examinar os custos estimados do portal do Azure

Ao criar Azure Cosmos DB recursos do portal do Azure, você pode ver os custos estimados. Use as seguintes etapas para examinar a estimativa de custo:

1. Entre no portal do Azure e navegue até sua conta do Azure Cosmos.
1. Vá para a **Data Explorer**.
1. Crie um novo contêiner, como um contêiner de grafo.
1. Insira a taxa de transferência necessária para sua carga de trabalho, como 400 RU/s. Depois de inserir o valor da taxa de transferência, você poderá ver a estimativa de preços, conforme mostrado na seguinte captura de tela:

   ![Estimativa de custo no portal do Azure](./media/plan-manage-costs/cost-estimate-portal.png)

Se sua assinatura do Azure tiver um limite de gastos, o Azure impedirá que você gaste em seu valor de crédito. Conforme você cria e usa os recursos do Azure, seus créditos são usados. Quando atingir seu limite de crédito, os recursos implantados serão desabilitados para o restante desse período de cobrança. Você não pode alterar seu limite de crédito, mas pode removê-lo. Para obter mais informações sobre limites de gastos, consulte [limite de gastos do Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Usar orçamentos e alertas de custo

É possível criar [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerenciar custos e criar alertas que notificam automaticamente os stakeholders de anomalias de gastos e risco de gastos em excesso. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. No entanto, eles podem ter funcionalidade limitada para gerenciar custos de serviço do Azure individuais como o custo de Azure Cosmos DB porque eles foram projetados para controlar os custos em um nível mais alto.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar recursos com Azure Cosmos DB, você incorre em custos. Os custos da unidade de uso de recursos variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso da unidade de solicitação. Assim que o uso de Azure Cosmos DB é iniciado, os custos são incorridos e você pode vê-los no painel de [análise de custo](../cost-management/quick-acm-cost-analysis.md) na portal do Azure.

Ao usar a análise de custo, você pode exibir os custos de Azure Cosmos DB em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, atual, mês anterior e ano. Você também pode exibir os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos. Para exibir os custos de Azure Cosmos DB na análise de custo:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra a janela **Gerenciamento de custos + cobrança** , selecione **Gerenciamento de custos** no menu e, em seguida, selecione análise de **custo**. Em seguida, você pode alterar o escopo de uma assinatura específica da lista suspensa **escopo** .

1. Por padrão, o custo de todos os serviços é mostrado no primeiro gráfico de rosca. Selecione a área no gráfico rotulada como "Azure Cosmos DB".

1. Para restringir os custos de um único serviço, como Azure Cosmos DB, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, escolha **Azure Cosmos DB** na lista. Veja um exemplo que mostra os custos para apenas Azure Cosmos DB:
 
   ![Monitorar custos com o painel de análise de custo](./media/plan-manage-costs/cost-analysis-pane.png)

No exemplo anterior, você verá o custo atual de Azure Cosmos DB para o mês de fev. Os gráficos também contêm Azure Cosmos DB custos por local e por grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para saber mais sobre como os preços funcionam no Azure Cosmos DB:

* [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md)
* [Otimizar o custo de armazenamento no Azure Cosmos DB](optimize-cost-storage.md)