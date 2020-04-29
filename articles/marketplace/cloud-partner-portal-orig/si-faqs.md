---
title: Perguntas Frequentes sobre os Insights do Vendedor
description: Perguntas frequentes sobre o recurso Insights do Vendedor do Portal do Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285377"
---
<a name="seller-insights-faq"></a>Perguntas Frequentes sobre os Insights do Vendedor
===================

Este artigo fornece diretrizes para procedimentos comuns do usuário nos Insights do Vendedor, além de perguntas sobre esse recurso.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Localizar as definições para os valores no arquivo de transação baixado
------------------------------------------------------------------

As definições dos valores de métrica no arquivo de transação são encontradas no artigo [Definições dos Insights do Vendedor](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Consulte os detalhes do cliente de transações pelas quais eu já paguei
-------------------------------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status de pagamento**e aplique o filtro para exibir apenas o valor "pago". As colunas a seguir serão exibidas, contendo os detalhes do cliente: **Nome da empresa**, **Email do cliente**, ** do cliente**, **Estado do cliente** e **Código postal do cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Calcular o valor a receber pelas minhas contas abertas
-------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status de pagamento**e aplique o filtro para exibir apenas o valor "pagamento futuro" e "não está pronto para pagamento". Em seguida, some a coluna rotulada **Valor do pagamento (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Calcular a receita por período de uso do cliente
------------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da transação**e filtre o valor "pago".   Para cada transação listada, a coluna rotulada **rótulo** representa o valor que foi pago a você.  Para calcular o período de uso associado à transação, use a coluna **Data de cobrança**, que é uma aproximação do último dia de uso do período para o qual a transação se aplica.


<a name="calculate-your-bad-debt"></a>Calcular sua dívida inválida
---------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da coleção final**e aplique o filtro para exibir apenas o valor "write off". Em seguida, some a coluna rotulada **Valor do pagamento (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Exibir informações de contato de cliente ou de pagamento
-------------------------------------------

Entre como um usuário com a função "proprietário" e não a função "colaborador". Somente a função de proprietário verá informações de pagamento e do cliente. Você pode encontrar mais informações sobre funções de usuário no artigo [Gerenciar usuários](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcular meus pagamentos antecipados
----------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna chamada **tipo de transação**e aplique o filtro para exibir apenas o valor "encargo". Em seguida, localize a coluna denominada **status da coleção final**e aplique o filtro para exibir apenas o valor "em andamento". Por fim, somar os valores da coluna **Valor do pagamento (PC)** para calcular todos os adiantamentos pagos a você antes da coleta do cliente.


<a name="calculate-customer-refunds"></a>Calcular reembolsos de cliente
--------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da coleção final**e aplique o filtro para exibir apenas o valor "reembolso". Some os valores da coluna **Valor do custo (PC)** para calcular todos os reembolsos processados para seus clientes.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identificar quais transações envolveram um Parceiro de Canal da Microsoft
----------------------------------------------------------------

Todas as transações na coluna **tipo de licença do Azure** que são filtradas para exibir os valores "Enterprise por revendedor" e "provedor de soluções na nuvem" envolvem um parceiro de canal da Microsoft. Para obter mais detalhes sobre o parceiro, você pode encontrar seu **Nome do revendedor** e **Email do revendedor** no download do Módulo de pagamento e no download do Módulo de cliente.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificar o uso de avaliação e conversões de avaliação
------------------------------------------

Os downloads de módulo de Ordem, Uso e Pagamento agora contêm **Data de término da avaliação** para ajudá-lo a entender quando o período de avaliação terminou para essa ordem específica, quando aplicável. Para ver o uso e as ordens de avaliação, localize a coluna **tipo de cobrança de SKU** nos downloads e aplique o filtro para exibir apenas o valor "avaliação". Para ver as conversões de avaliação, localize a coluna **data de término da avaliação** nos downloads e aplique o filtro para exibir apenas os pedidos quando a data de término da **avaliação** for anterior à data de hoje e a coluna de **data de cancelamento** estiver vazia ou posterior à data de término da **avaliação**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando meu pagamento mensal é calculado
------------------------------------

Seus pagamentos são emitidos para você perto do 15º dia de cada mês para todas as quantidades prontas para pagamento, perto do último dia do calendário do mês anterior. No terceiro dia do mês, a Microsoft calculará o valor de pagamento do mês anterior e atualizará todas as transações de cobrança aplicáveis em seu download com "pagamento futuro" na coluna **status do pagamento** . Essas transações permanecerão nesse estado até que a solicitação de pagamento seja enviada para sua conta bancária, quando o **status** do pagamento for atualizado para "pago", e a "data de pagamento" será atualizada para mostrar a data em que enviamos a solicitação de pagamento ao seu banco.


<a name="calculate-customer-acquisition-and-loss"></a>Calcular a perda e a aquisição de clientes
---------------------------------------

Você pode ver a data quando o cliente comprou uma das suas ofertas pela primeira vez localizando a coluna **Data da aquisição** no download do cliente. Da mesma forma, você pode ver a data após a qual ele não tinha mais nenhuma oferta publicada por você, localizando a coluna **Data da perda** no download do cliente.


<a name="finding-more-help"></a>Encontrar mais ajuda
-----------------

- [Definições do Seller Insights](./si-insights-definitions-v4.md) - Localizar definições para métricas e dados

- [Introdução ao Seller Insights](./si-getting-started.md) - Introdução ao recurso Seller Insights.

