---
title: Entender os encargos em sua fatura do Contrato de Parceiro da Microsoft – Azure
description: Saiba como ler e entender os encargos em sua fatura.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 579a052f8bde780ac33de85c5a08d9b3e79d3096
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515775"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>Entender os encargos em sua fatura do Contrato de Parceiro da Microsoft

 Na conta de cobrança de um Contrato de Parceiro da Microsoft, uma fatura é gerada todos os meses para cada perfil de cobrança. A fatura inclui todos os encargos do cliente do mês anterior. Entenda os encargos em sua fatura analisando as transações individuais no portal do Azure. Também é possível exibir suas faturas no portal do Azure. Para obter mais informações, consulte [Como baixar faturas do portal do Azure](billing-download-azure-invoice.md).

Este artigo aplica-se a uma conta de cobrança para um Contrato de Parceiro da Microsoft. [Verifique se você tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Exibir transações de uma fatura no portal do Azure

1. Entre no [Portal do Azure](https://www.azure.com).

2. Pesquise *Gerenciamento de Custos + Cobrança*.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selecione **Todas as transações** no lado esquerdo da página. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança, um perfil de cobrança ou um cliente e, em seguida, selecionar **Todas as transações**.

4. A página Todas as transações exibe as seguintes informações:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Coluna  |Definição  |
    |---------|---------|
    |Data     | A data da transação  |
    |ID da fatura     | O identificador da fatura na qual a transação foi faturada. Se você enviar uma solicitação de suporte, compartilhe a ID com o Suporte do Azure para agilizá-la |
    |Tipo de transação     |  O tipo de transação, como encargos de uso, compra e cancelamento  |
    |Família de produtos     | A categoria do produto, como computação para máquinas virtuais ou banco de dados para o Banco de Dados SQL do Azure|
    |SKU do produto     | Um código exclusivo que identifica a instância do produto |
    |Amount     |  A quantidade de transações      |
    |Perfil de faturamento     | A transação é exibida nesta fatura do perfil de cobrança |

5. Pesquise a ID da fatura para filtrar as transações da fatura.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Examinar os encargos pendentes para estimar sua próxima fatura

Os encargos são estimados e considerados pendentes até que sejam faturados. Você pode exibir os encargos pendentes do seu perfil de cobrança do Contrato de Parceiro da Microsoft no portal do Azure para estimar sua próxima fatura. Os encargos pendentes são estimados e não incluem impostos. Os encargos reais em sua próxima fatura serão diferentes dos encargos pendentes.

### <a name="view-pending-transactions"></a>Exibir transações pendentes

Ao identificar encargos pendentes, você pode entender os encargos analisando as transações individuais que contribuíram para eles. Neste ponto, os encargos de uso pendentes não são exibidos na página Todas as transações. Exiba os encargos de uso pendentes na página de assinaturas do Azure. Para obter mais informações, confira [Exibir encargos de uso pendentes](#view-pending-usage-charges)

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise *Gerenciamento de Custos + Cobrança*.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

4. Selecione **Todas as transações** no lado esquerdo da página.

5. Pesquise *pendentes*. Use o filtro **Intervalo de tempo** para exibir os encargos pendentes para o mês atual ou o mês passado.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Exibir encargos pendentes por cliente

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise *Gerenciamento de Custos + Cobrança*.

3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

4. Selecione **Clientes** no lado esquerdo da página.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. A página Clientes exibe os encargos do mês atual e do mês passado para cada assinatura de cliente do perfil de cobrança. Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes.

### <a name="view-pending-usage-charges"></a>Exibir encargos de uso pendentes

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise *Gerenciamento de Custos + Cobrança*.

3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

4. Selecione **Assinaturas do Azure** no lado esquerdo da página.

5. A página de assinaturas do Azure exibe os encargos do mês atual e do mês passado para cada assinatura do perfil de cobrança. Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Analisar os encargos de uso do Azure

Use o arquivo CSV de uso e encargos do Azure para analisar os encargos com base no uso. [Baixe um arquivo CSV com seu uso e encargos do Azure no portal do Azure](billing-download-azure-daily-usage.md).

É possível filtrar o arquivo de uso e encargos do Azure para reconciliar os encargos de uso para cada produto listado no arquivo .pdf da fatura. Para exibir os encargos de uso detalhados de um produto específico, filtre a coluna **Produto** no arquivo CSV de uso e encargos do Azure para incluir apenas o nome do produto.

Também é possível filtrar a coluna **customerName** no arquivo CSV de uso e encargos do Azure para exibir os encargos de uso diários de cada um dos seus clientes. Se você quiser exibir os encargos de uso diário por assinatura do Azure, filtre a coluna **subscriptionName**.


## <a name="pay-your-bill"></a>Pagar sua fatura

As instruções para pagamento de sua fatura são mostradas na parte inferior da fatura. Você pode pagar por transferência ou cheque dentro de 60 dias da data da fatura.

Se você já pagou a fatura, verifique o status do pagamento na página Faturas no portal do Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e o uso detalhado, confira:
