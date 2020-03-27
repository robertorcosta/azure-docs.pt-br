---
title: Termos de arquivo de encargos e uso do Azure do Contrato de Cliente da Microsoft
description: Saiba como ler e entender as seções do CSV de uso e encargos do Azure de seu perfil de cobrança.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: banders
ms.openlocfilehash: c6883dfd0f4c3bc11afdd68ead8c8c84729b1880
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065696"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Termos no arquivo de uso e encargos do Azure de um Contrato de Cliente da Microsoft

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

O arquivo CSV de uso e encargos do Azure contém os encargos de uso diário e no nível de medidor do período de cobrança atual.

Para obter o arquivo de uso e encargos do Azure, confira [Exibir e baixar o uso e os encargos do Azure para seu Contrato de Cliente da Microsoft](download-azure-daily-usage.md). Ele está disponível em um formato de arquivo .csv (valores separados por vírgulas) que pode ser aberto em um aplicativo de planilhas.

Encargos de uso são o total de encargos **mensais** em uma assinatura. Os encargos de uso não levam em consideração nenhum crédito nem desconto.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Alterações de uso e encargos do Azure EA

Se você for um cliente do EA, observará que os termos no arquivo CSV de uso do perfil de cobrança do Azure são diferentes dos termos no arquivo CSV de uso do Azure EA. Este é um mapeamento dos termos de uso do EA para os termos de uso do perfil de cobrança:

| CSV de uso do Azure EA | CSV de uso e encargos do Azure do Contrato de Cliente da Microsoft |
| --- | --- |
| Data | date |
| Month| date |
| Dia | date |
| Ano | date |
| Produto | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantidade |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Marcas | marcas |
| StoreServiceIdentifier | N/D |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Termos e descrições detalhados

Os termos a seguir são mostrados no arquivo de uso e encargos do Azure.

Termo | Descrição
--- | ---
invoiceId | A ID de documento exclusiva listada no PDF da fatura
previousInvoiceId | Referência a uma fatura original se esse item de linha é um reembolso
billingAccountName | Nome da conta de cobrança
billingAccountId | Identificador exclusivo da conta de cobrança raiz
billingProfileId | Nome do perfil de cobrança que acumula os encargos faturados
billingProfileName | Identificador exclusivo do perfil de cobrança que acumula os encargos faturados
invoiceSectionId | Identificador exclusivo da seção Fatura
invoiceSectionName | Nome da seção da fatura
costCenter | O centro de custo definido na assinatura para acompanhamento dos custos (disponível somente em períodos de cobrança em aberto)
billingPeriodStartDate | A data de início do período de cobrança para o qual a fatura é gerada
billingPeriodEndDate | A data de término do período de cobrança para o qual a fatura é gerada
servicePeriodStartDate | A data de início do período de classificação que definiu e bloqueou o preço para o serviço consumido ou adquirido
servicePeriodEndDate | A data de término do período de classificação que definiu e bloqueou o preço para o serviço consumido ou adquirido
date | Para encargos baseados no uso do Azure e do Marketplace, essa é a data de classificação. Para compras avulsas (reservas, Marketplace) ou encargos recorrentes fixos (ofertas de suporte), essa é a data de compra.
serviceFamily | Família de serviços à qual o serviço pertence
productOrderId | Identificador exclusivo do pedido do produto
productOrderName | Nome exclusivo do pedido do produto
consumedService | Nome do serviço consumido
meterId | O identificador exclusivo do medidor
meterName | O nome do medidor
meterCategory | Nome da categoria de classificação do medidor. Por exemplo, *Serviços de Nuvem*, *Rede* etc.
meterSubCategory | Nome da categoria de subclassificação do medidor
meterRegion | Nome da região em que o medidor do serviço está disponível. Identifica a localização do data center para determinados serviços que são cobrados com base na localização do data center.
oferta | Nome da oferta comprada
PayGPrice | Preço de varejo do recurso.
PricingModel | Identificador que indica como o medidor é cobrado (valores: Sob demanda, Reserva, Spot)
productId | Identificador exclusivo do produto que acumula os encargos
product | Nome do produto que acumula os encargos
ID da assinatura | Identificador exclusivo da assinatura que acumula os encargos
subscriptionName | Nome da assinatura que acumula os encargos
reservationId | Identificador exclusivo para a instância de reserva adquirida
reservationName | Nome da instância de reserva adquirida
publisherType | Tipo de distribuidor (valores: firstParty, thirdPartyReseller e thirdPartyAgency)
publisherName | Distribuidor de serviços do Marketplace
resourceGroupId | Identificador exclusivo do grupo de recursos associado ao recurso
resourceGroupName | Nome do grupo de recursos associado ao recurso
resourceId | Identificador exclusivo da instância do recurso
resourceType | Tipo de instância de recurso
resourceLocation | Identifica a localização do data center em que o recurso está sendo executado.
local | Localização normalizada do recurso se localizações de recursos diferentes estão configuradas para as mesmas regiões
quantidade | O número de unidades adquiridas ou consumidas
unitOfMeasure | A unidade de medida para cobrança do serviço. Por exemplo, os serviços de computação são cobrados por hora.
chargeType | O tipo de encargo. Valores: <ul><li>AsCharged-Usage: Encargos acumulados com base no uso de um serviço do Azure. Isso inclui o uso nas VMs que não são cobradas devido às instâncias reservadas.</li><li>AsCharged-PurchaseMarketplace: Encargos avulsos ou recorrentes fixos de compras do Marketplace</li><li>AsCharged-UsageMarketplace: Encargos de serviços do Marketplace cobrados com base em unidades de consumo</li></ul>
isAzureCreditEligible | Sinalizador que indica se o preço do serviço está qualificado a ser pago pelo uso de créditos Azure (valores: Verdadeiro, Falso)
serviceInfo1 | Metadados específicos do serviço
serviceInfo2 | Campo herdado que captura os metadados específicos do serviço opcional
additionalInfo | Metadados adicionais específicos ao serviço.
marcas | Marcas atribuídas ao recurso

### <a name="make-sure-that-charges-are-correct"></a>Verifique se os encargos estão corretos

Caso deseje ter certeza de que os encargos no arquivo de uso detalhado estão corretos, verifique-os. Confira [Entender os encargos na fatura de seu perfil de cobrança](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Exibir e baixar a fatura do Microsoft Azure](download-azure-invoice.md)
- [Exibir e baixar o uso e os encargos do Microsoft Azure](download-azure-daily-usage.md)
