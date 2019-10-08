---
title: Entender os encargos em sua fatura do Contrato de Cliente da Microsoft – Azure
description: Saiba como ler e entender os encargos em sua fatura.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8ab6cf061531a1ef3c72b2f36d25932c7498d291
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345274"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Entender os encargos em sua fatura do Contrato de Cliente da Microsoft

Entenda os encargos em sua fatura analisando as transações individuais. Na conta de cobrança de um Contrato de Cliente da Microsoft, uma fatura é gerada todos os meses para cada perfil de cobrança. A fatura inclui todos os encargos do mês anterior. Exiba suas faturas no portal do Azure. Para obter mais informações, confira [Baixar faturas de um Contrato de Cliente da Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Exibir transações de uma fatura no portal do Azure

1. Entre no [Portal do Azure](https://www.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione **Todas as transações** no lado esquerdo da página. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança, um perfil de cobrança ou uma seção da fatura e, em seguida, selecionar **Todas as transações**.

4. A página Todas as transações exibe as seguintes informações:

    ![Captura de tela que mostra a lista de transações faturadas](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Coluna  |Definição  |
    |---------|---------|
    |Data     | A data da transação  |
    |ID da fatura     | O identificador da fatura na qual a transação foi faturada. Se você enviar uma solicitação de suporte, compartilhe a ID com o Suporte do Azure para agilizá-la |
    |Tipo de transação     |  O tipo de transação, como encargos de uso, compra e cancelamento  |
    |Família de produtos     | A categoria do produto, como computação para máquinas virtuais ou banco de dados para o Banco de Dados SQL do Azure|
    |SKU do produto     | Um código exclusivo que identifica a instância do produto |
    |Amount     |  A quantidade de transações      |
    |Seção da fatura     | A transação é exibida nesta seção da fatura do perfil de cobrança |
    |Perfil de faturamento     | A transação é exibida nesta fatura do perfil de cobrança |

5. Pesquise a ID da fatura para filtrar as transações da fatura.

### <a name="view-transactions-by-invoice-sections"></a>Exibir transações por seções da fatura

As seções da fatura ajudam você a organizar os custos da fatura de um perfil de cobrança. Para obter mais informações, confira [Entender a seção da fatura](billing-mca-overview.md#invoice-sections). Quando uma fatura é gerada, os encargos de todas as seções no perfil de cobrança são mostrados na fatura.

A imagem a seguir mostra os encargos da seção da fatura do Departamento de Contabilidade em uma fatura de exemplo.

![Imagem de exemplo mostrando as informações de detalhes por seção da fatura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Ao identificar os encargos de uma seção da fatura, você poderá exibir as transações no portal do Azure para entender os encargos.

1. Acesse a página Todas as transações no portal do Azure para exibir as transações de uma fatura. Para obter mais informações, confira [Exibir transações de uma fatura no portal do Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtre pelo nome da seção da fatura para exibir as transações.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Examinar os encargos pendentes para estimar sua próxima fatura

Na conta de cobrança de um Contrato de Cliente da Microsoft, os encargos são estimados e considerados pendentes até que sejam faturados. Exiba os encargos pendentes no portal do Azure para estimar sua próxima fatura. Os encargos pendentes são estimados e não incluem impostos. Os encargos reais em sua próxima fatura serão diferentes dos encargos pendentes.

### <a name="view-summary-of-pending-charges"></a>Exibir um resumo dos encargos pendentes

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

4. Selecione a guia **Resumo** na parte superior da tela.

5. A seção de encargos exibe os encargos do mês atual e do mês passado.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes e são exibidos em sua próxima fatura.

### <a name="view-pending-transactions"></a>Exibir transações pendentes

Ao identificar encargos pendentes, você pode entender os encargos analisando as transações individuais que contribuíram para eles. Neste ponto, os encargos de uso pendentes não são exibidos na página Todas as transações. Exiba os encargos de uso pendentes na página de assinaturas do Azure. Para obter mais informações, confira [Exibir encargos de uso pendentes](#view-pending-usage-charges)

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

4. Selecione **Todas as transações** no lado esquerdo da página.

5. Pesquise *pendentes*. Use o filtro **Intervalo de tempo** para exibir os encargos pendentes para o mês atual ou o mês passado.

   ![Captura de tela que mostra a lista de transações pendentes](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Exibir encargos de uso pendentes

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise *Gerenciamento de Custos + Cobrança*.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, selecione um perfil de cobrança.

4. Selecione **Todas as assinaturas** no lado esquerdo da página.

5. A página de assinaturas do Azure exibe os encargos do mês atual e do mês passado para cada assinatura do perfil de cobrança. Os encargos do mês atual são os encargos pendentes do mês atual e são faturados quando a fatura é gerada para o mês. Se a fatura do mês passado ainda não foi gerada, os encargos do mês passado também ficam pendentes.

    ![Captura de tela que mostra a lista de assinaturas do Azure para o perfil de cobrança](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analisar os encargos de uso do Azure

Use o arquivo CSV de uso e encargos do Azure para analisar os encargos com base no uso. Baixe o arquivo de uma fatura ou de encargos pendentes. Para obter mais informações, confira [Obter sua fatura de cobrança e os dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Exibir o uso detalhado por seção da fatura

Filtre o arquivo de uso e encargos do Azure para reconciliar os encargos de uso nas seções da fatura.

As seguintes etapas orientam você na reconciliação de encargos de computação na seção da fatura do Departamento de Contabilidade:

![Imagem de exemplo mostrando as informações de detalhes por seção da fatura](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | PDF da fatura | CSV de uso e encargos do Azure |
 | --- | --- |
 |Departamento de contabilidade |invoiceSectionName |
 |Encargos de uso – Plano do Microsoft Azure |productOrderName |
 |Computação |serviceFamily |

1. Filtre a coluna **invoiceSectionName** no arquivo CSV para **Departamento de Contabilidade**.
2. Filtre a coluna **productOrderName** no arquivo CSV para **Plano do Microsoft Azure**.
3. Filtre a coluna **serviceFamily** no arquivo CSV para **Microsoft.Compute**.

![Captura de tela que mostra o arquivo de uso e encargos filtrado por seção da fatura](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Exibir o uso detalhado por assinatura

Filtre o arquivo CSV de uso e encargos do Azure para reconciliar os encargos de uso em suas assinaturas. Para exibir todas as assinaturas em um perfil de cobrança, confira [Exibir encargos de uso pendentes](#view-pending-usage-charges).

Ao identificar os encargos de uma assinatura, use o arquivo CSV de uso e encargos do Azure para analisá-los.

A imagem a seguir mostra a lista de assinaturas no portal do Azure.

![Captura de tela que mostra a lista de assinaturas do Azure para o perfil de cobrança](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtre a coluna **subscriptionName** no arquivo CSV de uso e encargos do Azure para **WA_Subscription** para exibir os encargos de uso detalhado de WA_Subscription.

![Captura de tela que mostra o arquivo de uso e encargos filtrado por assinatura](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar sua fatura

As instruções para pagamento de sua fatura são mostradas na parte inferior da fatura. [Saiba como efetuar o pagamento](billing-mca-understand-your-invoice.md#how-to-pay).

Se você já pagou a fatura, verifique o status do pagamento na página Faturas no portal do Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e o uso detalhado, confira:

- [Como obter sua fatura de cobrança e dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Entender os termos em sua fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-invoice.md)
- [Entender os termos no CSV de uso do Contrato de Cliente da Microsoft](billing-mca-understand-your-usage.md)
