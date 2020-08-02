---
title: Visão geral do resumo do pagamento - Microsoft Azure Marketplace
description: O resumo do Pagamento mostra detalhes sobre o dinheiro que você ganhou com sua oferta. Ele também permite que você saiba quando receberá os pagamentos e quanto será pago.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2e7658e965931dd78a6db0e2c9fee36edffff98b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479805"
---
# <a name="payout-summary-overview"></a>Visão geral do resumo do pagamento

O [resumo do Pagamento](./payout-summary.md) mostra detalhes sobre o dinheiro que você ganhou com a Microsoft. Ele também permite que você saiba quando receberá os pagamentos e quanto será pago.

Se você vender ofertas no Azure Marketplace, também verá as informações sobre pagamentos bem-sucedidos no resumo do Pagamento. Para obter mais informações sobre o pagamento do Microsoft Azure Marketplace, confira [Políticas de participação do Microsoft Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e [Contrato do editor do Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Para se qualificar para o pagamento, suas receitas devem atingir o [limite de pagamento](./payment-thresholds-methods-timeframes.md) de US$ 50. Para obter detalhes sobre o limite de pagamento, confira o [Contrato do editor do Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

Para exibir os detalhes do pagamento, entre no [Partner Center](https://partner.microsoft.com/dashboard/home) e selecione o ícone de pagamento no canto superior direito da tela:

![Ilustra o ícone de Pagamento no canto superior direito do portal do Partner Center.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Funções e permissões

Estas são as funções e permissões para acessar o relatório de pagamento:

| Relatórios/páginas | Proprietário da conta | Gerente | Desenvolvedor | Colaborador comercial | Colaborador financeiro | Marketer |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de aquisição (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Sem acesso | Sem acesso | Pode exibir | Sem acesso |
| Relatório/respostas de comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Sem acesso | Sem acesso | Pode exibir e enviar comentários |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de integridade (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Relatório de uso | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil fiscal | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo do pagamento | Pode exibir | Sem acesso | Sem acesso | Sem acesso | Pode exibir | Sem acesso  |
| | | | | | | |

## <a name="payment-schedules"></a>Agendamentos de pagamento

Para uma discussão dos agendamentos de pagamento, incluindo períodos de retenção, visibilidade do parceiro e quando o cliente usa um cartão de crédito ou fatura, veja a seção [Agendamentos de pagamento](./payout-policy-details.md#payment-schedules) do tópico **Detalhes de pagamento**.

## <a name="transaction-history-download-export"></a>Exportação de download do histórico de transações

Esta opção fornece um download de cada item de linha do ganho que você vê na página Histórico de transações. Isso inclui o tipo de ganho, data, valor da transação associada, cliente, produto e outros detalhes da transação relacionados ao programa de Incentivos.

| Nome da coluna | Descrição |
| --- | --- |
| earningId | Identificador exclusivo de cada ganho |
| participantID | A identidade principal do parceiro que ganha com o programa |
| participantIdType | ID do programa para programas de Incentivo e Vendedor, se o programa for para programas de Loja e Microsoft Azure Marketplace |
| participantName | Nome do parceiro de ganho |
| partnerCountryCode | Localização/país/região do parceiro de ganho |
| programName | Nome do programa de incentivo/loja |
| transactionId | Um identificador exclusivo da transação |
| transactionCurrency | Moeda em que a transação original do cliente ocorreu (não é a moeda do local do parceiro) |
| transactionDate | Data da transação. Útil para programas em que muitas transações contribuem para um ganho |
| transactionExchangeRate | Taxa de câmbio usada para mostrar o valor em USD da transação correspondente |
| transactionAmount | Valor da transação na moeda da transação original, com base no qual o ganho é gerado |
| transactionAmountUSD | Valor da transação em dólares americanos (USD) |
| alavanca | Regra comercial para o ganho |
| earningRate | Taxa de incentivo aplicada ao valor da transação para gerar um ganho |
| quantidade | Quantidade cobrada para programas de transação. Varia de acordo com o programa |
| quantityType | Tipo de quantidade, por exemplo: Quantidade cobrada, usuário ativo mensal (MAU) |
| earningType | Indica se é tarifa, desconto, cooperação, venda e assim por diante |
| earningAmount | Valor do ganho na moeda da transação original |
| earningAmountUSD | Valor do ganho em USD |
| earningDate | Data do ganho |
| calculationDate | Data em que o ganho foi calculado no sistema |
| earningExchangeRate | Taxa de câmbio usada para mostrar o valor em USD correspondente |
| exchangeRateDate | Data da taxa de câmbio usada para calcular EarningAmount USD |
| paymentAmountWOTax | Valor do ganho (sem imposto) na moeda de Pagamento somente para pagamentos &quot;Enviados&quot; |
| paymentCurrency | Moeda de pagamento escolhida pelo parceiro no perfil de Pagamento. Mostrado somente para pagamentos enviados |
| paymentExchangeRate | Taxa de câmbio usada para calcular paymentAmountWOTax na moeda de pagamento usando ExchangeRateDate |
| paymentId | Identificador exclusivo do pagamento. Esse número é visível em seu extrato bancário |
| paymentStatus | Status de pagamento |
| paymentStatusDescription | Descrição do status de pagamento |
| customerId | Está sempre em branco |
| customerName | Está sempre em branco |
| partNumber | Está sempre em branco |
| productName | Nome do produto vinculado à transação |
| productId | Identificador exclusivo do produto |
| parentProductId | Identificador exclusivo do produto pai. Se não houver um produto pai na transação, ID do produto pai = ID do produto. |
| parentProductName | O nome do produto pai. Se não houver um produto pai na transação, Nome do produto pai = Nome do produto. |
| productType | Tipo de produto (como aplicativo, complemento e jogo) |
| invoiceNumber | Número da fatura (somente para Contratos Enterprise) |
| resellerId | Identificador do revendedor |
| resellerName | Nome do revendedor |
| transactionType | Tipo de transação (como compra, reembolso, inversão e estorno) |
| localProviderSeller | Provedor/vendedor local do registro |
| taxRemitted | Valor do imposto remetido (vendas, uso ou impostos sobre IVA/GST). |
| taxRemitModel | Parte responsável pelo pagamento de impostos (vendas, uso ou impostos sobre IVA/GST). |
| storeFee | O valor retido pela Microsoft como taxa pela disponibilização do aplicativo ou complemento no marketplace comercial. |
| transactionPaymentMethod | Instrumento de pagamento do cliente usado na transação (como cartão, cobrança de operadora móvel e PayPal) |
| tpan | Rede de anúncios de terceiros |
| customerCountry | País/região do cliente |
| customerCity | Cidade do cliente |
| customerState | Estado do cliente |
| customerZip | CEP/código postal do cliente |
| TenantID | A ID do locatário |
| externalReferenceId | Identificador exclusivo do programa |
| externalReferenceIdLabel | Etiqueta de identificador exclusivo |
| transactionCountryCode | Código do país/região em que a transação ocorreu |
| taxCountry | País/região do cliente |
| taxState | Estado do cliente |
| taxCity | Cidade do cliente |
| taxZipCode | CEP/código postal do cliente |
| LicensingProgramName | Nome do programa de licenciamento |
| Código do programa | Cadeia de caracteres para mapear com o nome do programa |
| earningAmountInLastPaymentCurrency | Valor do ganho na moeda do último pagamento (o campo fica vazio se não houver pagamento anterior) |
| lastPaymentCurrency | Moeda do último pagamento (o campo fica vazio se não houver pagamento anterior) |
| AssetId | O identificador exclusivo dos pedidos do cliente para o serviço do marketplace. Representa os itens de linha de compra. Pode haver vários ativos. |
| OrderId | Está relacionado à fatura de um cliente |
| LineItemId | Linha individual na fatura de um cliente |
| País/região do cliente | O nome do país/região fornecido pelo cliente. Pode ser diferente do país/região na Assinatura do Azure de um cliente. |
| EmailAddress do cliente | O endereço de email fornecido pelo cliente. Pode ser diferente do endereço de email na Assinatura do Azure de um cliente. |
| SkuId | ID do SKU definido durante a publicação. Uma oferta pode ter muitos SKUs, mas um SKU só pode estar associado a uma única oferta. |

> [!NOTE]
> Todos os relatórios e insights sobre a opção de publicação da transação podem ser encontrados na seção Análise do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte
Para obter suporte à cobrança, entre em contato com o [suporte do editor do marketplace comercial](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="next-step"></a>Próxima etapa

- [Resumos do pagamento](./payout-summary.md)
