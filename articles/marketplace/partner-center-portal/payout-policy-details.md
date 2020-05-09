---
title: Detalhes da política de pagamento-Azure Marketplace
description: Detalhes sobre as políticas de pagamento, incluindo agendas e revitória.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6aa40d0914237a28c7bbd32b15bf0d8d02140192
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783546"
---
# <a name="payout-policy-details"></a>Detalhes da política de pagamento

Este artigo discute o processo de pagamento da Microsoft, o agendamento de pagamento, onde encontrar o status de um pagamento e a política de revitória.

## <a name="where-to-find-upcoming-payouts"></a>Onde encontrar pagamentos futuros

No Partner Center, selecione **pagamento** na parte superior direita do portal:

![Ilustra o ícone de pagamento no canto superior direito do portal do Partner Center.](./media/payout-overview.png)

> [!TIP]
> Nem todas as funções de conta têm acesso às informações de pagamento. Para obter detalhes, consulte [funções e permissões para acessar o relatório de pagamento](./payout-summary.md).

## <a name="payment-schedules"></a>Planos de pagamento

As seções a seguir descrevem nosso processo de pagamentos.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Enterprise Agreement transações após 1º de maio de 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Atualizar para nosso modelo de pagamento de Publicador do Marketplace comercial

A partir de 1º de maio de 2020, estamos atualizando nossa política de pagamento relacionada aos produtos adquiridos no Azure Marketplace ou AppSource por clientes com um Enterprise Agreement da Microsoft. Quando um cliente adquire um produto do Azure Marketplace ou AppSource usando seus Enterprise Agreement da Microsoft existentes para transações após 1º de maio de 2020, começaremos a emitir pagamentos no próximo ciclo de pagamento de 30 dias após a fatura do cliente. As transações em que um cliente usa um cartão de crédito não são alteradas e continuarão a ter um período de manutenção de 30 dias antes do pagamento. Esta tabela mostra detalhes sobre o agendamento de pagamento.

> [!NOTE]
> Consulte [processar para não pagamento de cliente](#process-for-customer-non-payment) abaixo para as ações que executamos se o cliente não conseguir pagar, mas já tiver emitido um pagamento para você.

| Evento  | Data  | Visibilidade do parceiro: relatório de pagamento do Partner Center  |  Visibilidade do parceiro: análise do Partner Center\* |
| --- | --- | --- | --- |
| Transação ou mês de uso | 8/1/2020 – 8/31/2020 | N/D | **Relatório de uso**: novo consumo mostrado (atualizado a cada quatro horas)<br>**Relatório do pedido**: N/A |
| Final do termo (mês) | 8/31/2020 | N/D | **Relatório de uso**: consumo final do mês mostrado<br>**Relatório do pedido**: N/A |
| Ordem gerada | 9/3/2020 – 9/7/2020 | N/D | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Calcular pagamento ganhando | 9/4/2020 – 9/10/2020 | Marcado como não **processado** no histórico de transações no painel do pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Pagamento mensal | 10/5/2020 | Marcado como **futuro** no histórico de transações no painel de pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Data do pagamento | 10/15/2020 | Marcado como **enviado** no histórico de transações e na seção de pagamentos do painel de pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Fatura de cliente coletada | 12/1/2020 | Marcado como **enviado** no histórico de transações e na seção de pagamentos do painel de pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos  |
|  |  |  |  |

\*Os relatórios de uso e de pedidos podem ser acessados na seção analisar no Partner Center.

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Clientes que pagam usando cartão de crédito ou fatura

Todas as compras com um cartão de crédito ou uma nota fiscal mensal têm um período de manutenção de 30 dias para garantir que os fundos sejam apagados e não haja estorno ou fraude suspeita.

| Evento  | Data  | Visibilidade do parceiro: relatório de pagamento do Partner Center  |  Visibilidade do parceiro: análise do Partner Center\*  |
| --- | --- | --- | --- |
| Transação ou mês de uso | 8/1/2019 - 8/31/2019 | N/D | **Relatório de uso**: novo consumo mostrado (atualizado a cada quatro horas)<br>**Relatório do pedido**: N/A |
| Final do termo (mês) | 8/31/2019 | N/D | **Relatório de uso**: consumo final do mês mostrado<br>**Relatório do pedido**: N/A |
| Ordem gerada | 9/3/2019 – 9/7/2019 | N/D | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Fatura de cliente coletada | 9/7/2019 – 9/10/2019 | N/D | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Calcular pagamento | 9/8/2019 -9/12/2019 | Marcado como não **processado** no histórico de transações no painel do pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Pagamento mensal | 5/11/2019\* | Marcado como **futuro** no histórico de transações no painel de pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Data do pagamento | 15/11/2019 | Marcado como **enviado** no histórico de transações e na seção de pagamentos no painel de pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
|  |  |  |  |

\*Os relatórios de uso e de pedidos podem ser acessados na seção analisar no Partner Center.

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>Enterprise Agreement transações antes de 1º de maio de 2020

Todas as compras que ocorrem antes dessa data são processadas e pagas de acordo com a agenda abaixo após a Microsoft ter coletado o pagamento dos clientes e processado a taxa do Marketplace.

| Evento  | Data  | Visibilidade do parceiro: relatório de pagamento do Partner Center  |  Visibilidade do parceiro: análise do Partner Center\*  |
| --- | --- | --- | --- |
| Transação ou mês de uso | 8/1/2019 – 8/31/2019 | N/D | **Relatório de uso**: novo consumo mostrado (atualizado a cada quatro horas)<br>**Relatório do pedido**: N/A |
| Final do termo (mês) | 8/31/2019 | N/D | **Relatório de uso**: consumo final do mês mostrado<br>**Relatório do pedido**: N/A |
| Ordem gerada | 9/3/2019 – 9/7/2019 | N/D | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Fatura de cliente coletada | 12/1/2019 | N/D | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Calcular pagamento | 12/5/2019 – 12/7/2019 | Marcado como não **processado** no histórico de transações no painel do pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Pagamento mensal | 1/5/2019 | Marcado como **futuro** no histórico de transações no painel de pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
| Data do pagamento | 1/15/2019 | Marcado como **enviado** no histórico de transações e na seção de pagamentos no painel do pagamento | **Relatório de uso**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório de pedidos**: pedidos de clientes mostrados como ativos |
|  |  |  |  |

\*Os relatórios de uso e de pedidos podem ser acessados na seção analisar no Partner Center.

## <a name="process-for-customer-non-payment"></a>Processo de não pagamento de cliente

Em raras ocasiões, a Microsoft não consegue coletar pagamentos de clientes para suas compras do Marketplace comercial. Quando um cliente não paga a Microsoft de acordo com seu agendamento de cobrança, começamos o processo de coleções. Esse processo leva aproximadamente quatro meses e envolve a comunicação persistente da Microsoft. Se o pagamento não for recebido no final deste processo, a Microsoft gravará os fundos como não coletáveis.

De acordo com o processo de pagamento articulado aqui, a Microsoft pode já ter pago os fundos para os editores (você) que, por fim, não podem ser coletados. Portanto, temos um processo para reconciliar esses valores. Para garantir que você tenha um aviso de que o pagamento (já recebido) pode ser reconciliado, você será notificado quando um cliente estiver no processo de cobranças e as compras provavelmente serão gravadas.

A Microsoft recuperará todos os pagamentos já pagos usando um dos seguintes métodos: (1) a Microsoft poderá subtrair os valores não pagos de pagamentos futuros; por exemplo, se $1000 em pagamentos forem considerados não colecionáveis e gravados, seus pagamentos futuros serão retidos até que a $1000 seja recuperada ou (2) a Microsoft poderá solicitar um reembolso ou Publicadores de notas fiscais por quaisquer valores não coletados.

Veja a seguir um exemplo de agendamento:

| Evento | Data aproximada | Visibilidade do parceiro |
| --- | --- | --- |
| Data de pagamento de exemplo | 10/15/2020 | Marcado como **enviado** no histórico de transações e na seção de pagamentos no painel de pagamento |
| <font color="red">Se o cliente não pagar a Microsoft</font> | 12/2/2020 – 12/5/2020 | Nenhuma alteração, igual à acima |
| O cliente recebe o primeiro email de pagamento atrasado | 12/6/2020 | Nenhum |
| O cliente recebe emails regulares de maior urgência | 12/7/2020 – 1/31/2020 | Nenhum |
| A gravação notificada do Publicador é provavelmente | 1/7/2020 | Notificação por email enviada ao Publicador de que seu cliente ainda não enviou o pagamento. A ID da transação e o valor de dólar estão incluídos. |
| O cliente recebe aviso de encerramento | 2/1/2020 | Nenhum |
| O processo de coleta termina/fundos são gravados | 2/15/2020 | Notificação por email enviada ao Publicador de que os fundos foram gravados. A ID da transação e o valor de dólar estão incluídos. |
| Pagamento deduzido | 3/1/2020 | O Publicador verá uma transação negativa na instrução pagamento do Partner Center |
| Pagamento retido | 3/15/2020 | Pagamentos futuros serão mostrados na instrução pagamento do centro de parceiros. O Publicador não receberá o pagamento até que o saldo não seja mais negativo.  |
|||

## <a name="next-step"></a>Próxima etapa

- [Detalhes do imposto](./tax-details-paid-transactions.md)
