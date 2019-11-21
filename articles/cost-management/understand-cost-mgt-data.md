---
title: Entender os dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: This article helps you better understand data that's included in Azure Cost Management and how frequently it's processed, collected, shown, and closed.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 25a20ca40b517209fca7b969e3a7399a1c7c7387
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229756"
---
# <a name="understand-cost-management-data"></a>Entender os dados de Gerenciamento de Custos

This article helps you better understand Azure cost and usage data that's included in Azure Cost Management. It explains how frequently data is processed, collected, shown, and closed. Você é cobrado mensalmente pelo uso do Azure. Although billing cycles are monthly periods, cycle start and end dates vary by subscription type. A frequência com que o Gerenciamento de Custos recebe dados de uso varia com base em diferentes fatores. Esses fatores incluem o tempo necessário para processar os dados e a frequência com que os serviços do Azure transmitem o uso para o sistema de cobrança.

Cost Management includes all usage and purchases, including reservations and third-party offerings for Enterprise Agreement (EA) accounts. Microsoft Customer Agreement accounts and individual subscriptions with pay-as-you-go rates  only include usage from Azure and Marketplace services. Support and other costs are not included. Costs are estimated until an invoice is generated and do not factor in credits.

## <a name="supported-microsoft-azure-offers"></a>Supported Microsoft Azure offers

As informações a seguir mostram as atuais [ofertas do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no Gerenciamento de Custos do Azure. Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Data is available in Cost Management starting on the **Data available from** date. If a subscription changes offers, costs before the offer change date will not be available.

| **Categoria**  | **Nome da oferta** | **Quota ID** | **Número da oferta** | **Data available from** |
| --- | --- | --- | --- | --- |
| **Azure Governamental** | Azure Governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | May 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Desenvolvimento/Teste Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | May 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | May 2014<sup>1</sup> |
| **Contrato de Cliente da Microsoft** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | March 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft** | [Microsoft Azure Plan for Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | March 2019<sup>3</sup> |
| **Microsoft Customer Agreement supported by partners** | Plano do Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01, and CSPDEVTEST_2018-05-01<br><br>The quota ID is reused for Microsoft Customer Agreement and legacy CSP subscriptions. Currently, only Microsoft Customer Agreement subscriptions are supported. | N/D | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | October 2, 2018<sup>2</sup> |
| **Pré-paga** | [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | October 2, 2018<sup>2</sup> |
| **Pré-paga** | [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | October 2, 2018<sup>2</sup> |
| **Pré-paga** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | October 2, 2018<sup>2</sup> |
| **Pré-paga** | [Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | October 2, 2018<sup>2</sup> |
| **Pré-paga** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | October 2, 2018<sup>2</sup> |
| **Pré-paga** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | October 2, 2018<sup>2</sup> |

_<sup>**1**</sup> For data before May 2014, visit the [Azure Enterprise portal](https://ea.azure.com)._

_<sup>**2**</sup> For data before October 2, 2018, visit the [Azure Account Center](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft Customer Agreements started in March 2019 and do not have any historical data before this point._

_<sup>**4**</sup> Historical data for credit-based and pay-in-advance subscriptions might not match your invoice. See [Historical data may not match invoice](#historical-data-might-not-match-invoice) below._

The following offers are not supported yet:

| Categoria  | **Nome da oferta** | **Quota ID** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Pagamento Conforme o Uso do Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Governamental** | Pagamento Conforme Uso do Azure Governamental | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **CSP (Provedor de Soluções na Nuvem)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure Governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | Azure Alemanha no CSP para o Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pré-paga**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pré-paga** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pré-paga**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de suporte** | Suporte Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de suporte** | Suporte Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de suporte** | Suporte Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de suporte** | Planos de suporte na Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de suporte** | Suporte Standard do Azure Governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de suporte** | Suporte Pro-Direct do Azure Governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de suporte** | Suporte Developer do Azure Governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determine your offer type
Se não vir os dados de uma assinatura e quiser determinar se sua assinatura se enquadra nas ofertas com suporte, você poderá validar se sua assinatura tem suporte. Para validar se uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Todos os serviços** no menu do painel esquerdo. Na lista de serviços, selecione **Assinaturas**. No menu de lista de assinaturas, clique na assinatura que deseja verificar. Sua assinatura é mostrada na guia Visão Geral e você pode ver a **Oferta** e a **ID da Oferta**. A imagem a seguir mostra um exemplo.

![Exemplo da guia Visão Geral da Assinatura mostrando a Oferta e a ID da Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Gerenciamento de Custos

As tabelas a seguir mostram os dados que estão incluídos ou não no Gerenciamento de Custos. All costs are estimated until an invoice is generated. Costs shown do not include free and prepaid credits.

**Dados de uso e de custo**

| **Incluído** | **Não incluído** |
| --- | --- |
| Azure service usage<sup>5</sup>        | Encargos de suporte – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Marketplace offering usage<sup>6</sup> | Impostos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Marketplace purchases<sup>6</sup>      | Créditos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Reservation purchases<sup>7</sup>      |  |
| Amortization of reservation purchases<sup>7</sup>      |  |

_<sup>**5**</sup> Azure service usage is based on reservation and negotiated prices._

_<sup>**6**</sup> Marketplace purchases are not available for Pay-As-You-Go, MSDN, and Visual Studio offers at this time._

_<sup>**7**</sup> Reservation purchases are only available for Enterprise Agreement (EA) accounts at this time._

**Metadados**

| **Incluído** | **Não incluído** |
| --- | --- |
| Resource tags<sup>8</sup> | Marcas de grupo de recursos |

_<sup>**8**</sup> Resource tags are applied as usage is emitted from each service and aren't available retroactively to historical usage._

## <a name="rated-usage-data-refresh-schedule"></a>Agenda de atualização de dados de uso calculados

Dados de uso e de custo estão disponíveis em Gerenciamento de Custos + Cobrança no portal do Azure e nas [APIs com suporte](index.yml). Ao avaliar os custos, tenha em mente os seguintes pontos:

- Os encargos estimados do período de cobrança atual são atualizados seis vezes por dia.
- Os encargos estimados do período de cobrança atual podem ser alterados conforme você incorre em mais uso.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _encerra_ o período de cobrança atual até 72 horas (três dias corridos) após o fim período de cobrança.

Os exemplos a seguir ilustram como os períodos de cobrança podem ser encerrados.

Assinaturas de EA (Contrato Enterprise) – Se o mês de cobrança terminar em 31 de março, os encargos estimados serão atualizados até 72 horas mais tarde. Neste exemplo, até meia-noite (UTC) de 4 de abril.

Assinaturas pagas conforme o uso – Se o mês de cobrança terminar no dia 15 de maio, os encargos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, até meia-noite (UTC) de 19 de maio.

### <a name="rerated-data"></a>Dados recalculados

Whether you use the [Cost Management APIs](index.yml), Power BI, or the Azure portal to retrieve data, expect the current billing period's charges to get rerated, and consequently change, until the invoice is closed.

## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização dos dados de uso varia

A disponibilidade de seus dados de uso incorridos no Gerenciamento de Custos depende de alguns fatores, incluindo:

- A frequência com que os serviços do Azure (como Armazenamento, Computação, CDN e SQL) transmitem o uso.
- O tempo necessário para processar os dados de uso por meio do mecanismo de classificação e dos pipelines de gerenciamento de custos.

Alguns serviços transmitem o uso com mais frequência do que outros. Portanto, talvez você veja dados no Gerenciamento de Custos para alguns serviços antes do que para outros serviços que emitem dados menos frequentemente. Normalmente, o uso de serviços leva 8 a 24 horas para aparecer no Gerenciamento de Custos. Tenha em mente que os dados de um mês em aberto são atualizados conforme você incorre em mais uso porque as atualizações são cumulativas.

## <a name="historical-data-might-not-match-invoice"></a>Historical data might not match invoice

Historical data for credit-based and pay-in-advance offers might not match your invoice. Some Azure pay-as-you-go, MSDN, and Visual Studio offers can have Azure credits and advanced payments applied to the invoice. However, the historical data shown in Cost Management is based on your estimated consumption charges only. Cost Management historical data doesn't include payments and credits. As a result, the historical data shown for the following offers may not match exactly with your invoice.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Free Trial (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Consulte

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
