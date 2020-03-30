---
title: Planeje e gerencie custos para o Armazenamento Azure
description: Saiba como planejar e gerenciar custos para o Azure Storage usando a análise de custos no portal Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304518"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planeje e gerencie custos para o Armazenamento Azure

Este artigo descreve como você planeja e gerencia os custos do Azure Storage. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos de armazenamento antes de adicionar quaisquer recursos. Depois de começar a usar os recursos do Azure Storage, use recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode rever os custos previstos e monitorar as tendências de gastos para identificar áreas onde você pode querer atuar.

Tenha em mente que os custos para o Armazenamento Azure são apenas uma parte dos custos mensais em sua conta do Azure. Embora este artigo explique como planejar e gerenciar custos para o Azure Storage, você é cobrado por todos os serviços e recursos do Azure usados para sua assinatura do Azure, incluindo os serviços de terceiros. Depois de estar familiarizado com o gerenciamento de custos para o Azure Storage, você pode aplicar métodos semelhantes para gerenciar custos para todos os serviços do Azure usados em sua assinatura.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte a diferentes tipos de conta do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../../cost-management-billing/costs/understand-cost-mgt-data.md). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Estimar custos antes de criar uma conta de armazenamento do Azure

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar e começar a transferir dados para uma conta do Azure Storage.

1. Na página da calculadora de preços do [Azure,](https://azure.microsoft.com/pricing/calculator/) escolha o azulejo **Contas de** armazenamento.

2. Role a página e localize a seção Contas de **armazenamento** da sua estimativa.

3. Escolha opções nas listas de paradas. 

   À medida que você modifica o valor dessas listas de parada, a estimativa de custos muda. Essa estimativa aparece no canto superior, bem como na parte inferior da estimativa. 
    
   ![Monitore os custos com painel de Análise de Custos](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   À medida que você altera o valor da lista de parada **tipo,** outras opções que aparecem nesta planilha também mudam. Use os links na seção **Mais Informações** para saber mais sobre o que cada opção significa e como essas opções afetam o preço das operações relacionadas ao armazenamento. 

4. Modifique as opções restantes para ver seu efeito na sua estimativa.

## <a name="use-budgets-and-cost-alerts"></a>Usar orçamentos e alertas de custo

É possível criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) para gerenciar custos e criar alertas que notificam automaticamente os stakeholders de anomalias de gastos e risco de gastos em excesso. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, por isso são úteis como parte de uma estratégia global de monitoramento de custos. No entanto, eles podem ter funcionalidade limitada para gerenciar custos individuais de serviço do Azure, como o custo do Armazenamento Azure, porque eles são projetados para rastrear custos em um nível mais alto.

## <a name="monitor-costs"></a>Monitorar custos

À medida que você usa os recursos do Azure com o Azure Storage, você incorre em custos. Os custos da unidade de uso de recursos variam de acordo com intervalos de tempo (segundos, minutos, horas e dias) ou por uso unitário (bytes, megabytes e assim por diante.) Os custos são incorridos assim que o uso do Azure Storage é iniciado. Você pode ver os custos no painel [de análise](../../cost-management-billing/costs/quick-acm-cost-analysis.md) de custos no portal Azure.

Quando você usa a análise de custos, você pode visualizar os custos do Armazenamento Azure em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são por dia, mês atual e anterior, e ano. Você também pode ver os custos com orçamentos e custos previstos. Mudar para visualizações mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde os gastos excessivos podem ter ocorrido. Se você criou orçamentos, você também pode facilmente ver onde eles excederam.

Para visualizar os custos do Azure Storage na análise de custos:

1. Inscreva-se no [portal Azure](https://portal.azure.com).

2. Abra a janela **Gerenciamento de Custos + Faturamento,** selecione Gerenciamento de **custos** no menu e selecione Análise **de custos**. Em seguida, você pode alterar o escopo de uma assinatura específica **a** partir da gota de escopo.

   ![Monitore os custos com painel de Análise de Custos](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Para exibir apenas os custos do Armazenamento Azure, **selecione Adicionar filtro** e, em seguida, selecione **Nome do serviço**. Em seguida, escolha o **armazenamento** da lista. 

   Aqui está um exemplo mostrando os custos apenas para o Armazenamento Azure:

   ![Monitore os custos de armazenamento com painel de análise de custos](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

No exemplo anterior, você vê o custo atual para o serviço. Os custos das regiões do Azure (locais) e por grupo de recursos também aparecem.  

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o gerenciamento de custos com [análise de custos.](../../cost-management-billing/costs/quick-acm-cost-analysis.md)

Veja os artigos a seguir para saber mais sobre como funciona o preço com o Azure Storage:

- [Preços de visão geral do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Otimizar custos para o armazenamento de blobs com capacidade reservada](../blobs/storage-blob-reserved-capacity.md)
