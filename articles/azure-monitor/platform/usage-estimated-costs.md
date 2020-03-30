---
title: Monitorar o uso e os custos estimados no Azure Monitor
description: Visão geral do processo de utilização da página de custos estimados e uso do Azure Monitor
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658808"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorar o uso e os custos estimados no Azure Monitor

> [!NOTE]
> Este artigo descreve como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure. Os artigos relacionados para componentes específicos do Azure Monitor incluem:
> - [Gerenciar o uso e os custos com o Azure Monitor Logs](manage-cost-storage.md) descreve como controlar seus custos alterando seu período de retenção de dados e como analisar e alertar sobre o uso de seus dados.
> - [Gerenciar o uso e os custos do Application Insights](../../azure-monitor/app/pricing.md) descreve como analisar o uso de dados no Application Insights.

## <a name="azure-monitor-pricing-model"></a>Modelo de preços do Monitor Azure

O modelo básico de faturamento do Azure Monitor é um preço baseado em consumo ("Pay-As-You-Go"). Você paga apenas pelo que usa. Os detalhes de preços estão disponíveis para [alertar, métricas, notificações,](https://azure.microsoft.com/pricing/details/monitor/) [Análises de Log](https://azure.microsoft.com/pricing/details/log-analytics/) e [Insights de Aplicativos.](https://azure.microsoft.com/pricing/details/application-insights/) 

Além do modelo Pay-As-You-Go para dados de log, o Log Analytics tem reservas de capacidade, que permitem economizar até 25% em comparação com o preço pay-as-you-go. O preço de reserva de capacidade permite que você compre uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado na taxa Pay-As-You-Go. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre preços de reserva de capacidade.

Alguns clientes terão acesso aos [níveis de preços legados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) do Log Analytics e ao nível de preços do Enterprise Application [Insights legado.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Entendendo os custos do Monitor do Azure

Há duas fases para entender os custos. A primeira é quando se considera o Azure Monitor como sua solução de monitoramento. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerenciar seu ambiente

Se você ainda não estiver usando o Azure Monitor Logs, você pode usar a calculadora de preços do [Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo de usar o Azure Monitor. Comece digitando "Azure Monitor" na caixa de pesquisa e clicando no azulejo do Monitor Azure resultante. Role a página para o Azure Monitor e selecione uma das opções da estada tipo:

- Consultas métricas e alertas  
- Log Analytics
- Application Insights

Em cada uma delas, a calculadora de preços ajudará você a estimar seus custos prováveis com base na sua utilização esperada.

Por exemplo, com o Log Analytics você pode inserir o número de VMs e o GB de dados que você espera coletar de cada VM. Normalmente, 1 GB a 3 GB de dados mês é ingerido a partir de uma VM Azure típica. Se você já está avaliando o Azure Monitor Logs, você pode usar suas estatísticas de dados do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e o [volume de dados que seu espaço de trabalho está ingerindo](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).

Da mesma forma, para o Application Insights, se você habilitar a funcionalidade "Estimar o volume de dados com base na atividade do aplicativo", você poderá fornecer entradas sobre seu aplicativo (solicitações por mês e visualizações de página por mês, caso você colete a telemetria do lado do cliente), e, em seguida, a calculadora lhe dirá a quantidade mediana e de 90% de dados coletados por aplicativos semelhantes. Esses aplicativos abrangem a gama de configuração do Application Insights (por exemplo, alguns têm amostragem padrão, alguns não têm amostragem etc.), então você ainda tem o controle para reduzir o volume de dados que ingere muito abaixo do nível médio usando amostragem. Mas este é um ponto de partida para entender o que outros clientes semelhantes estão vendo. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) sobre a estimativa de custos para o Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Entendendo seu uso e custos estimados

É importante entender e acompanhar seu uso uma vez usando o Azure Monitor, e existem um rico conjunto de ferramentas para facilitar isso. 

O Azure oferece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Após abrir o hub **Azure Cost Management + Billing,** clique em **Gerenciamento de Custos** e selecione o [Escopo](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (conjunto de recursos para investigar). 

Em seguida, para ver os custos do Monitor Do Azure nos últimos 30 dias, clique no bloco **de custos diários,** escolha "Últimos 30 dias" em datas relativas e adicione um filtro que seleciona os nomes do Serviço:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight e Análise

Isso resulta em uma visão como:

![Captura de tela do Azure Cost Management](./media/usage-estimated-costs/010.png)

A partir daqui, você pode perfurar a partir deste resumo de custos acumulado para obter os detalhes mais finos na exibição "Custo por recurso". Nos níveis atuais de preços, os dados do Azure Log são cobrados no mesmo conjunto de medidores, sejam eles originados do Log Analytics ou do Application Insights. Para separar os custos do uso do Log Analytics ou do Application Insights, você pode adicionar um filtro no **tipo resource**. Para ver todos os custos do Application Insights, filtre o tipo de recurso para "microsoft.insights/componentes" e para os custos do Log Analytics, filtre o tipo de recurso para "microsoft.operationalinsights/workspaces". 

Mais detalhes do seu uso estão disponíveis [baixando seu uso no portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso por recurso do Azure por dia. Nesta planilha do Excel, o uso dos recursos do Application Insights pode ser encontrado primeiro filtrando na coluna "Categoria do medidor" para mostrar "Insights de aplicativos" e "Análise de log", e, em seguida, adicionar um filtro na coluna "Instance ID" que é "contém microsoft.insights/componentes".  A maioria dos usos do Application Insights é relatada em medidores com a categoria medidor de Análise de Log, uma vez que há um backend de logs único para todos os componentes do Azure Monitor.  Apenas os recursos do Application Insights sobre níveis de preços legados e testes web em várias etapas são relatados com uma categoria de medidor de insights de aplicativos.  O uso é mostrado na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua conta do Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

> [!NOTE]
> O uso **da Gestão de Custos** no hub **Azure Cost Management + Billing** é a abordagem preferida para entender amplamente os custos de monitoramento.  As experiências **de uso e custos estimados** para o Log [Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) e [o Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) fornecem insights mais profundos para cada uma dessas partes do Azure Monitor.

Outra opção para visualizar o uso do Monitor Do Azure é a página **de uso e custos estimados** no hub monitor. Isso mostra o uso de recursos de monitoramento principal, como [alertas, métricas, notificações,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)e [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para clientes nos planos de preços disponíveis antes de abril de 2018, isso também inclui o uso do Log Analytics adquirido por meio das ofertas do Insights e do Analytics.

Nesta página, os usuários podem exibir o uso de recursos para os últimos 31 dias, agregados por assinatura. `Drill-ins`mostrar tendências de uso durante o período de 31 dias. São necessários vários dados para se juntar e fazer essa estimativa, portanto, seja paciente enquanto a página é carregada.

Este exemplo mostra o uso de monitoramento e uma estimativa dos custos resultantes:

![Captura de tela do portal do uso e custos estimados](./media/usage-estimated-costs/001.png)

Selecione o link na coluna de uso mensal para abrir um gráfico que mostra as tendências de uso nos últimos 31 dias: 

![Incluído por captura de tela do gráfico de barras do nó](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Direitos de assinatura do Operations Management Suite

Clientes que adquiriram o Microsoft Operations Management Suite E1 e E2 estão qualificados para direitos de ingestão de dados por nó para [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Para receber esses direitos para workspaces Log Analytics ou recursos Application Insights em uma determinada assinatura: 

- Workspaces do Log Analytics devem usar o tipo de preço "por nó (OMS)".
- Os recursos do Application Insights devem usar o nível de preços "Enterprise".

Dependendo do número de nós da suíte que sua organização comprou, mover algumas assinaturas para um nível de preços Pay-As-You-Go (Por GB) pode ser vantajoso, mas isso requer uma consideração cuidadosa.

> [!WARNING]
> Se sua organização possui o Atual Pacote de Gerenciamento de Operações da Microsoft E1 e E2, geralmente é melhor manter seus espaços de trabalho do Log Analytics no nível de preços "Por nó (OMS)" e seus recursos do Application Insights na camada de preços "Enterprise". 
>
