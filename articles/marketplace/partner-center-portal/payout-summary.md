---
title: Resumo do pagamento do mercado comercial | Mercado Azure
description: O resumo do Pagamento mostra detalhes sobre o dinheiro que você ganhou com sua oferta. Ele também permite saber quando você receberá os pagamentos e quanto você será pago.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730216"
---
# <a name="payout-reporting"></a>Relatório de pagamento

O [**resumo do Pagamento**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) mostra detalhes sobre o dinheiro que você ganhou com a Microsoft. Ele também permite saber quando você receberá os pagamentos e quanto você será pago.

Se você vender ofertas no Azure Marketplace, você também verá informações sobre pagamentos bem-sucedidos no **resumo de Pagamentos**. Para obter mais informações sobre o pagamento do Azure Marketplace, consulte as [Políticas de Participação no Mercado do Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkId=722436) e o Acordo de [Editores do Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Para ser elegível para o pagamento, seus rendimentos devem atingir o limite de [pagamento](payment-thresholds-methods-timeframes.md) de US $ 50. Para obter detalhes sobre o limite de pagamento, consulte esta página e revise o [Acordo do Microsoft Azure Marketplace Publisher](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Funções e permissão para acessar o relatório de pagamento](#roles-and-permission-to-access-the-payout-report)
- [Relatório de pagamento: diferença entre portal de parceiros na nuvem e centro de parceiros](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Tipos de clientes](#customer-types)
- [Co-relação entre pagamento e uso](#corelation-between-payout-and-usage)
- [Download do histórico de transações](#transaction-history-download-export)
- [Perguntas sobre cobrança e suporte](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Funções e permissão para acessar o relatório de pagamento

| Relatórios/Páginas    | Proprietário da conta    | Gerente  | Desenvolvedor | Contribuinte empresarial |  Contribuinte financeiro | Comerciante |
|------------------|------------------|----------|-----------|----|----|-----|
| Relatório de aquisição (incluindo dados em tempo real) | Pode exibir | Pode exibir | Sem acesso | Sem acesso | Pode exibir | Sem acesso |
| Relatório/respostas de comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Sem acesso | Sem acesso | Pode exibir e enviar comentários |
| Relatório de saúde (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Relatório de uso | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil de imposto | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo do pagamento | Pode exibir | Sem acesso | Sem acesso | Sem acesso | Pode exibir | Sem acesso |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Relatório de pagamento: diferença entre portal de parceiros na nuvem e centro de parceiros

| | Portal de Parceiros de Nuvem | Partner Center |
|---------|---------|---------|
| Links | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)E[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navegação | Relatórios de pagamento fornecidos no Pagamento insights | Relatórios de pagamento fornecidos no Partner Center – Ícone de pagamento |
| Escopo | <ul> <li>Transação por item de linha é visível, para coleta em andamento, coletada e paga </li> <li>Relatórios – mostra todos os itens da linha uma vez que a ordem de compra é criada, incluindo coleta em andamento e faturamento em Andamento, e status de coleta e itens de linha que ainda não são elegíveis para serem pagos. </li> </ul> | <ul> <li>Mostra os itens da linha uma vez que eles são considerados como ganhos elegíveis.</li> <li>Os clientes pagam à Microsoft primeiro, e depois os ISVs podem ver o relatório de pagamento começando.</li> <li>O relatório de pagamento não mostrará a coleta em andamento e o faturamento em andamento.  </li> </ul>  |
| Transação não pronta para pagamento | Faturamento em Andamento | Próximo pagamento estimado: O status de pagamento está no estado não processado.  |
| Status de pagamento |  | Não processado: <br> O ganho é elegível para pagamento. Ele permanece neste estado por um período de resfriamento conforme definido no guia do programa de Incentivo. <br> <br> Próximo: <br> Ordem de pagamento gerada pendente de revisões internas antes do pagamento ser processado. <br> <br> Enviado: <br> O pagamento foi enviado ao seu banco. |

## <a name="customer-types"></a>Tipos de clientes

### <a name="enterprise-agreement"></a>Acordo empresarial

Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

### <a name="credit-cards-and-monthly-invoice"></a>Cartões de crédito e a fatura mensal

Os clientes também podem pagar usando um cartão de crédito e uma fatura mensal. Neste caso, suas taxas de licença de software serão cobradas mensalmente.

### <a name="csp-and-direct-pay-users"></a>CSP e Usuários de Pagamento Direto

Por exemplo, se o cliente comprar usando um cartão de crédito.

## <a name="corelation-between-payout-and-usage"></a>Co-relação entre pagamento e uso

|Descrição    |    Data  | Pedidos/Uso  | Pagamento |
|----------|----------|-----------|-------------|
|Período de ordem   | 15 de agosto de 2019 - 30 de agosto de 2019 | **Ordens de atributos de correlação** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Uso** <br> <ul> <li>CustomerId </li> <li>Nome do Cliente</li> <li>(Referência de uso) BuyRecordId/LineItemId</li> <li> Carga estendida estimada <br> Pagamento estimado (PC) </li> </ul> |  |
|Prazo final (mês)   | 30 de agosto de 2019 | | |
|Data de cobrança | 1 de setembro de 2019 | | |
|Data de pagamento do cliente | 1 de setembro de 2019 | | |
|Efetue a caução de período (cartões de crédito, 30 dias) | 1 de setembro de 2019 - 30 de setembro de 2019 | | **Ordens de atributos de correlação:** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li> Nome do Cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do Cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçãoValor</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status de pagamento:** Não processado |
|Início do período de coleta | 1 de setembro de 2019 | | |
|Término do período de coleta (máximo, 30 dias) | 30 de setembro de 2019 | | |
|Data de cálculo de pagamento (mensalmente no dia 15) | 1 de outubro de 2019 | | **Atributos de correlação** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li>Nome do Cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do Cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçãoValor</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status de pagamento:** Próximo |
|Data de pagamento | 15 de outubro de 2019 | | **Atributos de correlação** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li> Nome do Cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do Cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçãoValor</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status de pagamento:** Pagamento enviado |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contrato empresarial (clientes trimestrais/mensais)

| Descrição |    Data  | Uso | Pagamento |
|----------|----------|---------|-----------|
|Período de ordem | 15 de agosto de 2019 - 30 de agosto de 2019 | **Ordens de atributos de correlação** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Relatório de uso** <br> <ul> <li>CustomerId </li> <li>Nome do Cliente</li> <li>(Referência de uso) BuyRecordId/LineItemId</li> <li> Carga estendida estimada <br> Pagamento estimado (PC) </li> </ul> | |
|Prazo final (trimestre) | 30 de setembro de 2019 | | |
|Data de cobrança | 15 de outubro de 2019 | | |
|Efetue a caução de período (cartões de crédito, 30 dias) | n/d | | |
|Início do período de coleta | 15 de outubro de 2019 | | |
|Apenas cartões de crédito, 30 dias | Nov 1, 2019 - Nov 30, 2019 | | |
|Término do período de coleta (máximo, 90 dias) | jan 15, 2020 | | |
|Data de pagamento do cliente | 30 de dez de 2019 | | |
|Cálculo de Pagamento | jan 15, 2020 | | |
|Data de pagamento | 15 de fevereiro de 2020 | | **Para clientes baseados trimestralmente** <br> <br> **Relatório de ordens** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li> Nome do Cliente</li> </ul> <br> **Uso** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do Cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçãoValor</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status do pagamento:** enviado |

## <a name="transaction-history-download-export"></a>Histórico de transações download exportação

Esta opção fornece um download de cada item da linha de ganho que você vê na página do histórico de transações, tipo de ganho, data, valor da transação associada, cliente, produto e outros detalhes transacionais aplicáveis ao programa Incentivos.

| Nome da coluna     | Descrição    |
|-------------|-------------------------------|
| earningId                      | Identificador exclusivo para cada ganho                                                                                                       |
| participanteId                  | A identidade primária do parceiro que ganha no âmbito do programa                                                                            |
| participanteIdType              | ID de programa sustais e IF vendedor para programas de loja e Mercado Azure                                          |
| participanteNome                | Nome do parceiro que ganha                                                                                                              |
| partnerCountryCode             | Localização/país do parceiro que ganha                                                                                                  |
| programName                    | Nome do programa de incentivo/loja                                                                                                             |
| transactionId                  | Identificador exclusivo para a transação                                                                                                    |
| transaçãoMoeda            | Moeda na qual ocorreu a transação original do cliente (não é a moeda de localização do parceiro)                                     |
| Transactiondate                | Data da transação. Útil para programas onde muitas transações contribuem para um ganho                                           |
| transaçãoTaxa de negociação        | Taxa de câmbio usada para mostrar o valor correspondente da transação USD                                                                 |
| transaçãoValor              | Valor da transação na moeda de transação original com base no qual o lucro é gerado                                              |
| transaçãoAmountUSD           | Valor da transação em USD                                                                                                                |
| Alavanca                          | Indica regra de negócio para o ganho                                                                                                  |
| taxa de ganhos                    | Taxa de incentivo aplicada sobre o valor da transação para gerar um lucro                                                                      |
| quantidade                       | Varia de acordo com o programa. Indica quantidade faturada para programas transacionais                                                            |
| quantidadeTipo                   | Indica tipo de quantidade, por exemplo: Quantidade faturada, MAU                                                                                     |
| earningType                    | Indica se é taxa, desconto, coop, vender etc.                                                                                          |
| earningAmount                  | Valor de Ganho na moeda de transação original                                                                                      |
| earningAmountUSD               | Valor de ganho em USD                                                                                                                    |
| earningDate                    | Data do ganho                                                                                                                      |
| cálculoData                | Data em que o ganho foi calculado no sistema                                                                                            |
| earningExchangeRate            | Taxa de câmbio usada para mostrar o valor correspondente de USD                                                                                  |
| exchangeRateDate               | Data da taxa de câmbio usada para calcular o Lucro do Valor USD                                                                                   |
| pagamentoAmountWOTax             | Valor de ganho (sem imposto) em Pagar para moeda para pagamentos "Enviados" apenas                                                                 |
| paymentCurrency                | Pague à moeda escolhida pelo parceiro no perfil Pagamento. Mostrado apenas para pagamentos enviados                                                   |
| pagamentoTaxa de câmbio            | Taxa de câmbio usada para calcular o pagamentoAmountWOTax em moeda de pagamento usando ExchangeRateDate                                            |
| pagamentoId            | Identificador exclusivo para o pagamento. Este número é visível em seu extrato bancário                                            |
| pagamentoStatus            | Status do pagamento                                            |
| pagamentoStatusDescrição            | Descrição amigável do status de pagamento                                            |
| customerId                     | Sempre estará em branco                                                                                                                     |
| customerName                   | Sempre estará em branco                                                                                                                     |
| partNumber                     | Sempre estará em branco                                                                                                                     |
| productName                    | Nome do produto vinculado à transação                                                                                                       |
| productId                      | Identificador exclusivo do produto                                                                                                                |
| parentProductId                | Identificador exclusivo do produto pai. Nota: se não houver um produto pai para a transação, então o ID do produto pai = ID do produto. |
| nome do produto pai              | Nome do produto pai. Nota: se não houver um produto pai para a transação, então o nome do produto-mãe = nome do produto.   |
| productType                    | Tipo de produto (por exemplo, app, complemento, jogo, etc.)                                                                                        |
| nota fiscalNúmero                  | Número da fatura (aplicável apenas para EA)                                                                                                  |
| revendedorId                     | Identificador de revendedor                                                                                                                      |
| revendedorNome                   | Nome do revendedor                                                                                                                            |
| Transactiontype                | Tipo de transação (por exemplo, compra, reembolso, estorno, etc.)                                                               |
| localProviderSeller            | Provedor/vendedor local de registro                                                                                                          |
| taxRemitted                    | Valor do imposto remetido (vendas, uso ou impostos IVA/GST).                                                                                   |
| taxRemitModel                  | Parte responsável por remeter impostos (vendas, uso ou impostos IVA/GST).                                                                    |
| storeFee                       | O valor retido pela Microsoft como taxa para tornar o aplicativo ou complemento disponível na Loja.                                            |
| transaçãoMétodo de pagamento       | A forma de pagamento do cliente usada na transação (por exemplo, cartão de crédito, conta de celular, PayPal, etc)                                |
| tpan                           | Indica a rede de anúncios de terceiros                                                                                                     |
| clientePaís                | País do cliente                                                                                                                         |
| clienteCidade                   | Cidade do cliente                                                                                                                            |
| clienteEstado                  | Estado do cliente                                                                                                                           |
| customerZip                    | Cep/cep do cliente                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Identificador exclusivo para o programa                                                                                                        |
| externalReferenceIdLabel       | Rótulo identificador exclusivo                                                                                                                  |
| transaçãoCountryCode       | Código do País em que a transação aconteceu                                                                                                                  |
| taxCountry       | Vendido para o país do cliente                                                                                                                  |
| taxState       | Vendido para o estado do cliente                                                                                                                  |
| taxCity       | Vendido para a Cidade do Cliente                                                                                                                  |
| taxZipCode       | Vendido para o cliente Zip                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Código do Programa       | String para mapear com o nome do programa                                                                                                                   |
| earningAmountInLastPaymentCurrency       | O valor do ganho na última moeda de pagamento (campo estará vazio, se nenhum pagamento prévio tiver sido pago)                                                                                                                   |
| lastPaymentCurrency       | Última moeda de pagamento (campo estará vazio, se nenhum pagamento prévio tiver sido pago)                                                                                                                   |
| AssetId       | O identificador exclusivo para os pedidos do cliente para o seu serviço de marketplace.  Representa os itens da linha de compra transacionada. Pode haver vários ativos.                                                                                                                   |
| OrderId       | diz respeito à fatura de um cliente                                                                                                                   |
| LineItemId       | linha individual na fatura de um cliente                                                                                                                   |
| País/Região do cliente       | O nome do país fornecido pelo cliente.  Isso pode ser diferente do país na Assinatura Azure de um cliente.                                                                                                                   |
| Endereço de e-mail do cliente       | O endereço de e-mail fornecido pelo cliente final.  Isso pode ser diferente do endereço de e-mail da Assinatura Azure de um cliente.                                                                                                                   |
| SkuId       | ID SKU conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode estar associada a uma única oferta.                                                                                                                   |

>[!Note]
>Todos os relatórios e insights para a opção de publicação de transações estão disponíveis através da seção Insights do Portal de Parceiros em Nuvem ou da seção Analytics do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Para obter ajuda em questões de faturamento, entre em contato com [o suporte de editores de marketplace comercial](https://partner.microsoft.com/support/v2/?stage=1).
