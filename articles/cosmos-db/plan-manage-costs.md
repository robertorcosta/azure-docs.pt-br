---
title: Planeje e gerencie custos para o Azure Cosmos DB
description: Aprenda a planejar e gerenciar custos para o Azure Cosmos DB usando a análise de custos no portal Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152577"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planeje e gerencie custos para o Azure Cosmos DB

Este artigo descreve como você pode planejar e gerenciar custos para o Azure Cosmos DB. Primeiro, você usa a calculadora de capacidade do Azure Cosmos DB para ajudar a planejar os custos antes de adicionar quaisquer recursos. Em seguida, à medida que você adiciona os recursos do Azure, você pode rever os custos estimados. Depois de começar a usar os recursos do Azure Cosmos DB, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer atuar.

Entenda que os custos para o Azure Cosmos DB são apenas uma parte dos custos mensais em sua conta do Azure. Se você estiver usando outros serviços do Azure, você será cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros. Este artigo explica como planejar e gerenciar custos para o Azure Cosmos DB. Depois de estar familiarizado com o gerenciamento de custos para o Azure Cosmos DB, você pode aplicar métodos semelhantes para gerenciar custos para todos os serviços do Azure usados em sua assinatura.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte a diferentes tipos de conta do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Revisar custos estimados com calculadora de capacidade

Use a [calculadora de capacidade do Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) para estimar custos antes de criar os recursos em uma conta do Azure Cosmos. A calculadora de capacidade é usada para obter uma estimativa do throughput e custo necessários de sua carga de trabalho. Configurar seus bancos de dados e contêineres do Azure Cosmos com a quantidade certa de throughput provisionado, ou [Unidades de Solicitação (RU/s)](request-units.md), para sua carga de trabalho é essencial para otimizar o custo e o desempenho. Você tem que inserir detalhes como tipo de API, número de regiões, tamanho do item, solicitações de leitura/gravação por segundo, dados totais armazenados para obter uma estimativa de custo. Para saber mais sobre a calculadora de capacidade, consulte o artigo [de estimativa.](estimate-ru-with-capacity-planner.md)

A captura de tela a seguir mostra a estimativa de throughput e custo usando a calculadora de capacidade:

![Estimativa de custos na calculadora de capacidade do Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Revisar os custos estimados do portal Azure

Ao criar recursos do Azure Cosmos DB a partir do portal Azure, você pode ver os custos estimados. Use as seguintes etapas para revisar a estimativa de custos:

1. Entre no portal do Azure e navegue até sua conta do Azure Cosmos.
1. Vá para o **Data Explorer**.
1. Crie um novo recipiente, como um recipiente gráfico.
1. Insira o throughput necessário para sua carga de trabalho, como 400 RU/s. Depois de inserir o valor de throughput, você pode ver a estimativa de preços mostrada na captura de tela a seguir:

   ![Estimativa de custos no portal Azure](./media/plan-manage-costs/cost-estimate-portal.png)

Se a sua assinatura do Azure tiver um limite de gastos, o Azure impede que você gaste mais do que o seu valor de crédito. À medida que você cria e usa os recursos do Azure, seus créditos são usados. Quando atingir o limite de crédito, os recursos que você implantou são desativados para o resto desse período de faturamento. Você não pode mudar seu limite de crédito, mas você pode removê-lo. Para obter mais informações sobre os limites de gastos, consulte [o limite de gastos do Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Usar orçamentos e alertas de custo

É possível criar [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerenciar custos e criar alertas que notificam automaticamente os stakeholders de anomalias de gastos e risco de gastos em excesso. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, por isso são úteis como parte de uma estratégia global de monitoramento de custos. No entanto, eles podem ter funcionalidade limitada para gerenciar custos individuais de serviço do Azure, como o custo do Azure Cosmos DB, porque eles são projetados para rastrear custos em um nível mais alto.

## <a name="monitor-costs"></a>Monitorar custos

À medida que você usa recursos com o Azure Cosmos DB, você incorre em custos. Os custos da unidade de uso de recursos variam de acordo com intervalos de tempo (segundos, minutos, horas e dias) ou por solicitação de uso da unidade. Assim que o uso do Azure Cosmos DB começa, os custos são incorridos e você pode vê-los no painel [de análise](../cost-management/quick-acm-cost-analysis.md) de custos no portal Azure.

Ao usar a análise de custos, você pode visualizar os custos do Azure Cosmos DB em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são por dia, atual, mês anterior e ano. Você também pode ver os custos com orçamentos e custos previstos. Mudar para visualizações mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde os gastos excessivos podem ter ocorrido. Se você criou orçamentos, você também pode facilmente ver onde eles excederam. Para ver os custos do Azure Cosmos DB na análise de custos:

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. Abra a janela **Gerenciamento de Custos + Faturamento,** selecione Gerenciamento de **custos** no menu e selecione Análise **de custos**. Em seguida, você pode alterar o escopo de uma assinatura específica **a** partir da gota de escopo.

1. Por padrão, o custo para todos os serviços é mostrado no primeiro gráfico de donuts. Selecione a área no gráfico rotulado "Azure Cosmos DB".

1. Para reduzir os custos para um único serviço, como o Azure Cosmos DB, selecione **Adicionar filtro** e, em seguida, selecione Nome **de serviço**. Em seguida, escolha **Azure Cosmos DB** da lista. Aqui está um exemplo mostrando os custos apenas para o Azure Cosmos DB:
 
   ![Monitore os custos com painel de Análise de Custos](./media/plan-manage-costs/cost-analysis-pane.png)

No exemplo anterior, você vê o custo atual para o Azure Cosmos DB para o mês de fevereiro. Os gráficos também contêm os custos do Azure Cosmos DB por localização e por grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir para saber mais sobre como funciona o preço no Azure Cosmos DB:

* [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md)
* [Otimizar o custo de armazenamento no Azure Cosmos DB](optimize-cost-storage.md)