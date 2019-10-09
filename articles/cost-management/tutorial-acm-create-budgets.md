---
title: Tutorial – Criar e gerenciar orçamentos do Azure | Microsoft Docs
description: Este tutorial ajuda a planejar e contabilizar os custos de serviços do Azure que você consome.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8d8f796fa7db9cab5bcac88a293dd1b98707a571
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025762"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: criar e gerenciar orçamentos do Azure

Orçamentos no Gerenciamento de Custos ajudam você a planejar e promover responsabilidade organizacional. Com orçamentos, você pode considerar os serviços do Azure que consome ou assina durante um período específico. Eles ajudam você a informar outras pessoas sobre seus gastos para gerenciar proativamente os custos e monitorar como os gastos evoluem ao longo do tempo. Quando os limites de orçamento que você criou são excedidos, apenas notificações são disparadas. Nenhum de seus recursos é afetado e seu consumo não é interrompido. Você pode usar os orçamentos para comparar e controlar como analisar os custos de gastos.

Os dados de custo e de uso geralmente estão disponíveis em 12-16 horas e os orçamentos são avaliados em relação a esses custos a cada quatro horas. As notificações por email normalmente são recebidas dentro de 12-16 horas.

Os orçamentos são redefinidos automaticamente no final de um período (mensal, trimestral ou anual) para o mesmo valor de orçamento quando você seleciona uma data de expiração no futuro. Uma vez que redefinidos com o mesmo valor de orçamento, você precisará criar orçamentos separados quando os valores monetários orçados forem diferentes para períodos futuros.

Os exemplos deste tutorial ajudam você a criar e editar um orçamento para uma assinatura do Azure Enterprise Agreement (EA).

Assista ao vídeo [como criar um orçamento para monitorar seus gastos com o gerenciamento de custos do Azure](https://www.youtube.com/watch?v=ExIVG_Gr45A) para ver como você pode criar orçamentos no Azure para monitorar os gastos.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

## <a name="prerequisites"></a>Pré-requisitos

Há suporte para orçamentos de uma variedade de tipos de conta do Azure. Para exibir a lista completa dos tipos de conta compatíveis, confira [Entender os dados do Gerenciamento de Custos](understand-cost-mgt-data.md). Para exibir os orçamentos, você precisará, pelo menos, de acesso de leitura em sua conta do Azure.

 Para assinatura do Azure EA, você deve ter acesso de leitura para exibir orçamentos. Para criar e gerenciar orçamentos, você deve ter permissão de colaborador. Você pode criar orçamentos individuais para grupos de recursos e assinaturas de EA. No entanto, não é possível criar os orçamentos para contas de cobrança de EA.

As seguintes permissões do Azure, ou escopos, têm suporte por assinatura para orçamentos por usuário e grupo. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário – pode criar, modificar ou excluir os orçamentos para uma assinatura.
- Colaborador e contribuidor do Gerenciamento de Custos - pode criar, modificar ou excluir seus próprios orçamentos. Pode modificar o valor do orçamento para orçamentos criados por outras pessoas.
- Leitor e leitor do Gerenciamento de Custos – pode exibir os orçamentos para os quais ele têm permissão.

Para obter mais informações sobre como atribuir permissões aos dados de Gerenciamento de Custos, consulte [Atribuir acesso aos dados de Gerenciamento de Custos](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Você pode criar um orçamento de assinatura do Azure para um período mensal, trimestral ou anual. O conteúdo de navegação na portal do Azure determina se você cria um orçamento para uma assinatura ou para um grupo de gerenciamento.

Para criar ou exibir um orçamento, abra o escopo desejado no portal do Azure e selecione **orçamentos** no menu. Por exemplo, navegue até **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione **orçamentos** no menu. Use o **escopo** do Pill para alternar para um escopo diferente, como um grupo de gerenciamento, em orçamentos. Para obter mais informações sobre escopos, consulte [Entender e trabalhar com escopos](understand-work-scopes.md).

Depois de criar os orçamentos, eles mostram uma exibição simples de seus gastos atual em relação a eles.

Clique em **Adicionar** .

![Exemplo mostrando uma lista de orçamentos já criados](./media/tutorial-acm-create-budgets/budgets01.png)

Na janela **criar orçamento** , verifique se o escopo mostrado está correto. Escolha os filtros que você deseja adicionar. Os filtros permitem que você crie orçamentos para custos específicos, como grupos de recursos em uma assinatura ou um serviço como máquinas virtuais. Qualquer filtro que você possa usar na análise de custo também pode ser aplicado a um orçamento.

Depois de identificar o escopo e os filtros, digite um nome de orçamento. Em seguida, escolha um período mensal, trimestral ou anual de redefinição de orçamento. Esse período de redefinição determina a janela de tempo que é analisada pelo orçamento. O custo avaliado pelo orçamento começa em zero no início de cada novo período. Quando você cria um orçamento trimestral, ele funciona da mesma maneira que um orçamento mensal. A diferença é que o valor do orçamento para o trimestre é dividido entre os três meses do trimestre. Um valor de orçamento anual é dividido uniformemente entre todos os 12 meses do ano civil.

Se você tiver uma assinatura de Pagamento Conforme o Uso, do MSDN ou do Visual Studio, o período de cobrança da fatura talvez não esteja alinhado ao mês do calendário. Para esses tipos de assinatura e grupos de recursos, você pode criar um orçamento alinhado ao seu período de fatura ou a meses de calendário. Para criar um orçamento alinhado ao período da fatura, selecione um período de redefinição de **mês de cobrança**, **trimestre de cobrança**ou ano de **cobrança**. Para criar um orçamento alinhado ao mês do calendário, selecione um período de redefinição **mensal**, **trimestral**ou **anual**.

Em seguida, identifique a data de validade quando o orçamento se tornar inválido e pare de avaliar seus custos.

Com base nos campos escolhidos no orçamento até o momento, um grafo é mostrado para ajudá-lo a selecionar um limite a ser usado para seu orçamento. O orçamento sugerido é baseado no custo previsto mais alto que você pode incorrer em períodos futuros. Você pode alterar o valor do orçamento.

![Exemplo mostrando a criação de orçamento com dados de custo mensal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Depois de configurar o valor do orçamento, clique em **Avançar** para configurar alertas de orçamento. Orçamentos exigem pelo menos um limite de custo (% do orçamento) e um endereço de email correspondente. Opcionalmente, você pode incluir até cinco limites e cinco endereços de email em um único orçamento. Quando um limite de orçamento é atingido, as notificações de email normalmente são recebidas em menos de 20 horas. Para obter mais informações sobre as notificações, confira [Alertas de custo de uso](cost-mgt-alerts-monitor-usage-spending.md). No exemplo a seguir, um alerta de email é gerado quando 90% do orçamento é atingido.

![Exemplo mostrando condições de alerta](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Depois de criar um orçamento, ele é mostrado na análise de custo. Exibir seu orçamento em relação à sua tendência de gastos é uma das primeiras etapas quando você começa a [analisar seus custos e gastos](quick-acm-cost-analysis.md).

![Exemplo de orçamento e gastos mostrados na análise de custos](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, você criou um orçamento para uma assinatura. No entanto, você também pode criar um orçamento para um grupo de recursos. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Gerenciamento de Custos + Cobrança** &gt; **Assinaturas** &gt; selecione uma assinatura > **Grupo de recursos** > selecione um grupo de recursos > **Orçamentos** > e, em seguida escolha **Adicionar** um orçamento.

## <a name="trigger-an-action-group"></a>Disparar um grupo de ações

Quando você cria ou edita um orçamento para um escopo de grupo de recursos ou assinatura, você pode configurá-lo para chamar um grupo de ação. O grupo de ações pode executar uma variedade de ações diferentes quando o limite do orçamento é atingido. Para obter mais informações sobre grupos de ações, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/platform/action-groups.md). Para obter mais informações sobre como usar a automação baseada em orçamento com grupos de ação, consulte [gerenciar custos com orçamentos do Azure](../billing/billing-cost-management-budget-scenario.md).

Para criar ou atualizar grupos de ações, clique em **gerenciar grupos de ações** enquanto estiver criando ou editando um orçamento.

![Exemplo de criação de um orçamento para mostrar grupos de ação de gerenciamento](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Em seguida, clique em **Adicionar grupo de ações** e crie o grupo de ações.


![Imagem da caixa Adicionar grupo de ações](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Depois que o grupo de ações for criado, feche a caixa para retornar ao seu orçamento.

Configure seu orçamento para usar o grupo de ações quando um limite individual for atingido. Há suporte para até cinco limites diferentes.

![Exemplo mostrando a seleção do grupo de ações para uma condição de alerta](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

O exemplo a seguir mostra os limites de orçamento definidos como 50%, 75% e 100%. Cada um é configurado para disparar as ações especificadas dentro do grupo de ações designado.

![Exemplo mostrando condições de alerta configuradas com vários grupos de ação e tipos de ações](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

Avance para o próximo tutorial para criar uma exportação recorrente para os dados de gerenciamento de custos.

> [!div class="nextstepaction"]
> [Criar e gerenciar dados exportados](tutorial-export-acm-data.md)
