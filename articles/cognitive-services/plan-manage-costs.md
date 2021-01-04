---
title: Planejar o gerenciamento de custos para serviços cognitivas do Azure
description: Saiba como planejar e gerenciar os custos dos serviços cognitivas do Azure usando a análise de custo no portal do Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 48e111ac8b2b4fd3c1e2fee568e20699896dfff3
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705757"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planejar e gerenciar custos para serviços cognitivas do Azure

Este artigo descreve como planejar e gerenciar os custos dos serviços cognitivas do Azure. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos de serviços cognitivas antes de adicionar qualquer recurso ao serviço para estimar os custos. Em seguida, ao adicionar recursos do Azure, examine os custos estimados. Depois de começar a usar os recursos de serviços cognitivas (por exemplo, fala, Pesquisa Visual Computacional, LUIS, Análise de Texto, tradutor, etc.), use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. Os custos para serviços cognitivas são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar custos para serviços cognitivas, você é cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo no gerenciamento de custos dá suporte à maioria dos tipos de conta do Azure, mas não a todos eles. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Estimar os custos antes de usar serviços cognitivas

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar serviços cognitivas.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Calculadora de preços do Azure para serviços cognitivas" border="true":::

Ao adicionar novos recursos ao seu espaço de trabalho, retorne a esta calculadora e adicione o mesmo recurso aqui para atualizar suas estimativas de custo.

Para obter mais informações, consulte [preços dos serviços cognitivas do Azure](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Entenda o modelo de cobrança completo para serviços cognitivas

Os serviços cognitivas são executados na infraestrutura do Azure que [acumula custos](https://azure.microsoft.com/pricing/details/cognitive-services/) quando você implanta o novo recurso. É importante entender que a infraestrutura adicional pode acumular o custo. Você precisa gerenciar esse custo ao fazer alterações nos recursos implantados. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Custos que normalmente se acumulam com serviços cognitivas

Normalmente, depois de implantar um recurso do Azure, os custos são determinados pelo tipo de preço e pelas chamadas de API feitas para o ponto de extremidade. Se o serviço que você está usando tiver uma camada de compromisso, passar as chamadas alocadas em sua camada poderá incorrer em um encargo excedente.

Custos adicionais podem ser acumulados ao usar esses serviços:

#### <a name="qna-maker"></a>QnA Maker

Quando você cria recursos para QnA Maker, os recursos para outros serviços do Azure também podem ser criados. Entre elas estão:

- [Serviço de Azure App (para o tempo de execução)](https://azure.microsoft.com/pricing/details/app-service/)
- [Pesquisa Cognitiva do Azure (para os dados)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Custos que podem ser acumulados após a exclusão do recurso

#### <a name="qna-maker"></a>QnA Maker

Depois de excluir QnA Maker recursos, os recursos a seguir podem continuar a existir. Eles continuam acumulando os custos até que você os exclua.

- [Serviço de Azure App (para o tempo de execução)](https://azure.microsoft.com/pricing/details/app-service/)
- [Pesquisa Cognitiva do Azure (para os dados)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-monetary-credit-with-cognitive-services"></a>Usando crédito monetário com serviços cognitivas

Você pode pagar por cobranças de serviços cognitivas com seu crédito de compromisso monetário de EA. No entanto, você não pode usar crédito de compromisso monetário de EA para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="create-budgets"></a>Criar orçamentos

Você pode criar [orçamentos](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, as equipes de finanças podem analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>Próximas etapas

- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
