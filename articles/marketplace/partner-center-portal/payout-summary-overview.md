---
title: Visão geral do resumido de pagamento-Azure Marketplace
description: O resumo de pagamento mostra detalhes sobre o dinheiro que você ganhou com sua oferta. Ele também permite saber quando você receberá os pagamentos e quanto você será pago.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 48644f2f8148a7aa1974be0d7f761e9b3a55612d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783507"
---
# <a name="payout-summary-overview"></a>Visão geral do resumido de pagamento

O [Resumo do pagamento](./payout-summary.md) mostra detalhes sobre o dinheiro que você ganhou com a Microsoft. Ele também permite que você saiba quando receberá pagamentos e quanto será pago.

Se você vender ofertas no Azure Marketplace, também verá informações sobre pagamentos bem-sucedidos no resumo do pagamento. Para obter mais informações sobre o pagamento do Azure Marketplace, consulte [políticas de participação do Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e [contrato de Microsoft Azure Marketplace editor](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Para ser elegível para pagamento, seus prosseguimentos devem alcançar o [limite de pagamento](./payment-thresholds-methods-timeframes.md) de $50. Para obter detalhes sobre o limite de pagamento, consulte o [contrato do Microsoft Azure Marketplace Publisher](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

Todos os relatórios e informações para a opção de publicação de transação estão disponíveis na seção análise do Partner Center, acessadas usando esse ícone no canto superior direito do portal:

![Ilustra o ícone de pagamento no canto superior direito do portal do Partner Center.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Funções e permissões

Essas são funções e permissões para acessar o relatório de pagamento:

| Relatórios/páginas | Proprietário da conta | Gerente | Desenvolvedor | Colaborador de negócios | Colaborador financeiro | Comerciante |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de aquisição (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Sem acesso | Sem acesso | Pode exibir | Sem acesso |
| Relatório/respostas de comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Sem acesso | Sem acesso | Pode exibir e enviar comentários |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de integridade (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Relatório de uso | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil de imposto | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo do pagamento | Pode exibir | Sem acesso | Sem acesso | Sem acesso | Pode exibir | Sem acesso  |
| | | | | | | |

## <a name="payout-report-differences"></a>Diferenças de relatório de pagamento

Essas são as diferenças no relatório de pagamento entre Portal do Cloud Partner (antigo) e o Partner Center (novo):

| Portal de Parceiros de Nuvem | Partner Center |
| --- | --- |
| **Link**:https://cloudpartner.azure.com/ | **Link**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory ehttps://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navegação**: relatório de pagamento fornecido no pagamento de informações | **Navegação**: relatório de pagamento fornecido no Partner Center – ícone de pagamento |
| **Escopo**:<ul><li>A transação por item de linha está visível para coleta em andamento, coletada e paga.</li><li>Relatório – mostra todos os itens de linha após a criação da ordem de compra, incluindo a coleta em andamento e a cobrança em andamento, bem como o status da coleta e os itens de linha que ainda não estão qualificados para serem pagos.</li></ul> | **Escopo**:<ul><li>Mostra os itens de linha depois que eles são considerados ganhos qualificados.</li><li>Os clientes pagam para a Microsoft primeiro e, em seguida, os ISVs podem ver o relatório de pagamento iniciando.</li><li>O relatório de pagamento não mostrará a coleção em andamento e a cobrança em andamento.</li></ul> |
| **A transação não está pronta para pagamento**: cobrança em andamento | **Transação não pronta para**pagamento: próximo pagamento estimado: o status do pagamento está no estado não processado. |
| **Status do pagamento**: n/a | **Status do pagamento**:<ul><li>Não processado: a conquista está qualificada para pagamento.</li><li>Futuro: a conquista será enviada ao Publicador no pagamento mensal seguinte.</li><li>Enviado: o pagamento foi enviado ao seu banco.</li></ul> |
| | |

## <a name="payment-schedules"></a>Planos de pagamento

Para uma discussão sobre planos de pagamento, incluindo períodos de retenção, visibilidade do parceiro e quando o cliente usa um cartão de crédito ou fatura, consulte a seção [planos de pagamento](./payout-policy-details.md#payment-schedules) do tópico **detalhes** do pagamento.

## <a name="transaction-history-download-export"></a>Exportação de download de histórico de transações

Essa opção fornece um download de cada item de linha de conquista que você vê na página Histórico de transações. Isso inclui o tipo de conquista, data, valor da transação associada, cliente, produto e outros detalhes transacionais relacionados ao programa incentivos.

| Nome da coluna | Descrição |
| --- | --- |
| ganhandoid | Identificador exclusivo para cada conquista |
| participante da | A principal identidade do parceiro ganhando sob o programa |
| participantIdType | ID do programa para programas de incentivo e vendedor se o programa for para programas de armazenamento e Azure Marketplace |
| participantename | Nome do parceiro de conquista |
| partnerCountryCode | Localização/país do parceiro de conquista |
| programName | Nome do programa de incentivo/loja |
| transactionId | Identificador exclusivo para a transação |
| transactionCurrency | Moeda na qual a transação original do cliente ocorreu (não é a moeda do local do parceiro) |
| transactionDate | Data da transação. Útil para programas em que muitas transações contribuem para uma conquista |
| transactionExchangeRate | Taxa de câmbio usada para mostrar a transação correspondente valor USD |
| transactionAmount | Valor da transação na moeda da transação original com base na qual a conquista é gerada |
| transactionAmountUSD | Valor da transação em dólares dos EUA (USD) |
| alavanca | Regra de negócios para a conquista |
| earningRate | Taxa de incentivo aplicada ao valor da transação para gerar uma conquista |
| quantidade | Quantidade cobrada para programas transacionais. Varia de acordo com o programa |
| quanttype | Tipo de quantidade, por exemplo: quantidade cobrada, usuários ativos mensais (MAU) |
| ganhandotype | Indica se é Tarifa, desconto, Coop, venda e assim por diante |
| earningAmount | Conquistando valor na moeda da transação original |
| earningAmountUSD | Conquistando valor em USD |
| earningDate | Data da conquista |
| calculationDate | Data em que a conquista foi calculada no sistema |
| earningExchangeRate | Taxa de câmbio usada para mostrar o valor USD correspondente |
| exchangeRateDate | Data da taxa de câmbio usada para calcular EarningAmount USD |
| paymentAmountWOTax | Ganhando valor (sem imposto) em pagar para a moeda &quot;somente&quot; para pagamentos enviados |
| paymentCurrency | Pague para moeda escolhida pelo parceiro no perfil de pagamento. Mostrado somente para pagamentos enviados |
| paymentExchangeRate | Taxa de câmbio usada para calcular paymentAmountWOTax na moeda de pagamento usando ExchangeRateDate |
| pagamento de | Identificador exclusivo para o pagamento. Esse número é visível em seu extrato bancário |
| paymentStatus | Status do pagamento |
| paymentStatusDescription | Descrição do status de pagamento |
| customerId | Estará sempre em branco |
| customerName | Estará sempre em branco |
| partNumber | Estará sempre em branco |
| productName | Nome do produto vinculado à transação |
| productId | Identificador de produto exclusivo |
| parentProductId | Identificador exclusivo do produto pai. Se não houver um produto pai para a transação, então, ID do produto pai = ID do produto. |
| parentProductName | Nome do produto pai. Se não houver um produto pai para a transação, então nome do produto pai = nome do produto. |
| productType | Tipo de produto (como aplicativo, complemento e jogo) |
| invoiceNumber | Número da nota fiscal (somente para contratos empresariais) |
| revendedorid | Identificador do revendedor |
| revendedorname | Nome do revendedor |
| transactionType | Tipo de transação (como compra, reembolso, reversão e estorno) |
| localProviderSeller | Provedor local/vendedor de registro |
| taxRemitted | Valor do imposto remetido (vendas, uso ou impostos IVA/GST). |
| taxRemitModel | Parte responsável por remeter impostos (vendas, uso ou impostos IVA/GST). |
| storeFee | O valor retido pela Microsoft como uma taxa para tornar o aplicativo ou complemento disponível no Marketplace comercial. |
| transactionPaymentMethod | Instrumento de pagamento de cliente usado para a transação (como cartão, cobrança de operadora móvel e PayPal) |
| tpan | Rede de terceiros do AD |
| CustomerCountryComparer | País do cliente |
| customerCity | Cidade do cliente |
| customerstate | Estado do cliente |
| customerZip | CEP do cliente/CEP |
| TenantID | A ID do locatário |
| externalReferenceId | Identificador exclusivo do programa |
| externalReferenceIdLabel | Rótulo de identificador exclusivo |
| transactionCountryCode | Código do país no qual a transação ocorreu |
| taxCountry | País do cliente |
| taxState | Estado do cliente |
| taxCity | Cidade do cliente |
| taxZipCode | CEP do cliente |
| LicensingProgramName | Nome do programa de licenciamento |
| Código do programa | Cadeia de caracteres para mapear com o nome do programa |
| earningAmountInLastPaymentCurrency | Ganhando valor na última moeda de pagamento (o campo estará vazio se nenhum pagamento anterior tiver sido pago) |
| lastPaymentCurrency | Última moeda de pagamento (o campo estará vazio se nenhum pagamento anterior tiver sido pago) |
| AssetId | O identificador exclusivo para os pedidos do cliente para o serviço do Marketplace. Ele representa os itens de linha de compra. Pode haver vários ativos. |
| OrderId | Relacionado à fatura de um cliente |
| LineItemId | Linha individual na fatura de um cliente |
| País/Região do cliente | O nome do país fornecido pelo cliente. Isso pode ser diferente do país na assinatura do Azure de um cliente. |
| EmailAddress do cliente | O endereço de email fornecido pelo cliente. Isso pode ser diferente do endereço de email na assinatura do Azure de um cliente. |
| SkuId | ID do SKU conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode estar associada a uma única oferta. |

> [!NOTE]
> Todos os relatórios e informações sobre a opção de publicação de transação podem ser encontrados na seção análise do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte
Para obter suporte de cobrança, contate o [suporte do editor](https://partner.microsoft.com/support/v2/?stage=1)do Marketplace comercial.

## <a name="next-step"></a>Próxima etapa

- [Resumos de pagamento](./payout-summary.md)
