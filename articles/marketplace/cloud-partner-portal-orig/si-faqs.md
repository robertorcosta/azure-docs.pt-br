---
title: Perguntas Frequentes sobre os Insights do Vendedor
description: Perguntas frequentes sobre o recurso Insights do Vendedor do Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 059ec175a48cdcdec6214a6581452ec0536bf566
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030442"
---
<a name="seller-insights-faq"></a>Perguntas Frequentes sobre os Insights do Vendedor
===================

Este artigo fornece diretrizes para procedimentos comuns do usuário nos Insights do Vendedor, além de perguntas sobre esse recurso.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Localizar as definições para os valores no arquivo de transação baixado
------------------------------------------------------------------

As definições dos valores de métrica no arquivo de transação são encontradas no artigo [Definições dos Insights do Vendedor](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Consulte os detalhes do cliente de transações pelas quais eu já paguei
-------------------------------------------------------------

Depois de baixar suas transações de módulo de pagamento, localize a coluna rotulada **Status do pagamento** e aplique o filtro para exibir apenas o valor "Pago". As colunas a seguir aparecerão contendo os detalhes do cliente: **Nome da empresa**, **email do cliente**, **país do cliente**, estado do **cliente**e **CEP do cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Calcular o valor a receber pelas minhas contas abertas
-------------------------------------

Depois de baixar suas transações de módulo de pagamento, localize a coluna rotulada **Status do pagamento** e aplique o filtro para exibir apenas o valor "Pagamento futuro" e "Não pronto para pagamento". Em seguida, some a coluna rotulada **Valor do pagamento (PC)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Calcular a receita por período de uso do cliente
------------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da transação**e filtre o valor "pago".   Para cada transação listada, a coluna rotulada **rótulo** representa o valor que foi pago a você.  Para estimar o período de uso associado à transação, use a **data de cobrança**da coluna, que é uma aproximação aproximada do último dia de uso do período ao qual a transação se aplica.


<a name="calculate-your-bad-debt"></a>Calcular sua dívida inválida
---------------------

Depois de baixar suas transações de módulo de pagamento, localize a coluna rotulada **Status final da coleta** e aplique o filtro para exibir apenas o valor "Dar baixa". Em seguida, some a coluna rotulada **Valor do pagamento (PC)** .


<a name="view-payout-or-customer-contact-information"></a>Exibir informações de contato de cliente ou de pagamento
-------------------------------------------

Entre como um usuário com a função "proprietário" e não a função "colaborador". Somente a função de proprietário verá informações de pagamento e do cliente. Você pode encontrar mais informações sobre funções de usuário no artigo [Gerenciar usuários](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcular meus pagamentos antecipados
----------------------------

Depois de baixar suas transações de módulo de pagamento, localize a coluna rotulada **Tipo da transação** e aplique o filtro para exibir apenas o valor "Cobrar". Em seguida, localize a coluna rotulada **Status final da coleta** e aplique o filtro para exibir apenas o valor "Em andamento". Por fim, somar os valores da coluna **Valor do pagamento (PC)** para calcular todos os adiantamentos pagos a você antes da coleta do cliente.


<a name="calculate-customer-refunds"></a>Calcular reembolsos de cliente
--------------------------

Depois de baixar suas transações de módulo de pagamento, localize a coluna rotulada **Status final da coleta** e aplique o filtro para exibir apenas o valor "Reembolso". Some os valores da coluna **Valor do custo (PC)** para calcular todos os reembolsos processados para seus clientes.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identificar quais transações envolveram um Parceiro de Canal da Microsoft
----------------------------------------------------------------

Todas as transações na coluna **Tipo de licença do Azure** que são filtrados para exibir os valores "Enterprise por meio de revendedor" e "Provedor de Soluções na Nuvem" envolvem um Parceiro de Canal da Microsoft. Para obter mais detalhes sobre o parceiro, você pode encontrar seu **Nome do revendedor** e **Email do revendedor** no download do Módulo de pagamento e no download do Módulo de cliente.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificar o uso de avaliação e conversões de avaliação
------------------------------------------

Os downloads de módulo de Ordem, Uso e Pagamento agora contêm **Data de término da avaliação** para ajudá-lo a entender quando o período de avaliação terminou para essa ordem específica, quando aplicável. Para ver ordens e uso de avaliação, localize a coluna **Tipo de cobrança do SKU** nos downloads e aplique o filtro para exibir apenas o valor "Avaliação". Para ver as conversões de avaliação, localize a coluna **Data de término da avaliação** nos downloads e aplique o filtro para exibir apenas as ordens em que a **Data de término da avaliação** é posterior à data atual e a coluna **Data de cancelamento** está vazia ou é posterior à **Data de término da avaliação**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando meu pagamento mensal é calculado
------------------------------------

Seus pagamentos são emitidos para você perto do 15º dia de cada mês para todas as quantidades prontas para pagamento, perto do último dia do calendário do mês anterior. No terceiro dia do mês, a Microsoft calculará o valor do pagamento do mês anterior e atualizará todas as transações de cobrança aplicáveis em seu download com "Pagamento futuro" na coluna **Status do pagamento**. Essas transações permanecerão nesse estado até que a solicitação de pagamento seja enviada para sua conta bancária, momento em que seu **Status do pagamento** será atualizado para "Pago" e a "Data de pagamento" será atualizada para mostrar a data em que enviamos a solicitação de pagamento ao seu banco.


<a name="calculate-customer-acquisition-and-loss"></a>Calcular a perda e a aquisição de clientes
---------------------------------------

Você pode ver a data quando o cliente comprou uma das suas ofertas pela primeira vez localizando a coluna **Data da aquisição** no download do cliente. Da mesma forma, você pode ver a data após a qual ele não tinha mais nenhuma oferta publicada por você, localizando a coluna **Data da perda** no download do cliente.


<a name="finding-more-help"></a>Encontrar mais ajuda
-----------------

- [Definições dos Insights do Vendedor](./si-insights-definitions-v4.md) – localizar definições para métricas e dados

- [Introdução ao Seller Insights](./si-getting-started.md) - Introdução ao recurso Seller Insights.

