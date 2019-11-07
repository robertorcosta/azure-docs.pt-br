---
title: Entender os dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender melhor os dados incluídos no gerenciamento de custos do Azure e com que frequência eles são processados, coletados, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721359"
---
# <a name="understand-cost-management-data"></a>Entender os dados de Gerenciamento de Custos

Este artigo ajuda você a entender melhor os dados de uso e de custo do Azure incluídos no gerenciamento de custos do Azure. Ele explica com que frequência os dados são processados, coletados, mostrados e fechados. Você é cobrado mensalmente pelo uso do Azure. Embora os ciclos de cobrança sejam períodos mensais, as datas de início e término do ciclo variam por tipo de assinatura. A frequência com que o Gerenciamento de Custos recebe dados de uso varia com base em diferentes fatores. Esses fatores incluem o tempo necessário para processar os dados e a frequência com que os serviços do Azure transmitem o uso para o sistema de cobrança.

O gerenciamento de custos inclui todo o uso e as compras, incluindo reservas e ofertas de terceiros para contas de Enterprise Agreement (EA). As contas de contrato de cliente da Microsoft e as assinaturas individuais com tarifas pagas conforme o uso incluem apenas os serviços do Azure e do Marketplace. O suporte e outros custos não são incluídos. Os custos são estimados até que uma nota fiscal seja gerada e não Fatore os créditos.

## <a name="supported-microsoft-azure-offers"></a>Ofertas de Microsoft Azure com suporte

As informações a seguir mostram as atuais [ofertas do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no Gerenciamento de Custos do Azure. Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Os dados estão disponíveis no gerenciamento de custos a partir dos **dados disponíveis a partir da** Data. Se uma assinatura alterar as ofertas, os custos antes da data de alteração da oferta não estarão disponíveis.

| **Categoria**  | **Nome da oferta** | **ID da cota** | **Número da oferta** | **Dados disponíveis de** |
| --- | --- | --- | --- | --- |
| **Azure Governamental** | Azure Governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Desenvolvimento/Teste Enterprise                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maio de 2014<sup>1</sup> |
| **Contrato com o cliente da Microsoft** | [Plano de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | Março de 2019<sup>3</sup> |
| **Contrato com o cliente da Microsoft** | [Plano de Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | Março de 2019<sup>3</sup> |
| **Contrato com o cliente da Microsoft com suporte dos parceiros** | Plano de Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>A ID da cota é reutilizada para o contrato do cliente da Microsoft e assinaturas CSP herdadas. Atualmente, há suporte apenas para assinaturas do contrato do cliente da Microsoft. | N/D | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pré-paga** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 de outubro de 2018<sup>2</sup> |

_<sup>**1**</sup> para os dados antes de 2014 de maio, visite o [portal do Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> para dados antes de 2 de outubro de 2018, visite o [centro de contas do Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> os contratos de clientes da Microsoft começaram em março de 2019 e não têm nenhum dado histórico antes desse ponto._

_<sup>**4**</sup> os dados históricos para assinaturas com base em crédito e de pagamento em adiantamento podem não corresponder à sua fatura. Consulte [os dados históricos podem não corresponder à fatura](#historical-data-might-not-match-invoice) abaixo._

As ofertas a seguir ainda não têm suporte:

| Categoria  | **Nome da oferta** | **ID da cota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Pagamento Conforme o Uso do Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **CSP (Provedor de Soluções na Nuvem)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure Governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Provedor de Soluções na Nuvem)** | CSP do Azure na Alemanha para o Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pré-paga**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pré-paga** | [Azure para estudantes](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pré-paga**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de suporte** | Suporte Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de suporte** | Suporte Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de suporte** | Suporte Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de suporte** | Planos de suporte na Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de suporte** | Suporte Standard do Azure Governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de suporte** | Suporte Pro-Direct do Azure Governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de suporte** | Suporte Developer do Azure Governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se não vir os dados de uma assinatura e quiser determinar se sua assinatura se enquadra nas ofertas com suporte, você poderá validar se sua assinatura tem suporte. Para validar se uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Todos os serviços** no menu do painel esquerdo. Na lista de serviços, selecione **Assinaturas**. No menu de lista de assinaturas, clique na assinatura que deseja verificar. Sua assinatura é mostrada na guia Visão Geral e você pode ver a **Oferta** e a **ID da Oferta**. A imagem a seguir mostra um exemplo.

![Exemplo da guia Visão Geral da Assinatura mostrando a Oferta e a ID da Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Gerenciamento de Custos

As tabelas a seguir mostram os dados que estão incluídos ou não no Gerenciamento de Custos. Todos os custos são estimados até que uma fatura seja gerada. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

**Dados de uso e de custo**

| **Incluído** | **Não incluído** |
| --- | --- |
| Uso do serviço do Azure<sup>5</sup>        | Encargos de suporte – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Uso<sup>6</sup> da oferta do Marketplace | Impostos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Compras do Marketplace<sup>6</sup>      | Créditos – Para obter mais informações, consulte [Termos da fatura explicados](../billing/billing-understand-your-invoice.md). |
| Compras de reserva<sup>7</sup>      |  |
| Amortização de compras de reserva<sup>7</sup>      |  |

_<sup>**5**</sup> o uso do serviço do Azure baseia-se na reserva e nos preços negociados._

_<sup>**6**</sup> as compras do Marketplace não estão disponíveis para ofertas de pagamento conforme o uso, MSDN e Visual Studio no momento._

_<sup>**7**</sup> as compras de reserva estão disponíveis somente para contas de Enterprise Agreement (ea) neste momento._

**Metadados**

| **Incluído** | **Não incluído** |
| --- | --- |
| Marcas de recurso<sup>8</sup> | Marcas de grupo de recursos |

_<sup>**8**</sup> marcas de recurso são aplicadas conforme o uso é emitido de cada serviço e não está disponível retroativamente para uso histórico._

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

Se você usar as [APIs de gerenciamento de custos](index.yml), Power bi ou o portal do Azure para recuperar dados, espere que os encargos do período de cobrança atual sejam reclassificados e, consequentemente, sejam alterados até que a fatura seja fechada.

## <a name="cost-management-data-fields"></a>Campos de dados de gerenciamento de custos

Os campos de dados a seguir são encontrados em arquivos de detalhes de uso e APIs de gerenciamento de custos. Para os seguintes campos em negrito, os parceiros podem usar filtrar e agrupar por recursos na análise de custo para analisar os custos por vários campos. Os campos em negrito se aplicam somente aos contratos de clientes da Microsoft com suporte pelos parceiros.

| **Nome do campo** | **Descrição** |
| --- | --- |
| faturaid | A ID da nota fiscal mostrada na nota fiscal para a transação específica. |
| previousInvoiceID | Referência a uma fatura original há um reembolso (custo negativo). Populado somente quando há um reembolso. |
| billingAccountName | Nome da conta de cobrança que representa o parceiro. Ele acumula todos os custos entre os clientes que se integraram a um contrato com o cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. |
| billingAccountID | Identificador da conta de cobrança que representa o parceiro. |
| billingProfileID | Identificador do perfil de cobrança que agrupa custos entre faturas em uma única moeda de cobrança entre os clientes que se integraram a um contrato de cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e Reservas. |
| billingProfileName | Nome do perfil de cobrança que agrupa custos entre faturas em uma única moeda de cobrança entre os clientes que se integraram a um contrato de cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e Reservas. |
| invoiceSectionName | Nome do projeto que está sendo cobrado na fatura. Não aplicável para contratos de clientes da Microsoft integrados por parceiros. |
| invoiceSectionID | Identificador do projeto que está sendo cobrado na fatura. Não aplicável para contratos de clientes da Microsoft integrados por parceiros. |
| **CustomerTenantID** | Identificador do locatário de Azure Active Directory da assinatura do&#39;cliente s. |
| **Customer** | Nome do locatário de Azure Active Directory para a assinatura&#39;do cliente s. |
| **CustomerTenantDomainName** | Nome de domínio para o locatário de Azure Active Directory da&#39;assinatura do cliente s. |
| **PartnerTenantID** | Identificador para o locatário&#39;de Azure Active Directory do parceiro. |
| **PartnerName** | Nome do locatário do parceiro Azure Active Directory. |
| **ResellerMPNID** | MPNID para o revendedor associado à assinatura. |
| costCenter | Centro de custo associado à assinatura. |
| billingPeriodStartDate | Data de início do período de cobrança, conforme mostrado na nota fiscal. |
| billingPeriodEndDate | Data de término do período de cobrança, conforme mostrado na nota fiscal. |
| servicePeriodStartDate | Data de início do período de classificação em que o uso do serviço foi classificado para cobranças. Os preços dos serviços do Azure são determinados para o período de classificação. |
| servicePeriodEndDate | Data de término do período em que o uso do serviço foi classificado para cobranças. Os preços dos serviços do Azure são determinados com base no período de classificação. |
| data | Para dados de consumo do Azure, ele mostra a data de uso como classificado. Para a instância reservada, ela mostra a data de compra. Para encargos recorrentes e encargos de uso único, como Marketplace e suporte, ele mostra a data de compra. |
| productID | Identificador do produto que tem encargos acumulados por consumo ou compra. É a chave concatenada de productID e SKuID, conforme mostrado no Partner Center. |
| product | Nome do produto que tem encargos acumulados por consumo ou compra, conforme mostrado na nota fiscal. |
| Da onfamily | Mostra a família de serviços para o produto adquirido ou cobrado. Por exemplo, armazenamento ou computação. |
| productOrderID | O identificador do ativo ou o nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. |
| productOrderName | O nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. |
| consumedService | Serviço consumido (taxonomia herdada), conforme usado em detalhes de uso de EA herdado. |
| meterID | Identificador medido para consumo medido. |
| meterName | Identifica o nome do medidor para consumo medido. |
| meterCategory | Identifica o serviço de nível superior para uso. |
| meterSubCategory | Define o tipo ou a subcategoria do serviço do Azure que pode afetar a taxa. |
| meterRegion | Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter. |
| ID da assinatura | Identificador exclusivo gerado pela Microsoft para a assinatura do Azure. |
| subscriptionName | Nome da assinatura do Azure. |
| Termo | Exibe o termo para a validade da oferta. Por exemplo, as instâncias reservadas mostram 12 meses de um termo anual da instância reservada. Para compras de uma vez ou compras recorrentes, o termo exibe um mês para SaaS, Azure Marketplace e suporte. Não aplicável ao consumo do Azure. |
| PublisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo de Publicador que identifica o Publicador como primeira parte, revendedor de terceiros ou agência de terceiros. |
| partNumber | Número de peça para a instância reservada não usada e os serviços do Azure Marketplace. |
| publisherName | Nome do editor do serviço, incluindo Publicadores da Microsoft ou de terceiros. |
| reservationId | Identificador para a compra de instância reservada. |
| reservaname | Nome da instância reservada. |
| reservationOrderId | OrderID para a instância reservada. |
| frequência | Frequência de pagamento para uma instância reservada. |
| resourceGroup | Nome do grupo de recursos do Azure usado para gerenciamento de recursos do ciclo de vida. |
| instanceID (ou) ResourceId | Identificador da instância de recurso. |
| resourceLocation | Nome do local do recurso. |
| Local | Local normalizado do recurso. |
| effectivePrice | O preço unitário efetivo do serviço, em moeda de preços. Exclusivo para um produto, uma família de serviços, um medidor e uma oferta. Usado com preços na folha de preços da conta de cobrança. Quando há preços em camadas ou uma quantidade incluída, ele mostra o preço combinado do consumo. |
| Quantidade | Quantidade medida comprada ou consumida. O valor do medidor usado durante o período de cobrança. |
| unitOfMeasure | Identifica a unidade em que o serviço é cobrado. Por exemplo, GB e horas. |
| pricingCurrency | A moeda que define o preço unitário. |
| billingCurrency | A moeda que define o custo cobrado |
| encargotype | Define o tipo de encargo que o custo representa no gerenciamento de custos do Azure, como compra e reembolso. |
| costinBillingCurrency | ExtendedCost ou custo combinado antes do imposto na moeda cobrada. |
| costinPricingCurrency | ExtendedCost ou custo combinado antes do imposto na moeda de preços para correlacionar com os preços. |
| **costinUSD** | ExtendedCost estimado ou custo combinado antes do imposto em USD. |
| **paygCostInBillingCurrency** | Mostra os custos se os preços estiverem em preços de varejo. Mostra os preços pagos conforme o uso na moeda de cobrança. Disponível somente em escopos de RBAC. |
| **paygCostInUSD** | Mostra os custos se os preços estiverem em preços de varejo. Mostra os preços pagos conforme o uso em USD. Disponível somente em escopos de RBAC. |
| exchangeRate | Taxa de câmbio usada para converter da moeda de preço para a moeda de cobrança. |
| exchangeRateDate | A data da taxa de câmbio que&#39;s usou para converter da moeda de preço para a moeda de cobrança. |
| isAzureCreditEligible | Indica se o custo é qualificado para pagamento por créditos do Azure. |
| serviceInfo1 | Um campo herdado que captura os metadados específicos do serviço opcional. |
| serviceInfo2 | Um campo herdado que captura os metadados específicos do serviço opcional. |
| additionalInfo | Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual. |
| marcas | Marca que você atribui ao medidor. Use marcas para agrupar registros de cobrança. Por exemplo, você pode usar marcas para distribuir os custos entre os departamentos que usam o medidor. |
| **partnerEarnedCreditRate** | Taxa de desconto aplicado se houver um acordo de crédito de parceiro (PEC) com base no acesso de link do administrador do parceiro. |
| **partnerEarnedCreditApplied** | Indica se o crédito obtido do parceiro foi aplicado. |


## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização dos dados de uso varia

A disponibilidade de seus dados de uso incorridos no Gerenciamento de Custos depende de alguns fatores, incluindo:

- A frequência com que os serviços do Azure (como Armazenamento, Computação, CDN e SQL) transmitem o uso.
- O tempo necessário para processar os dados de uso por meio do mecanismo de classificação e dos pipelines de gerenciamento de custos.

Alguns serviços transmitem o uso com mais frequência do que outros. Portanto, talvez você veja dados no Gerenciamento de Custos para alguns serviços antes do que para outros serviços que emitem dados menos frequentemente. Normalmente, o uso de serviços leva 8 a 24 horas para aparecer no Gerenciamento de Custos. Tenha em mente que os dados de um mês em aberto são atualizados conforme você incorre em mais uso porque as atualizações são cumulativas.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos podem não corresponder à fatura

Os dados históricos para ofertas com base em crédito e de pagamento antecipado podem não corresponder à fatura. Algumas ofertas pagas conforme o uso do Azure, MSDN e Visual Studio podem ter créditos do Azure e pagamentos avançados aplicados à fatura. No entanto, os dados históricos mostrados no gerenciamento de custos baseiam-se apenas nos encargos de consumo estimado. Os dados históricos do gerenciamento de custos não incluem pagamentos e créditos. Como resultado, os dados históricos mostrados para as ofertas a seguir podem não corresponder exatamente à sua fatura.

- Azure para estudantes (MS-AZR-0170P)
- Azure via Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Avaliação gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Confira também

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
