---
title: Migrar do EA para as APIs de Contrato de Cliente da Microsoft – Azure
description: Este artigo ajuda você a entender as consequências da migração de um Microsoft EA (Contrato Enterprise) para um Contrato de Cliente da Microsoft.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 1c556f2371ffba239ceee64fd4180143ae9b9230
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290056"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrar do Contrato Enterprise para as APIs de Contrato de Cliente da Microsoft

Este artigo ajuda você a entender a estrutura de dados, a API e outras diferenças de integração do sistema entre o EA (Contrato Enterprise) e as contas do MCA (Contrato de Cliente da Microsoft). O Gerenciamento de Custos do Azure dá suporte a APIs para os dois tipos de conta. Examine o artigo [Configurar conta de cobrança para o Contrato de Cliente da Microsoft](../manage/mca-setup-account.md) antes de continuar.

As organizações com uma conta de EA existente devem, além de configurar uma conta do MCA, também examinar este artigo. Anteriormente, renovar uma conta de EA exigia um mínimo de trabalho para mudar de um registro antigo para um novo. No entanto, migrar para uma conta de MCA requer esforço adicional. O esforço adicional é devido às alterações no subsistema de cobrança subjacente, que afetam todas as APIs e ofertas de serviço relacionadas ao custo.

## <a name="mca-apis-and-integration"></a>Integração de APIs do MCA

As APIs do MCA e a nova integração permitem que você:

- Tenha disponibilidade de API completa por meio de APIs nativas do Azure.
- Configure várias faturas em uma conta de cobrança.
- Acesse uma API combinada com a utilização do serviço do Azure, a utilização do Marketplace de terceiros e compras no Marketplace.
- Veja os custos entre os perfis de cobrança (o mesmo que os registros) usando o Gerenciamento de Custos do Azure.
- acesse novas APIs para mostrar os custos, seja notificado quando os custos excederem os limites predefinidos e exporte dados brutos automaticamente.

## <a name="migration-checklist"></a>Lista de verificação de migração

Os itens a seguir ajudam você a fazer a transição para APIs do MCA.

- Familiarize-se com a nova [conta de cobrança de Contrato de Cliente da Microsoft](../understand/mca-overview.md).
- Determine quais APIs você usa e veja quais delas são substituídas na seção a seguir.
- Familiarize-se com as [APIs REST do Azure Resource Manager](/rest/api/azure).
- Se você ainda não estiver usando as APIs do Azure Resource Manager, [registre seu aplicativo cliente com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Atualize qualquer código de programação para [usar a Autenticação do Azure AD](/rest/api/azure/#create-the-request).
- Atualize qualquer código de programação para substituir chamadas à API de EA por chamadas à API de MCA.
- Atualize o tratamento de erro para usar novos códigos de erro.
- Examine as ofertas de integração adicionais, como Cloudyn e Power BI, para outras ações necessárias.

## <a name="ea-apis-replaced-with-mca-apis"></a>APIs de EA substituídas por APIs de MCA

As APIs de EA usam uma chave de API para autenticação e autorização. As APIs de MCA usam a Autenticação do Azure AD.

| Finalidade | API de EA | API de MCA |
| --- | --- | --- |
| Saldo e créditos | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Uso (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Uso (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Uso do Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Períodos de cobrança | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Tabela de preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Compras de reserva | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Recomendações de reserva | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Uso de reserva | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> O serviço do Azure e o uso do Marketplace de terceiros estão disponíveis com a [API de Detalhes de Uso](/rest/api/consumption/usagedetails).

As seguintes APIs estão disponíveis para contas de cobrança de MCA:

| Finalidade | API de MCA (Contrato de Cliente da Microsoft) |
| --- | --- |
| Contas de cobrança<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Perfis de cobrança<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Seções da fatura<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faturas | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Assinaturas para cobrança | {scope}/billingSubscriptions |

<sup>2</sup> APIs retornam listas de objetos, que são escopos nos quais as experiências de Gerenciamento de Custos no portal do Azure e nas APIs operam. Para obter mais informações sobre escopos do Gerenciamento de Custos, veja [Entender e trabalhar com escopos](understand-work-scopes.md).

Se você usar qualquer API EA existente, precisará atualizá-las para dar suporte a contas de cobrança de MCA. A seguinte tabela mostra outras alterações de integração:

| Finalidade | Oferta antiga | Nova oferta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gerenciamento de Custos do Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Pacote de conteúdo e conector do [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |  [Conector do Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>APIs para obter saldo e créditos

A API [Obter Resumo do Saldo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) fornece um resumo mensal de:

- Saldos
- Novas compras
- Encargos do serviço Azure Marketplace
- Ajustes
- Encargos excedentes do serviço

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request).

A API Obter Resumo do Saldo é substituída pela API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Para obter os saldos disponíveis com a API de Saldo Disponível:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>APIs para obtenção de custo e uso

Obtenha, com as APIs a seguir, um detalhamento diário dos custos da utilização do serviço do Azure, do uso do Marketplace de terceiros e de outras compras do Marketplace. As APIs separadas a seguir foram mescladas para os serviços do Azure e o uso do Marketplace de terceiros. As APIs antigas são substituídas pela API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Ela adiciona compras do Marketplace, que até o presente momento só eram mostradas no resumo do saldo.

- [Obter detalhes de uso/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de uso/enviar](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Obter detalhes de uso/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter detalhes de uso/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Obter encargo de loja do marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Obter encargo de loja do marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request).

Todas as APIs anteriores são substituídas pela API de Detalhes de Consumo/Uso.

Para obter detalhes de uso com a API de Detalhes de Uso:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

A API de Detalhes de Uso, assim como todas as APIs de Gerenciamento de Custos, está disponível em vários escopos. Para custos faturados, como você normalmente receberia em um nível de registro, use o escopo do perfil de cobrança.  Para obter mais informações sobre escopos do Gerenciamento de Custos, veja [Entender e trabalhar com escopos](understand-work-scopes.md).

| Type | Formato de ID |
| --- | --- |
| Conta de cobrança | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Perfil de faturamento | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscription | `/subscriptions/{subscriptionId}` |
| Resource group | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Use os parâmetros de querystring a seguir para atualizar qualquer código de programação.

| Parâmetros antigos | Novos parâmetros |
| --- | --- |
| `billingPeriod={billingPeriod}` | Sem suporte |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

O corpo da resposta também foi alterado.

Corpo da resposta antigo:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Novo corpo da resposta:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

O nome da propriedade que contém a matriz de registros de uso se alterou de dados para _valores_. Cada registro costumava ter uma lista simples de propriedades detalhadas. No entanto, em cada registro, todos os detalhes agora estão em uma propriedade aninhada chamada _Propriedades_, com exceção das marcas. A nova estrutura é consistente com outras APIs do Azure. Alguns nomes de propriedade foram alterados. A tabela a seguir mostra as propriedades correspondentes.

| Propriedade antiga | Nova propriedade | Observações |
| --- | --- | --- |
| AccountId | N/D | O criador da assinatura não é acompanhado. Use invoiceSectionId (igual a departmentId). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/D | O criador da assinatura não é acompanhado. Use invoiceSectionName (igual a departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observe que essas propriedades são opostas. Se isAzureCreditEnabled for verdadeiro, ChargesBilledSeparately será falso. |
| ConsumedQuantity | quantidade | &nbsp; |
| ConsumedService | consumedService | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| ConsumedServiceId | Nenhum | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Data e usageStartDate | date | &nbsp;  |
| Dia | Nenhum | Analisa o dia com base na data. |
| DepartmentId | invoiceSectionId | Os valores exatos são diferentes. |
| DepartmentName | invoiceSectionName | Os valores exatos das cadeias de caracteres podem ser diferentes. Configure as seções da fatura para corresponderem aos departamentos, se necessário. |
| ExtendedCost e Custo | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| É cobrança recorrente | Nenhum | &nbsp;  |
| Location | local | &nbsp;  |
| MeterCategory | meterCategory | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| MeterId | meterId | Os valores exatos das cadeias de caracteres são diferentes. |
| MeterName | meterName | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| MeterRegion | meterRegion | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| MeterSubCategory | meterSubCategory | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| Month | Nenhum | Analisa o mês com base na data. |
| Nome da oferta | Nenhum | Use publisherName e productOrderName. |
| OfferID | Nenhum | &nbsp;  |
| Número do pedido | Nenhum | &nbsp;  |
| PartNumber | Nenhum | Use meterId e productOrderName para identificar preços de maneira exclusiva. |
| Nome do Plano | productOrderName | &nbsp;  |
| Produto | Produto |   |
| ProductId | productId | Os valores exatos das cadeias de caracteres são diferentes. |
| Nome do editor | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Os valores exatos das cadeias de caracteres são diferentes. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Nenhum | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/D | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| ServiceTier | meterSubCategory | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| StoreServiceIdentifier | N/D | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Marcas | marcas | A propriedade marcas aplica-se ao objeto raiz, não à propriedade de propriedades aninhadas. |
| UnitOfMeasure | unitOfMeasure | Os valores exatos das cadeias de caracteres são diferentes. |
| usageEndDate | date | &nbsp;  |
| Ano | Nenhum | Analisa o ano com base na data. |
| (novo) | billingCurrency | A moeda usada para a cobrança. |
| (novo) | billingProfileId | ID exclusiva do perfil de cobrança (a mesma que a do registro). |
| (novo) | billingProfileName | Nome do perfil de cobrança (o mesmo que o do registro). |
| (novo) | chargeType | Use para diferenciar a utilização do serviço do Azure, o uso do Marketplace e as compras. |
| (novo) | invoiceId | A ID exclusiva da fatura. Vazio para o mês atual e em aberto. |
| (novo) | publisherType | Tipo de distribuidor para compras. Vazio para uso. |
| (novo) | serviceFamily | Tipo de compra. Vazio para uso. |
| (novo) | servicePeriodEndDate | Data de término do serviço comprado. |
| (novo) | servicePeriodStartDate | Data de início do serviço comprado. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API de Períodos de Cobrança substituída por API de Faturas

As contas de cobrança de MCA não usam períodos de cobrança. Em vez disso, elas usam faturas para delimitar os custos para períodos de cobrança específicos. A [API de Períodos de Cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) é substituída pela API de Faturas. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request).

Para obter faturas com a API de Faturas:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>APIs de Tabelas de Preços

Esta seção aborda as APIs de Tabelas de Preços existentes e fornece recomendações para migrar para a API de Tabelas de Preços para Contratos de Cliente da Microsoft. Ela também aborda a API de Tabelas de Preços para Contratos de Cliente da Microsoft e explica os campos nas tabelas de preços. As APIs de [Obter tabelas de preços Enterprise](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) e [Obter períodos de cobrança Enterprise](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) são substituídas pela API de Tabelas de Preços para Contrato de Cliente da Microsoft (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). A nova API dá suporte aos formatos JSON e CSV, em formatos REST assíncronos. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>APIs de Cobrança Enterprise

Você usou as APIs de Cobrança Enterprise com Registros Enterprise para obter informações sobre o preço e o período de cobrança. A autenticação e a autorização usaram tokens Web do Azure Active Directory.

Para obter os preços aplicáveis para o Registro Enterprise especificado com as APIs de Tabelas de Preços e de Período de Cobrança:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API de Tabelas de Preços para Contratos de Cliente da Microsoft

Use a API de Tabelas de Preços para Contratos de Cliente da Microsoft para exibir os preços para todos os serviços de consumo do Marketplace e de consumo do Azure. Os preços mostrados para o perfil de cobrança se aplicam a todas as assinaturas que pertencem ao perfil de cobrança.

Use a API de Tabelas de Preços para exibir os dados da Tabela de Preços dos Serviços de Consumo do Azure no formato CSV:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Use a API de Tabelas de Preços para exibir os dados da Tabela de Preços dos Serviços de Consumo do Azure no formato JSON:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

O uso da API retorna a tabela de preços da conta inteira. No entanto, você também pode obter uma versão condensada da tabela de preços no formato PDF. O resumo inclui os serviços de consumo do Marketplace e consumo do Azure que são cobrados por uma fatura específica. A fatura é identificada pelo {invoiceId}, que é igual ao **número da fatura** mostrado nos arquivos PDF de resumo da fatura. Veja um exemplo.

![Imagem de exemplo mostrando o número da fatura que corresponde à InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Para exibir informações de fatura com a API de Tabelas de Preços no formato CSV:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para exibir informações de fatura com a API de Tabelas de Preços no formato JSON:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Você também pode ver os preços estimados para qualquer serviço de consumo do Marketplace ou de consumo do Azure no ciclo de serviço ou ciclo de cobrança aberto atual.

Para exibir os preços estimados dos serviços de consumo com a API de Tabelas de Preços no formato CSV:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Para exibir os preços estimados dos serviços de consumo com a API de Tabelas de Preços no formato JSON:

| Método | URI da solicitação |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

As APIs de Tabelas de Preços do Contrato de Cliente da Microsoft são *APIs REST assíncronas*. As respostas para as APIs foram alteradas em relação às APIs síncronas mais antigas. O corpo da resposta da API também foi alterado.

#### <a name="old-response-body"></a>Corpo da resposta antigo

Aqui está um exemplo da resposta da API REST síncrona:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Novo corpo da resposta

As APIs dão suporte ao formato [assíncrono REST do Azure](../../azure-resource-manager/management/async-operations.md). Chame a API usando GET e você receberá a seguinte resposta:

```
No Response Body

HTTP Status 202 Accepted
```

Os seguintes cabeçalhos são enviados com a localização da saída:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Faça outra chamada GET para a localização. A resposta para a chamada GET é a mesma até que a operação atinja um estado de conclusão ou de falha. Quando concluída, a resposta para a localização de chamada GET retorna a URL de download. Isso ocorre como se a operação tivesse sido executada simultaneamente. Aqui está um exemplo:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

O cliente também pode fazer uma chamada GET para o `Azure-AsyncOperation`. O ponto de extremidade retorna o status da operação.

A tabela a seguir mostra os campos da API Obter Tabelas de Preços Enterprise mais antiga. Ela inclui campos correspondentes na nova tabela de preços para Contrato de Cliente da Microsoft:

| Propriedade antiga | Nova propriedade | Observações |
| --- | --- | --- |
| billingPeriodId  | _Não aplicável_ | Não aplicável. Para Contratos de Cliente da Microsoft, a fatura e a tabela de preços associada substituíram o conceito de billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Os valores exatos das cadeias de caracteres podem ser diferentes. |
| includedQuantity  | includedQuantity | Não aplicável aos serviços nos Contratos de Cliente da Microsoft. |
| partNumber  | _Não aplicável_ | Em vez disso, use uma combinação de productOrderName (igual a offerID) e meterID. |
| unitPrice  | unitPrice | O preço unitário é aplicável aos serviços consumidos em Contratos de Cliente da Microsoft. |
| currencyCode  | pricingCurrency | Os Contratos de Cliente da Microsoft têm representações de preço na moeda de precificação e na moeda de cobrança. O currencyCode corresponde ao pricingCurrency nos Contratos de Cliente da Microsoft. |
| offerID | productOrderName | Em vez de OfferID, você pode usar productOrderName, mas isso não é o mesmo que OfferID. No entanto, productOrderName e meter determinam os preços nos Contratos de Cliente da Microsoft relacionados a meterId e OfferID em registros herdados. |

## <a name="consumption-price-sheet-api-operations"></a>Operações de API de Tabelas de Preços de Consumo

Para Contratos Enterprise, você usou as operações [Obter](/rest/api/consumption/pricesheet/get) e [Obter por Período de Cobrança](/rest/api/consumption/pricesheet/getbybillingperiod) da API de Tabelas de Preços de Consumo para um escopo por subscriptionId ou por um período de cobrança. A API usa a autenticação do Azure Resource Management.

Para obter as informações da tabela de preços para um escopo com a API de Tabelas de Preços:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Para obter informações da tabela de preços por período de cobrança com a API de Tabelas de Preços:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Para Contratos de Clientes da Microsoft, use os seguintes pontos de extremidade de API em vez daqueles mostrados acima:

**API de Tabelas de Preços para Contratos de Cliente da Microsoft (API REST assíncrona)**

Essa API é para Contratos de Cliente da Microsoft e fornece atributos adicionais.

**Tabela de preços para um escopo de perfil de cobrança em uma conta de cobrança**

Essa API é a API existente. Ela foi atualizada para fornecer a tabela de preços de um perfil de cobrança em uma conta de cobrança.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Tabela de preços para um escopo por conta de cobrança

Azure Resource Manager autenticação é usada quando você obtém a tabela de preços no escopo de registro em uma conta de cobrança.

Para obter a tabela de preços na conta de registro em uma conta de cobrança:

| Método | URI da solicitação |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Para um Contrato de Cliente da Microsoft, use as informações na seção a seguir. Elas fornecem as propriedades de campo usadas para Contratos de Cliente da Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Tabela de preços para um escopo de perfil de cobrança em uma conta de cobrança

A tabela de preços atualizada por API de conta de cobrança obtém a tabela de preços no formato CSV. Para obter a tabela de preços no escopo do perfil de cobrança para um MCA:

| Método | URI da solicitação |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

No escopo de registro do EA, a resposta da API e as propriedades são idênticas. As propriedades correspondem às mesmas propriedades de MCA.

As propriedades mais antigas para [APIs de Tabelas de Preços do Azure Resource Manager](/rest/api/consumption/pricesheet) e as mesmas novas propriedades estão na tabela a seguir.

| Propriedade antiga de API de Tabelas de Preços do Azure Resource Manager  | Nova propriedade da API de Tabelas de Preços do Contrato de Cliente da Microsoft   | DESCRIÇÃO |
| --- | --- | --- |
| ID de medidor | _meterId_ | Identificador exclusivo do medidor. Igual a meterID. |
| Nome do medidor | meterName | Nome do medidor. O medidor representa o recurso implantável de um serviço do Azure. |
| Categoria de medidor  | serviço | Nome da categoria de classificação do medidor. Igual ao serviço na Tabela de Preços do Contrato de Cliente da Microsoft. Os valores exatos das cadeias de caracteres são diferentes. |
| Subcategoria de medidor | meterSubCategory | Nome da categoria de subclassificação do medidor. Com base na classificação de diferenciação do conjunto de recursos de alto nível no serviço. Por exemplo, BD SQL Básico versus BD SQL Standard. |
| Região do medidor | meterRegion | &nbsp;  |
| Unidade | _Não aplicável_ | Pode ser analisado segundo unitOfMeasure. |
| Unidade de medida | unitOfMeasure | &nbsp;  |
| Número da peça | _Não aplicável_ | Em vez do número de peça, use productOrderName e MeterID para identificar de maneira exclusiva o preço de um perfil de cobrança. Os campos são listados na fatura do MCA em vez do número da peça em faturas do MCA. |
| Preço unitário | unitPrice | Preço unitário do Contrato de Cliente da Microsoft. |
| Código de moeda | pricingCurrency | Os Contratos de Cliente da Microsoft representam preços na moeda de precificação e na moeda de cobrança. O código da moeda corresponde ao pricingCurrency nos Contratos de Cliente da Microsoft. |
| Quantidade incluída | includedQuantity | Não aplicável aos serviços nos Contratos de Cliente da Microsoft. Mostrar com valores de zero. |
|  ID da oferta  | productOrderName | Em vez de OfferID, use productOrderName. Não é o mesmo que OfferID, no entanto, o productOrderName e o medidor determinam os preços nos Contratos de Cliente da Microsoft. Relacionado a meterId e OfferID em registros herdados. |

O preço dos Contratos de Cliente da Microsoft é definido de maneira diferente dos Contratos Enterprise. O preço dos serviços no Registro Enterprise é exclusivo para o produto, o número de peça, o medidor e a oferta. O número da peça não é usado em Contratos de Cliente da Microsoft.

O preço do serviço de Consumo do Azure que faz parte de um Contrato de Cliente da Microsoft é exclusivo para productOrderName e meterID. Eles representam o medidor de serviço e o plano de produto.

Para reconciliar entre a planilha de preços e o uso na API de detalhes de uso, você pode usar o productOrderName e o meterID.

Os usuários que têm direitos de proprietário, colaborador, leitor e gerenciador de faturas do perfil de cobrança podem baixar a tabela de preços.

A tabela de preços inclui preços para serviços cujo preço é baseado no uso. Os serviços incluem consumo do Azure e consumo do Marketplace. O preço mais recente no final de cada período de serviço é bloqueado e aplicado ao uso em um período de serviço. Para os serviços de consumo do Azure, o período de serviço é geralmente de um mês civil.

### <a name="retired-price-sheet-api-fields"></a>Campos desativados da API de Tabelas de Preços

Os campos a seguir não estão disponíveis nas APIs de Tabelas de Preços do Contrato de Cliente da Microsoft ou então têm os mesmos campos.

|Campo desativado| DESCRIÇÃO|
|---|---|
| billingPeriodId | Não aplicável. Corresponde a InvoiceId para um MCA. |
| offerID | Não aplicável. Corresponde a productOrderName em um MCA. |
| meterCategory  | Não aplicável. Corresponde a Serviço em um MCA. |
| unit | Não aplicável. Pode ser analisado segundo unitOfMeasure. |
| currencyCode | O mesmo que o pricingCurrency em um MCA. |
| meterLocation | O mesmo que a meterRegion em um MCA. |
| partNumber partnumber | Não aplicável porque o número de peça não está listado em faturas de MCA. Em vez de número de peça, use a combinação de meterId e productOrderName para identificar os preços com exclusividade. |
| totalIncludedQuantity | Não aplicável. |
| pretaxStandardRate  | Não aplicável. |

## <a name="reservation-instance-charge-api-replaced"></a>API de Cobrança de Instância de Reserva substituída

Você pode obter transações de cobrança para compras de reserva com a [API de Cobrança de Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). A nova API inclui todas as compras, incluindo ofertas do Marketplace de terceiros. Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request). A API de Cobrança de Instância Reservada é substituída pela API de Transações.

Para obter transações de compra de reserva com a API de Transações:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>APIs de Recomendações substituídas

As APIs de Recomendações de Compra de Instância Reservada fornecem o uso da máquina virtual nos últimos 7, 30 ou 60 dias. As APIs também fornecem recomendações de compra de reserva. Elas incluem:

- [API Compartilhada de Recomendações de Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API Individual de Recomendações de Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request). As APIs de recomendações de reserva listadas anteriormente são substituídas pela API [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Para obter recomendações de reserva com a API de Recomendações de Reserva:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>APIs de Uso de Reserva substituídas

Você pode obter o uso de reserva em um registro com a API de Uso da Instância Reservada. Se houver mais de uma instância reservada em um registro, você também poderá obter o uso de todas as compras de instância reservada usando essa API.

Elas incluem:

- [Detalhes de Uso da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Resumo de Uso da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Todas as APIs de consumo são substituídas por APIs nativas do Azure que usam o Azure AD para autenticação e autorização. Para obter mais informações sobre como chamar as APIs REST do Azure, veja [Introdução ao REST](/rest/api/azure/#create-the-request). As APIs de recomendações de reserva listadas anteriormente são substituídas pelas APIs [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Para obter detalhes de reserva com a API de Detalhes da Reserva:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Para obter resumos de reserva com a API de Resumos da Reserva:

| Método | URI da solicitação |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Mover do Cloudyn para o Gerenciamento de Custos

As organizações que usam [Cloudyn](https://cloudyn.com) devem começar a usar o [Gerenciamento de Custos do Azure](https://azure.microsoft.com/services/cost-management/) para necessidades de gerenciamento de custos. O Gerenciamento de Custos está disponível no portal do Azure sem integração e com uma latência de oito horas. Para obter mais informações, confira a [Documentação do Gerenciamento de Custos](../index.yml).

Com o Gerenciamento de Custos do Azure, você pode:

- Exibir custos ao longo do tempo em um orçamento predefinido. Analisar padrões de custo diários para identificar e interromper anomalias de gasto. Segmente os custos por marca, grupo de recursos, serviço e localização.
- Criar orçamentos para definir limites de uso e de custos e receber notificações quanto limites importantes forem atingidos. Defina a automação com grupos de ações para disparar eventos personalizados e imponha limites rígidos conforme seus critérios.
- Otimizar o custo e o uso com recomendações do Assistente do Azure. Descubra otimizações de compra com reservas, reduza máquinas virtuais subutilizadas e exclua recursos não utilizados para permanecer dentro dos orçamentos.
- Agendar uma exportação de dados de uso e de custo para publicar um arquivo CSV em sua conta de armazenamento diariamente. Automatize a integração a sistemas externos para manter os dados de cobrança sincronizados e atualizados.

## <a name="power-bi-integration"></a>Integração com o Power BI

Você também pode usar o Power BI para preparar relatórios de custo. O [conector do Gerenciamento de Custos do Azure](/power-bi/desktop-connect-azure-cost-management) para Power BI Desktop pode ser usado para criar relatórios personalizados e avançados que ajudam você a entender melhor seus gastos com o Azure. Atualmente, o conector do Gerenciamento de Custos do Azure dá suporte a clientes com um Contrato de Cliente da Microsoft ou um EA (Contrato Enterprise).

## <a name="next-steps"></a>Próximas etapas

- Leia a [documentação do Gerenciamento de Custos](../index.yml) para saber como monitorar e controlar os gastos do Azure. Leia-a também caso você queira otimizar o uso de recursos com o Gerenciamento de Custos.
