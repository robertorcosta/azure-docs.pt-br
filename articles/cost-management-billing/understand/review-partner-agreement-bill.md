---
title: Examinar sua fatura do Contrato de Parceiro da Microsoft – Azure
description: Saiba como examinar sua conta e o uso de recursos e como verificar cobranças em sua fatura do Contrato de Parceiro da Microsoft.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 61d47c2f308555265ccabad4d7456026ee9a639c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299305"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Tutorial: Examinar sua fatura do Contrato de Parceiro da Microsoft

 Na conta de cobrança de um Contrato de Parceiro da Microsoft, uma fatura é gerada todos os meses para cada perfil de cobrança. A fatura inclui todos os encargos do cliente do mês anterior. Entenda os encargos em sua fatura analisando as transações individuais no portal do Azure. Você também pode exibir suas faturas no portal do Azure e comparar os encargos com o arquivo de detalhes de uso.

Para obter mais informações, consulte [Como baixar faturas do portal do Azure](download-azure-invoice.md).

Este tutorial se aplica somente aos parceiros do Azure com um Contrato de Parceiro da Microsoft.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Examinar transações faturadas no portal do Azure
> * Examinar os encargos pendentes para estimar sua próxima fatura
> * Analisar os encargos de uso do Azure

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter acesso a uma conta de cobrança para um Contrato de Parceiro da Microsoft.

É necessário que você tenha assinado o Azure há mais de 30 dias. O Azure cobra você no final do período de faturamento.

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft

Verifique o tipo de contrato para determinar se você tem acesso a uma conta de cobrança para um Contrato de Parceiro da Microsoft.

Na portal do Azure, digite *gerenciamento de custos + cobrança* na caixa de pesquisa e, em seguida, selecione **Gerenciamento de Custos + Cobrança**.

![Captura de tela que mostra a pesquisa do portal do Azure](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Caso você tenha acesso a apenas um escopo do orçamento, selecione **Propriedades** no lado esquerdo. Você tem acesso a uma conta de cobrança para um Contrato de Parceiro da Microsoft se o tipo de conta de cobrança é **Contrato de Parceiro da Microsoft**.

![Captura de tela que mostra o Contrato de Parceiro da Microsoft na página de propriedades](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Caso tenha acesso a vários escopos do orçamento, verifique o tipo na coluna da conta de cobrança. Você tem acesso a uma conta de cobrança para um Contrato de Parceiro da Microsoft se o tipo de conta de cobrança para um dos escopos é **Contrato de Parceiro da Microsoft**.

![Captura de tela que mostra o Contrato de Parceiro da Microsoft na página de lista de contas de cobrança](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Examinar transações faturadas no portal do Azure

Em Gerenciamento de Custos + Cobrança, selecione **Todas as transações** no lado esquerdo da página. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança, um perfil de cobrança ou um cliente e, em seguida, selecionar **Todas as transações**.

A página Todas as transações exibe as seguintes informações:

![Captura de tela que mostra a lista de transações faturadas](./media/review-partner-agreement-bill/all-transactions.png)

|Coluna  |Definição  |
|---------|---------|
|Data     | A data da transação  |
|ID da fatura     | O identificador da fatura na qual a transação foi faturada. Se você enviar uma solicitação de suporte, compartilhe a ID com o Suporte do Azure para agilizá-la |
|Tipo de transação     |  O tipo de transação, como encargos de uso, compra e cancelamento  |
|Família de produtos     | A categoria do produto, como computação para máquinas virtuais ou banco de dados para o Banco de Dados SQL do Azure|
|SKU do produto     | Um código exclusivo que identifica a instância do produto |
|Amount     |  A quantidade de transações      |
|Perfil de faturamento     | A transação é exibida nesta fatura do perfil de cobrança |

Pesquise uma ID da fatura para filtrar as transações da fatura.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Examinar os encargos pendentes para estimar sua próxima fatura

Os encargos são estimados e considerados pendentes até que sejam faturados. Você pode exibir os encargos pendentes do seu perfil de cobrança do Contrato de Parceiro da Microsoft no portal do Azure para estimar sua próxima fatura. Os encargos pendentes são estimados e não incluem impostos. Os encargos reais em sua próxima fatura serão diferentes dos encargos pendentes.

### <a name="view-pending-transactions"></a>Exibir transações pendentes

Ao identificar encargos pendentes, você pode entender os encargos analisando as transações individuais que contribuíram para eles. Neste ponto, os encargos de uso pendentes não são exibidos na página Todas as transações. Exiba os encargos de uso pendentes na página de assinaturas do Azure.

Em Gerenciamento de Custos + Cobrança, selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

Selecione **Todas as transações** no lado esquerdo da página.

Pesquise *pendentes*. Use o filtro **Intervalo de tempo** para exibir os encargos pendentes para o mês atual ou o mês passado.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Exibir encargos pendentes por cliente

Em Gerenciamento de Custos + Cobrança, selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

Selecione **Clientes** no lado esquerdo da página.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

A página Clientes exibe os encargos do mês atual e do mês passado para cada assinatura de cliente do perfil de cobrança. Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes.

### <a name="view-pending-usage-charges"></a>Exibir encargos de uso pendentes

Em Gerenciamento de Custos + Cobrança, selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

Selecione **Assinaturas do Azure** no lado esquerdo da página. A página de assinaturas do Azure exibe os encargos do mês atual e do mês passado para cada assinatura do perfil de cobrança. Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analisar os encargos de uso do Azure

Use o arquivo CSV de uso e encargos do Azure para analisar os encargos com base no uso. É possível filtrar o arquivo de uso e encargos do Azure para reconciliar os encargos de uso para cada produto listado no arquivo .pdf da fatura. Para exibir os encargos de uso detalhados de um produto específico, filtre a coluna **Produto** no arquivo CSV de uso e encargos do Azure para incluir apenas o nome do produto.

Também é possível filtrar a coluna **customerName** no arquivo CSV de uso e encargos do Azure para exibir os encargos de uso diários de cada um dos seus clientes. Se você quiser exibir os encargos de uso diário por assinatura do Azure, filtre a coluna **subscriptionName**.

## <a name="pay-your-bill"></a>Pagar sua fatura

As instruções para pagamento de sua fatura são mostradas na parte inferior da fatura. Você pode pagar por transferência ou cheque dentro de 60 dias da data da fatura.

Se você já pagou a fatura, verifique o status do pagamento na página Faturas no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Examinar transações faturadas no portal do Azure
> * Examinar os encargos pendentes para estimar sua próxima fatura
> * Analisar os encargos de uso do Azure

Saiba como usar o Gerenciamento de Custos do Azure para parceiros.

> [!div class="nextstepaction"]
> [Introdução ao Gerenciamento de Custos do Azure para parceiros](../costs/get-started-partners.md)
