---
title: Entenda os encargos dos serviços externos do Azure
description: Saiba mais sobre a cobrança de serviços externos, anteriormente conhecidos como Marketplace, no Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8da85ec5781ff9575cf380092fea9e41d6af8786
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686115"
---
# <a name="understand-your-azure-external-services-charges"></a>Entender os encargos de serviços externos do Azure
Os serviços externos são publicados por fornecedores de software de terceiros no Azure Marketplace. Por exemplo, o SendGrid é um serviço externo que pode ser comprado no Azure, mas não é publicado pela Microsoft. Alguns produtos da Microsoft também são vendidos por meio do Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Como os serviços externos são cobrados

- Caso você tenha um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft), seus serviços de terceiros serão cobrados com o restante dos serviços do Azure. [Verifique o tipo de sua conta de cobrança](#check-billing-account-type) para ver se você tem acesso a um MCA ou um MPA.
- Caso você não tenha um MCA ou um MPA, os serviços externos serão cobrados separadamente dos serviços do Azure. Você receberá duas faturas a cada período de cobrança: uma para os serviços do Azure e outra para compras de Marketplaces.
- Cada serviço externo tem um modelo de cobrança diferente. Alguns serviços são faturados de acordo com o pagamento conforme o uso, enquanto outros têm encargos mensais fixos.
- Não é possível usar créditos gratuitos mensais para serviços externos. Se você estiver usando uma assinatura do Azure que inclua [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), eles não poderão ser aplicados a encargos de serviços externos. Quando você provisiona um novo recurso ou serviço externo, um aviso é exibido:

    ![Aviso de compra no Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Gastos externos para clientes do EA

Os clientes do EA podem visualizar os gastos de serviços externos e baixar relatórios no portal do EA. Consulte [Azure Marketplace para clientes do EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar.

## <a name="view-and-download-invoices-for-external-services"></a>Exibir e baixar faturas para serviços externos

Caso você tenha um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft), seus serviços de terceiros serão cobrados com o restante dos serviços do Azure em uma só fatura. [Verifique o tipo de sua conta de cobrança](#check-billing-account-type) para ver se você tem acesso a um MCA ou um MPA. Se você fizer isso, confira [Exibir e baixar faturas no portal do Azure](download-azure-invoice.md) para ver os encargos de terceiros.

Caso não tenha um MCA ou um MPA, você terá faturas separadas para encargos de terceiros. 

Os encargos do Azure Marketplace são mostrados em sua moeda local.

Exiba e baixe suas faturas do Azure Marketplace no portal do Azure seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.
1. No menu esquerdo, selecione **Faturas**.
1. Na lista suspensa de assinatura, selecione a assinatura associada aos serviços do Marketplace.
1. Examine a coluna **Tipo** na lista de faturas. Se uma fatura for para um serviço do Marketplace, o tipo será **Azure Marketplace e Reservas**. 

    ![captura de tela do tipo do Azure Marketplace na grade de faturas](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Para filtrar por tipo para que você só examine as faturas do Azure Marketplace e das Reservas, selecione o filtro **Tipo**. Selecione **Azure Marketplace e Reservas** na lista suspensa.

    ![captura de tela do filtro Tipo selecionado, mostrando Azure Marketplace e Reserva selecionado na lista suspensa](./media/understand-azure-marketplace-charges/type-filter.png)

1. Selecione o ícone de download à direita da fatura que você deseja baixar.

    ![captura de tela que mostra o ícone de download selecionado da fatura](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. Em **Fatura**, selecione o botão azul **Download**.

    ![captura de tela que mostra o botão de download da fatura no painel de contexto](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Pague por serviços externos no portal do Azure

Caso você tenha um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft), seus serviços de terceiros serão cobrados com o restante dos serviços do Azure. [Verifique o tipo de sua conta de cobrança](#check-billing-account-type) para ver se você tem acesso a um MCA ou um MPA. Se você fizer isso, poderá pagar por toda a fatura no portal do Azure seguindo as etapas em [Pagar sua fatura do Microsoft Azure](pay-bill.md).

Se você não tiver um MCA ou MPA, poderá pagar por suas faturas do Marketplace no portal do Azure seguindo estas etapas:

1. Siga as etapas na seção anterior, [Exibir e baixar faturas para serviços externos](#view-and-download-invoices-for-external-services), para localizar suas faturas do Marketplace.
1. Selecione o link azul **Pagar agora** da fatura que você deseja pagar.

    ![captura de tela que mostra o link Pagar agora selecionado na grade faturas](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Você só verá o link **Pagar agora** se o tipo de sua fatura for **Azure Marketplace e Reservas** e o status de pagamento da fatura for vencido ou em atraso.

1. Na nova página, clique no link azul **Selecionar forma de pagamento**.

    ![captura de tela que mostra o link selecionar forma de pagamento selecionado](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. Após selecionar uma forma de pagamento, clique no botão azul **Pagar agora** na parte inferior esquerda da página.
    ![captura de tela que mostra o botão pagar agora selecionado](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Alterar pagamento padrão para serviços externos

Ao comprar um serviço externo, você escolhe uma assinatura do Azure para o recurso. A forma de pagamento da assinatura do Azure selecionada se torna a forma de pagamento do serviço externo. Para alterar a forma de pagamento de um serviço externo, é necessário [alterar a forma de pagamento da assinatura do Azure](../manage/change-credit-card.md) vinculada a esse serviço externo. Você pode descobrir a assinatura a qual a pedido de serviço externo está vinculada seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
     ![captura de tela do item de menu Todos os recursos](./media/understand-azure-marketplace-charges/all-resources.png)
1. Pesquise o serviço externo.
1. Procure o nome da assinatura na coluna **Assinatura**.
    ![captura de tela do nome da assinatura para o recurso](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Clique no nome da assinatura e [atualize a forma de pagamento ativa](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Cancelar pedido de serviço externo

Se você deseja cancelar seu pedido de serviço externo, exclua o recurso no [portal do Azure](https://portal.azure.com).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
    ![Captura de tela do item de menu Todos os recursos](./media/understand-azure-marketplace-charges/all-resources.png)
1. Pesquise o serviço externo.
1. Marque a caixa ao lado do recurso que deseja excluir.
1. Selecione **Excluir** na barra de comandos.
    ![Captura de tela do botão Excluir](./media/understand-azure-marketplace-charges/delete-button.png)
1. Digite *'Sim'* na folha de confirmação.
    ![Excluir Recurso](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Clique em **Excluir**.

## <a name="check-billing-account-type"></a>Verificar o tipo de conta de cobrança
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)