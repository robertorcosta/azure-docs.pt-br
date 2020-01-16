---
title: Cenários de automação para gerenciamento de custos e de cobrança do Azure |Microsoft Docs
description: Saiba como cenários comuns de gerenciamento de cobrança e de custos são mapeados para diferentes APIs.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992810"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Cenários de automação para gerenciamento de custos e de cobrança

Este artigo lista os cenários comuns de gerenciamento de custos e cobrança do Azure. Ele mapeia esses cenários para APIs que você pode usar. Em cada mapeamento de API do cenário, você pode encontrar um resumo das APIs e a funcionalidade que elas oferecem.

## <a name="common-scenarios"></a>Cenários comuns

Você pode APIs de gerenciamento de custos e de cobrança em vários cenários para responder a perguntas relacionadas a uso e a custo. Aqui está uma estrutura de tópicos dos cenários comuns:

- **Reconciliação de nota fiscal**: a Microsoft encarregadou o valor certo?  Qual é minha fatura e posso calculá-la por conta própria?

- **Encargos cruzados**: agora que sei quanto estou sendo cobrado, quem em minha organização precisa pagar?

- **Otimização de custos**: sei quanto eu fui cobrado. Como posso obter mais do dinheiro que estou gastando no Azure?

- **Controle de custos**: desejo ver quanto estou gastando e usando o Azure com o passar do tempo. Quais são as tendências? Como posso melhorar?

- **Gastos com o Azure durante o mês**: quanto meu mês atual está gastando até hoje? É necessário fazer alguma alteração ao meus gastos e/ou uso do Azure? Em que período do mês estou consumindo mais do Azure?

- **Alertas**: como posso configurar o consumo baseado em recursos ou os alertas baseados em monetários?

## <a name="scenario-to-api-mapping"></a>Mapeamento de cenário para API

|         API        | Reconciliação de fatura    | Encargos cruzados    | Otimização de custos    | Controle de custos    | Gastos do meio do mês    | Alertas    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Orçamentos                     |                           |                  |           X          |                  |                    |     X     |
| Cobranças do Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Tabela de preços                 |             X             |         X        |           X          |         X        |          X         |           |
| Recomendações de reserva |                           |                  |           X          |                  |                    |           |
| Detalhes da reserva         |                           |                  |           X          |         X        |                    |           |
| Resumos da reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de uso               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de Cobrança             |             X             |         X        |           X          |         X        |                    |           |
| Faturas                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Uso sem classificação               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Os mapeamentos de cenário para API não incluem as APIs de consumo Enterprise. Sempre que possível, use as APIs de consumo geral para novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos de API

### <a name="consumption"></a>Consumo
Os clientes Diretos via Web e empresariais podem usar todas as seguintes APIs, exceto quando observado:

-   [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets) (*somente clientes empresariais*): Crie orçamentos de uso ou de custo para recursos, grupos de recursos ou medidores de cobrança. Quando você tiver criado os orçamentos, poderá configurar alertas para notificar quando você exceder limites de orçamento definidos. Você também pode configurar ações para ocorrerem quando você atingir os valores de orçamento.

-   [API de encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): obter dados de uso e custos em todos os recursos do Azure Marketplace (ofertas de parceiros do Azure). Você pode usar esses dados para somar os custos de todos os recursos do Marketplace ou investigar os custos ou o uso de recursos específicos.

-   [API de folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*somente clientes empresariais*): Obtenha preços personalizados para todos os medidores. As empresas podem usar esses dados em combinação com detalhes de uso e informações de uso do marketplace calcular custos usando dados de uso e do Marketplace.

-   [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Obtenha recomendações para comprar instâncias de VM reservadas. As recomendações ajudam a analisar as economias de custo esperadas e os valores de compra. Para obter mais informações, confira [APIs para automação de reserva do Azure](../reservations/reservation-apis.md).

-   [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): consulte informações sobre reservas de VM adquiridas anteriormente, como quanto consumo é reservado versus quanto é usado. Você pode ver os dados em um nível de detalhe por VM. Para obter mais informações, confira [APIs para automação de reserva do Azure](../reservations/reservation-apis.md).

-   [API de resumos de reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): consulte informações agregadas sobre reservas de VM que sua organização comprou, como quanto consumo é reservado versus quanto é usado na agregação. Para obter mais informações, confira [APIs para automação de reserva do Azure](../reservations/reservation-apis.md).

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails): Obtenha informações de custo e uso em todos os recursos do Azure da Microsoft. As informações estão na forma de registros de detalhes de uso que atualmente são emitidos uma vez por metro por dia. Você pode usar as informações para somar os custos de todos os recursos ou investigar os custos ou o uso de recursos específicos.

-   [API do RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Obtenha taxas de medição se você for um cliente direto da Web. Você então pode usar as informações retornadas com suas informações de uso de recursos para calcular manualmente a fatura esperada.

-   [API de uso sem classificação](/previous-versions/azure/reference/mt219003(v=azure.100)): Obtenha informações brutas de uso antes que o Azure faça qualquer medição/cobrança.

### <a name="billing"></a>Cobrança
-   [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Determine um período de cobrança para analisar, junto com as IDs de fatura para esse período. Você pode usar as IDs de fatura com a API de Faturas.

-   [API de notas fiscais](/rest/api/billing/2019-10-01-preview/invoices): Obtenha a URL de download para uma fatura de um período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo de Enterprise
As seguintes APIs são apenas para Enterprise:

-   [API de Resumo de saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Obtenha um resumo mensal das informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos excedentes. Você pode obter essas informações para o período de faturamento atual ou para qualquer período no passado. As empresas podem usar esses dados para comparação com o resumo de encargos calculados manualmente. Essa API não fornece informações específicas do recurso nem uma exibição agregada de custos.

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Obtenha informações sobre o uso do Azure (das ofertas da Microsoft) para o mês atual, um período de cobrança específico ou um período de data personalizado. As empresas podem usar esses dados para calcular faturas manualmente com base na taxa e no consumo. As empresas também podem usar informações de organização/departamento para custos de atributo entre organizações. Os dados fornecem uma exibição do uso e do custo específica a um recurso.

-   [API de cobrança do Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Obtenha informações sobre o uso do Azure (de ofertas de parceiros) para o mês atual, um período de cobrança específico ou um período de data personalizado. As empresas podem usar esses dados para calcular faturas manualmente com base na taxa e no consumo. As empresas também podem usar informações de organização/departamento para custos de atributo entre organizações. Essa API fornece uma exibição do uso e do custo específica a um recurso.

-   [API da folha de preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Obtenha a taxa aplicável para cada medidor para o registro e o período de cobrança fornecidos. Você pode usar essas informações de taxa em combinação com detalhes de uso e informações de uso do marketplace para calcular manualmente a fatura esperada.

-   [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Obtenha uma lista de períodos de cobrança. A API também fornece uma propriedade que aponta para a rota da API para os quatro conjuntos de dados da API empresarial que pertencem ao período de cobrança:: Balancesummary, UsageDetails, encargos do Marketplace e PriceSheet.

-   [API de recomendações de instância reservada](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): examine 7 dias, 30 dias ou 60 dias de uso da máquina virtual e Obtenha recomendações de compra únicas e compartilhadas. Você pode usar essa API para analisar as economias de custo esperadas e os valores de compra recomendados. Para obter mais informações, confira [APIs para automação de reserva do Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de relatórios corporativos e as APIs de consumo? Quando devo usar cada um?
Essas APIs têm um conjunto semelhante de funcionalidades e podem responder ao mesmo conjunto amplo de perguntas no espaço de faturamento e gerenciamento de custos. Mas eles se destinam a públicos diferentes:

- APIs de Relatórios Enterprise estão disponíveis para clientes que assinaram um Contrato Enterprise com a Microsoft que concede a eles acesso negociados investimentos e preços personalizados. As APIs exigem uma chave que você pode obter do [Enterprise Portal](https://ea.azure.com). Para obter uma descrição dessas APIs, confira [visão geral de APIs de relatórios para clientes empresariais](enterprise-api.md).

- APIs de Consumo estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, consulte [Visão geral da API de consumo do Azure](consumption-api-overview.md) e a [referência da API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). Recomendamos as APIs fornecidas como a solução para os cenários de desenvolvimento mais recentes.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual é a diferença entre a API de detalhes de uso e a API de uso?
Essas APIs fornecem dados fundamentalmente diferentes:

- A [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece informações de uso e custo do Azure por instância de medidor. Os dados fornecidos já passaram pelo sistema de medição de custos no Azure e tiveram seu custo aplicado junto com outras possíveis alterações:

   - Alterações para contabilizar o uso de compromissos monetários pré-pagos
   - Alterações para considerar discrepâncias de uso descobertas pelo Azure

- a [API de uso](/previous-versions/azure/reference/mt219003(v=azure.100)) fornece informações de uso do Azure bruto antes de transmiti-la por meio do sistema de medição de custo do Azure. Esses dados podem não ter nenhuma correlação com a quantidade de uso ou custos visto depois que o Azure cobra um sistema de medição.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de fatura e a API de detalhes de uso?
Essas APIs fornecem uma exibição diferente dos mesmos dados:

- A [API de Fatura](/rest/api/billing/2019-10-01-preview/invoices) destina-se somente a clientes Diretos da Web. Ela fornece um rollup mensal de sua fatura com base nos encargos agregados para cada tipo de medidor.

- A [API de Detalhes de Uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) apresenta uma exibição detalhada dos registros de uso/custo para cada dia. Os clientes Enterprise e Diretos via Web podem usá-la.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>O que é a diferença entre a API de tabela de preços e a API RateCard?
Essas APIs fornecem conjuntos semelhantes de dados, mas têm diferentes públicos:

- a [API de folha de preço](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece preço personalizado negociado para um cliente Enterprise.

- A [API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) fornece o preço voltado para o público que se aplica a clientes Diretos via Web.

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre como usar as APIs do Azure para obter insights de maneira programática sobre seu uso do Azure, veja [Visão geral da API de Consumo do Azure](consumption-api-overview.md) e [Visão geral da API de Cobrança do Azure](usage-rate-card-overview.md).

- Para comparar sua fatura com o arquivo detalhado de uso diário e os relatórios de gerenciamento de custos no Portal do Azure, veja [Entenda sua fatura do Microsoft Azure](../understand/review-individual-bill.md).

- Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
