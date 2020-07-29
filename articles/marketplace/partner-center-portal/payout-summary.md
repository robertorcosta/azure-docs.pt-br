---
title: Relatórios de pagamento – marketplace comercial da Microsoft
description: Os relatórios de pagamento mostram detalhes sobre o dinheiro que você ganhou com sua oferta, incluindo a quantidade de pagamento e quando você será pago.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.date: 04/24/2020
ms.openlocfilehash: dc690e29129f5be68456e6a9dc075ba72f11b121
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318225"
---
# <a name="payout-summaries"></a>Resumos do pagamento

O resumo do pagamento mostra detalhes sobre o dinheiro que você ganhou com a Microsoft. Ele também permite que você saiba quando receberá os pagamentos e quanto será pago.

Se você vender produtos no Azure Marketplace, também verá as informações sobre pagamentos bem-sucedidos no resumo do pagamento. Para obter detalhes, confira [Políticas de participação do Microsoft Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e [Contrato do editor do Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Para se qualificar para o pagamento, suas receitas devem alcançar o [limite de pagamento](payment-thresholds-methods-timeframes.md) de US$ 50. Para obter detalhes sobre o limite de pagamento, confira esta página e examine o Contrato de Desenvolvedor de Aplicativos.

> [!NOTE]
> Se você estiver procurando suporte em relação a pagamentos, incluindo a configuração de contas de pagamento, pagamentos ausentes, colocação de pagamentos em espera ou qualquer outra coisa, entre em contato com o suporte [aqui](https://developer.microsoft.com/windows/support).

## <a name="access-the-payout-summary-pages"></a>Acessar as páginas de resumo do pagamento

Para abrir uma das páginas de resumo do pagamento:

1. Selecione o ícone Pagamento no canto superior direito.
2. Selecione Histórico de transações, Pagamentos ou Exportar dados.

## <a name="transaction-history-page"></a>Página Histórico de transação

Esta página exibe todos os seus ganhos individuais, incluindo a data, o tipo e os ganhos de cada um. Você pode selecionar um período de tempo a ser exibido e também pode filtrar por ID de registro, Programa, ID de pagamento, Tipo de ganho, Alavanca e Status. Os dados estão disponíveis para o ano fiscal atual (1º de julho a 30 de junho) e os dois anos fiscais anteriores.

Para ver mais detalhes sobre um ganho, selecione a seta para baixo no lado direito da página. Isso exibirá a alavanca, o valor da receita e o produto. Se um desses dados estiver não disponível e você precisar acessá-lo, entre em contato com o [suporte](https://developer.microsoft.com/windows/support). Se o ganho for o resultado de um ajuste e não de uma transação, os campos de produto não serão exibidos.

Para exportar um dos dados de transação nessa página, use a página **Exportar dados**.

## <a name="payments-page"></a>Página Pagamentos

Os totais nessa página representam todos os programas que você participa. Você pode filtrar por ID de participante, Programa, ID de pagamento e Tipo de ganho. Os valores são fornecidos em dólares americanos. O valor pago também é exibido na moeda de pagamento.

| Área                   | Descrição                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| Total pago neste ano   | O total combinado pago a você neste ano, em dólares americanos, para todos os seus programas       |
| Próximo pagamento estimado | Seu próximo pagamento (mesmo se houver outros em breve), em dólares americanos |
| Último pagamento           | O valor (em dólares americanos), o nome do programa e o programa do seu pagamento mais recente           |
| Pagamentos por origem     | Quantidade de pagamentos (em dólares americanos) representados pelo programa nos últimos 12 meses           |
| Pagamentos               | Selecione **Pago** ou **Pendente** e, em seguida, classifique conforme desejado. Para obter detalhes adicionais de um pagamento específico, selecione **Exibir**. Para baixar uma cópia do demonstrativo de remessa de pagamento, selecione **Baixar**. Como os dados do histórico de transações podem levar até 24 horas para serem exibidos, talvez você não veja os ganhos associados imediatamente. |
|||

Para exportar um dos dados nessa página, selecione **Exportar** e siga as instruções na página Exportar dados.

## <a name="transaction-history-page"></a>Página Histórico de transação

Esta página exibe todos os seus ganhos individuais, incluindo a data, o tipo e os ganhos de cada um. Você pode selecionar um período de tempo a ser exibido e pode filtrar por ID de registro, Programa, ID de pagamento, Tipo de ganho, Alavanca e Status. Os dados estão disponíveis para o ano fiscal atual (1º de julho a 30 de junho) e os dois anos fiscais anteriores.

Para ver mais detalhes sobre um ganho, selecione a seta para baixo no lado direito da página. Isso exibirá a alavanca, o valor da receita e o produto. Se um desses dados estiver não disponível e você precisar acessá-lo, entre em contato com o [suporte](https://developer.microsoft.com/windows/support). Se o ganho for o resultado de um ajuste e não de uma transação, os campos de produto não serão exibidos.

Para exportar um dos dados da transação nessa página, selecione **Exportar** e siga as instruções na página Exportar dados. Os arquivos exportados da página Histórico de transação mostram os dados na moeda da transação, os ganhos na moeda da transação e em dólares americanos e o valor pago na moeda de pagamento.

## <a name="payment-status"></a>Status de pagamento

| Status do ganho           | Motivo                                                                                                                                      | É necessária uma ação do parceiro?                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Não processado              | O ganho está qualificado para pagamento. Ele permanece nesse estado por um período de carência, conforme definido no guia do programa de incentivo. | Não                                                         |
| Futuros                 | A ordem de pagamento gerou revisões internas pendentes antes de o pagamento ser processado                                                               | Não                                                         |
| Fatura de imposto pendente      | Sua fatura de imposto está incompleta ou é inválida                                                                                                  | Atualize sua fatura de imposto para ser pago |
| Rejeitado durante a revisão   | O pagamento foi rejeitado durante a revisão                                                                                                     | Entre em contato com o [Suporte da Microsoft](https://developer.microsoft.com/windows/support) para detalhes                      |
| Falhou                   | O pagamento falhou devido a um erro do sistema da Microsoft                                                                                         | Entre em contato com o [Suporte da Microsoft](https://developer.microsoft.com/windows/support) para detalhes                      |
| Em andamento              | O pagamento está em andamento                                                                                                                 | Não                                                         |
| Pagamento incorreto        | O ressarcimento do pagamento está em andamento                                                                                                       | Não                                                         |
| Enviado                     | O pagamento foi enviado ao seu banco                                                                                                     | Não                                                         |
| Em reprocessamento             | Houve um erro do sistema da Microsoft no pagamento e ele está sendo reprocessado                                                                  | Não                                                         |
| Reversed                 | O pagamento foi revertido pelo seu banco e será enviado novamente no próximo ciclo de pagamento                                                     | Não                                                         |
| Fatura de imposto rejeitada     | Sua fatura de imposto foi rejeitada durante a análise. Todos os pagamentos pendentes estarão em espera até que a análise da fatura de imposto seja concluída.                 | Entre em contato com o [Suporte da Microsoft](https://developer.microsoft.com/windows/support) para detalhes                      |
| Fatura de imposto em análise | Sua fatura de imposto está sendo examinada. Seu pagamento será liberado depois que a fatura de imposto tiver sido aprovada.                                   | Não                                                         |
| Rejeitado                 | O pagamento foi rejeitado pelo banco                                                                                                      | Entre em contato com seu banco para obter detalhes.                             |
|||

## <a name="export-data-page"></a>Exportar página de dados

Siga estas instruções para exportar os dados.

A página Exportar dados não é atualizada por conta própria. Talvez seja necessário atualizar a página manualmente para ver os dados mais recentes.

O filtro pode resultar em um erro **Não há dados disponíveis**. Isso provavelmente significa que você saiu do período de tempo padrão selecionado em três meses e, em seguida, selecionou uma ID de pagamento de um ganho que está fora desse período. Expanda seu período de tempo e tente novamente.

## <a name="payments"></a>Pagamentos

![Exportar pagamentos](./media/pc-export-payments.png)

Essa opção fornece um download dos pagamentos recebidos em seu banco para um determinado programa, o imposto associado e a quantidade de ganho agregada. Esse relatório é usado para vários programas do Partner Center, portanto, algumas colunas podem ser inaplicáveis ao seu relatório. Essas colunas são marcadas abaixo.

| Nome da coluna              | Descrição                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| participantID            | A identidade principal do parceiro que ganha com o programa                                                                             |
| participantIDType        | Normalmente a ID do programa para programas de incentivo e ID do vendedor para programas da loja                                                                |
| participantName          | Nome do parceiro de ganho                                                                                                               |
| programName              | Nome do programa de incentivo/loja                                                                                                              |
| ganho                   | Valor ganho na moeda de pagamento para o programa/participantID                                                                       |
| earnedUSD                | Valor ganho para a ID do programa/participante, em US$                                                                                      |
| withheldTax              | Valor de imposto retido na moeda de pagamento para o programa/participantID                                                               |
| salesTax                 | Valor total do imposto sobre as vendas na moeda de pagamento para o programa/participantID (aplicável somente a programas de incentivo)                   |
| serviceFeeTax            | Valor total de serviceFeeTax na moeda de pagamento para o programa/participantID (aplicável somente a programas da loja e do Azure Marketplace) |
| totalPayment             | Pagamento total na moeda local, excluindo a retenção de imposto e incluindo o imposto sobre as vendas (se aplicável) para o programa/participantID   |
| currencyCode             | Código da moeda de pagamento                                                                                                                      |
| paymentMethod            | O método usado para pagar o parceiro, por exemplo, transferência bancária eletrônica, nota de crédito                                                     |
| paymentID                | Identificador exclusivo do pagamento. Esse número geralmente é visível em seu extrato bancário (aplicável somente a pagamentos SAP).              |
| paymentStatus            | Status de pagamento                                                                                                                            |
| paymentStatusDescription | Descrição amigável do status de pagamento                                                                                                    |
| paymentDate              | A data de pagamento foi enviada da Microsoft                                                                                                      |
|||

## <a name="transaction-history"></a>Histórico de transação

![Exportar histórico de transação](./media/pc-export-transaction.png)

Essa opção fornece um download de cada item de linha de ganho que você vê na página Histórico de transação, tipo de conquista, data, valor da transação associada, cliente, produto e outros detalhes transacionais aplicáveis aos seus programas.

| Nome da coluna                    | Descrição                                                                                                                              | Aplicabilidade para Incentivos/Loja/Azure Marketplace           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | Identificador exclusivo de cada ganho                                                                                                       | Todos                                                            |
| participantId                  | A identidade principal do parceiro que ganha com o programa                                                                            | Todos                                                            |
| participantIdType              | ID do programa para programas de incentivo e vendedor SE o programa for para programas da loja e do Microsoft Azure Marketplace                                          | Todos                                                            |
| participantName                | Nome do parceiro de ganho                                                                                                              | Todos                                                            |
| partnerCountryCode             | Localização/país/região do parceiro de ganho                                                                                                  | Todos                                                            |
| programName                    | Nome do programa de incentivo/loja                                                                                                             | Todos                                                            |
| transactionId                  | Um identificador exclusivo da transação                                                                                                    | Todos                                                            |
| transactionCurrency            | Moeda em que a transação original do cliente ocorreu (não é a moeda da localização do parceiro)                                     | Todos                                                            |
| transactionDate                | Data da transação. Útil para programas em que muitas transações contribuem para um ganho                                           | Todos                                                            |
| transactionExchangeRate        | Data da taxa de câmbio usada para mostrar o valor em US$ da transação correspondente                                                                 | Todos                                                            |
| transactionAmount              | Valor da transação na moeda da transação original, com base em qual o ganho é gerado                                              | Todos                                                            |
| transactionAmountUSD           | Valor da transação em US$                                                                                                                | Todos                                                            |
| alavanca                          | Indica a regra de negócios para o ganho                                                                                                  | Todos                                                            |
| earningRate                    | Taxa de incentivo aplicada ao valor da transação para gerar um ganho                                                                      | Todos                                                            |
| quantidade                       | Varia de acordo com o programa. Indica a quantidade cobrada para programas de transação                                                            | Todos                                                            |
| quantityType                   | Indica o tipo de quantidade, por exemplo, quantidade cobrada, MAU                                                                             | Todos                                                            |
| earningType                    | Indica se é valor, desconto, cooperação, venda e assim por diante                                                                                          | Todos                                                            |
| earningAmount                  | Valor do ganho na moeda da transação original                                                                                      | Todos                                                            |
| earningAmountUSD               | Valor do ganho em US$                                                                                                                    | Todos                                                            |
| earningDate                    | Data do ganho                                                                                                                      | Todos                                                            |
| calculationDate                | Data em que o ganho foi calculado no sistema                                                                                            | Todos                                                            |
| earningExchangeRate            | Taxa de câmbio usada para mostrar o valor em US$ correspondente                                                                                  | Todos                                                            |
| exchangeRateDate               | Data da taxa de câmbio usada para calcular EarningAmount US$                                                                                   | Todos                                                            |
| paymentAmountWOTax             | Valor do ganho (sem imposto) na moeda de pagamento somente para pagamentos "Enviados"                                                                 | Todos                                                            |
| paymentCurrency                | Moeda de pagamento escolhida pelo parceiro no perfil de Pagamento. Mostrado somente para pagamentos enviados                                                   | Todos                                                            |
| paymentExchangeRate            | Taxa de câmbio usada para calcular paymentAmountWOTax na moeda de pagamento usando ExchangeRateDate                                            | Todos                                                            |
| claimId                        | Um identificador exclusivo para a declaração                                                                                                              | Incentivos – apenas alguns programas                                |
| planId                         | Identificador exclusivo do plano                                                                                                               | Incentivos – apenas alguns programas                                |
| paymentId                      | Identificador exclusivo do pagamento. Esse número é normalmente visível em seu extrato bancário                                                 | Somente pagamentos SAP                                              |
| paymentStatus                  | Status de pagamento                                                                                                                           | Todos                                                            |
| paymentStatusDescription       | Descrição amigável do status de pagamento                                                                                                   | Todos                                                            |
| customerId                     | Está sempre em branco                                                                                                                     | Somente programas de incentivo (exceção: OEM) e Azure Marketplace |
| customerName                   | Está sempre em branco                                                                                                                     | Somente programas de incentivo (exceção: OEM) e Azure Marketplace |
| partNumber                     | Está sempre em branco                                                                                                                     | Alguns programas de incentivo e de loja e o Azure Marketplace        |
| productName                    | Nome do produto vinculado à transação                                                                                                       | Todos                                                            |
| productId                      | Identificador exclusivo do produto                                                                                                                | Loja e Azure Marketplace                                    |
| parentProductId                | Identificador exclusivo do produto pai. Se não houver um produto pai na transação, ID do produto pai = ID do produto. | Loja e Azure Marketplace                                    |
| parentProductName              | O nome do produto pai. Se não houver um produto pai na transação, Nome do produto pai = Nome do produto.   | Loja e Azure Marketplace                                    |
| productType                    | Tipo de produto, como aplicativo, complemento ou jogo                                                                                        | Loja e Azure Marketplace                                    |
| invoiceNumber                  | Número da fatura (aplicável apenas ao EA)                                                                                                  | Incentivo e Azure Marketplace – apenas alguns programas           |
| subscriptionId                 | Identificador de assinatura associado ao cliente                                                                                         | Incentivo – apenas alguns programas                                 |
| subscriptionStartDate          | Data de início da assinatura                                                                                                                  | Incentivo – apenas alguns programas                                 |
| subscriptionEndDate            | Data de término da assinatura                                                                                                                    | Incentivo – apenas alguns programas                                 |
| resellerId                     | Identificador do revendedor                                                                                                                      | Incentivo – apenas alguns programas                                 |
| resellerName                   | Nome do revendedor                                                                                                                            | Incentivo – apenas alguns programas                                 |
| distributorId                  | Identificador do distribuidor                                                                                                                   | Incentivo – apenas alguns programas                                 |
| distributorName                | Nome do distribuidor                                                                                                                         | Incentivo – apenas alguns programas                                 |
| agreementNumber                | Número do contrato                                                                                                                         | Incentivo – apenas alguns programas                                 |
| agreementStartDate             | Data de início do contrato                                                                                                                     | Incentivo – apenas alguns programas                                 |
| agreementEndDate               | Data de término do contrato                                                                                                                       | Incentivo – apenas alguns programas                                 |
| workload                       | Carga de trabalho                                                                                                                                 | Incentivo – apenas alguns programas                                 |
| transactionType                | Tipo de transação, como compra, reembolso, inversão ou estorno                                                               | Loja e Azure Marketplace                                    |
| localProviderSeller            | Provedor/vendedor local do registro                                                                                                          | Somente na loja                                                     |
| taxRemitted                    | Valor do imposto remetido (vendas, uso ou impostos sobre IVA/GST)                                                                                   | Loja e Azure Marketplace                                    |
| taxRemitModel                  | Parte responsável pelo pagamento de impostos (vendas, uso ou impostos sobre IVA/GST)                                                                    | Somente na loja                                                     |
| storeFee                       | O valor retido pela Microsoft como taxa pela disponibilização do aplicativo ou complemento na loja                                           | Somente na loja                                                     |
| transactionPaymentMethod       | Meio de pagamento do cliente usado na transação, como cartão, cobrança de operadora móvel ou PayPal                                | Loja e Azure Marketplace                                    |
| tpan                           | Indica a rede de anúncios de terceiros                                                                                                     | Loja – somente anúncios                                               |
| customerCountry                | País/região do cliente                                                                                                                         | Loja e Azure Marketplace                                    |
| customerCity                   | Cidade do cliente                                                                                                                            | Loja e Azure Marketplace                                    |
| customerState                  | Estado do cliente                                                                                                                           | Loja e Azure Marketplace                                    |
| customerZip                    | CEP/código postal do cliente                                                                                                                 | Loja e Azure Marketplace                                    |
| purchaseTypeCode               | Está sempre em branco                                                                                                                     | Programa de incentivo – CRI                                        |
| purchaseOrderType              | Está sempre em branco                                                                                                                     | Programa de incentivo – CRI                                        |
| purchaseOrderCoverageStartDate | Está sempre em branco                                                                                                                     | Programa de incentivo – CRI                                        |
| purchaseOrderCoverageEndDate   | Está sempre em branco                                                                                                                     | Programa de incentivo – CRI                                        |
| programOfferingLevel           |                                                                                                                                          | Programa de incentivo – CRI                                        |
| TenantID                       |                                                                                                                                          | Programas de incentivo                                             |
| externalReferenceId            | Identificador exclusivo do programa                                                                                                        | Programas de pagamento direto (incentivo e loja)                      |
| externalReferenceIdLabel       | Rótulo de identificador exclusivo                                                                                                                  | Programas de pagamento direto (incentivo e loja)                      |
|||

## <a name="historical-statements"></a>Demonstrativo do histórico

![Exportar demonstrativo do histórico](./media/pc-export-statements.png)

O histórico de transações de antes de 1º de julho de 2019 é tratado separadamente. Os demonstrativos usarão os campos a seguir em vez dos atuais.

> [!NOTE]
> O histórico de transações herdadas tem uma coluna chamada "Reservado" que corresponde à coluna "Ganhos" no histórico moderno, exceto pelo fato de que ela exclui todos os ganhos com status = "Pagamento enviado".

> [!NOTE]
> Filtros como 3M, 6M ou 12M não serão aplicados à seção **Demonstrativos do histórico**.

| Nome do campo              | Descrição                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Origem da receita          | A origem de sua receita com base no local no qual a transação ocorreu, como Microsoft Store, Windows Phone Store, Windows Store 8 ou publicidade                  |
| ID do pedido                | Identificador exclusivo do pedido. Essa ID permite que você identifique as transações de compra com suas respectivas transações de não compra, como reembolsos ou estornos. Ambas terão a mesma ID de pedido. Além disso, no caso de uma cobrança dividida em que vários métodos de pagamento para uma compra, ele permite vincular as transações de compra. |
| ID da transação          | Identificador exclusivo da transação.                                                                                                                                          |
| Data e hora da transação   | A data e hora em que a transação ocorreu (UTC).                                                                                                                       |
| ID do produto pai       | Identificador exclusivo do produto pai. Se não houver um produto pai na transação, ID do produto pai = ID do produto.                                |
| Produto ID              | Identificador exclusivo do produto.                                                                                                                                              |
| Nome do produto pai     | O nome do produto pai. Se não houver um produto pai na transação, Nome do produto pai = Nome do produto.                                  |
| Nome do Produto            | O nome do produto                                                                                                                                                    |
| Tipo de produto            | Tipo de produto, como aplicativo, complemento ou jogo                                                                                                                       |
| Quantidade                | Quando a origem da receita é Microsoft Store para Empresas, a quantidade representa o número de licenças adquiridas. Para todas as outras origens de receita, a quantidade será sempre 1. Mesmo quando uma transação é dividida em dois itens de linha porque dois métodos de pagamento diferentes foram usados, cada item de linha mostrará uma quantidade de 1. |
| Tipo de transação        | Tipo de transação, como compra, reembolso, inversão ou estorno                                                                                              |
| Forma de pagamento          | Meio de pagamento do cliente usado na transação, como cartão, cobrança de operadora móvel ou PayPal                                                               |
| País/região        | País/região em que a transação ocorreu                                                                                                                          |
| Provedor/vendedor local | Provedor/vendedor local do registro                                                                                                                                        |
| Moeda da transação    | Moeda da transação                                                                                                                                            |
| Valor da transação      | Valor da transação                                                                                                                                              |
| Imposto remetido            | Valor do imposto remetido (vendas, uso ou impostos sobre IVA/GST)                                                                                                                  |
| Recebimentos líquidos            | Valor da transação menos o imposto remetido                                                                                                                                   |
| Valor de armazenamento               | O percentual dos recebimentos líquidos retidos pela Microsoft como valor pela disponibilização do aplicativo ou complemento na loja                                                      |
| Lucros do aplicativo            | Recebimentos líquidos menos o valor de armazenamento                                                                                                                                       |
| Impostos retidos          | Valor do imposto de renda retido (ou seja, incluído no arquivo CSV **Reservado**)                                                                                                |
| Pagamento                 | Os lucros do aplicativo menos qualquer retenção de imposto sobre renda aplicável (valor mostrado na moeda da transação). Não incluído no arquivo CSV **Reservado**.                               |
| Taxa de FX                 | Taxa de câmbio estrangeira usada para converter a moeda da transação na moeda de pagamento                                                                                         |
| Moeda de pagamento        | Moeda na qual seu pagamento é feito                                                                                                                                       |
| Pagamento convertido       | Valor de pagamento convertido na moeda de pagamento usando a taxa de FX                                                                                                         |
| Modelo de pagamento de imposto         | Parte responsável pelo pagamento de impostos (vendas, uso ou impostos sobre IVA/GST)                                                                                                   |
| Data e hora da qualificação   | A data e a hora em que os lucros da transação se tornam qualificados para pagamento (UTC). Quando um pagamento é criado, ele inclui os lucros da transação com uma data e hora de qualificação antes da data de criação do pagamento (incluída apenas no arquivo CSV **Reservado**). |
| Charges                 | Mostra uma divisão de todo o detalhamento de preço agregado na coluna Valor da transação (incluído apenas para o Azure Marketplace; não incluído no arquivo CSV **Reservado**). |
|||

## <a name="next-step"></a>Próxima etapa

- [Detalhes da política de pagamento](./payout-policy-details.md)
