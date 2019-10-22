---
title: Entenda os encargos dos serviços externos do Azure | Microsoft Docs
description: Saiba mais sobre a cobrança de serviços externos, anteriormente conhecidos como Marketplace, no Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2803f8d659726ac95bcefa6191a816ed2b2dcb2d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375403"
---
# <a name="understand-your-azure-external-services-charges"></a>Entender os encargos de serviços externos do Azure
Os serviços externos são publicados por fornecedores de software de terceiros no Azure Marketplace. Por exemplo, o SendGrid é um serviço externo que pode ser comprado no Azure, mas não é publicado pela Microsoft. Alguns produtos da Microsoft também são vendidos por meio do Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Como os serviços externos são cobrados

- Caso você tenha um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft), seus serviços de terceiros serão cobrados com o restante dos serviços do Azure. [Verifique o tipo de sua conta de cobrança](#check-billing-account-type) para ver se você tem acesso a um MCA ou um MPA.
- Caso você não tenha um MCA ou um MPA, os serviços externos serão cobrados separadamente dos serviços do Azure.
- Cada serviço externo tem um modelo de cobrança diferente. Alguns serviços são faturados de acordo com o pagamento conforme o uso, enquanto outros têm encargos mensais fixos.
- Não é possível usar créditos gratuitos mensais para serviços externos. Se você estiver usando uma assinatura do Azure que inclua [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), eles não poderão ser aplicados a encargos de serviços externos. Quando você provisiona um novo recurso ou serviço externo, um aviso é exibido:

    ![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices-for-external-services"></a>Exibir e baixar faturas para serviços externos

Caso você tenha um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft), seus serviços de terceiros serão cobrados com o restante dos serviços do Azure. [Verifique o tipo de sua conta de cobrança](#check-billing-account-type) para ver se você tem acesso a um MCA ou um MPA. Se você fizer isso, confira [Exibir e baixar faturas no portal do Azure](billing-download-azure-invoice.md) para ver os encargos de terceiros.

Caso não tenha um MCA ou um MPA, você terá faturas separadas para encargos de terceiros. Exiba e baixe suas faturas do Azure Marketplace no portal do Azure seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.
1. No menu esquerdo, selecione **Faturas**.
1. Clique na guia **Azure Marketplace e Reservas**.  ![Imagem da guia Azure Marketplace e Reservas](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Na lista suspensa de assinaturas, selecione a assinatura que contém os serviços externos para os quais deseja ver as faturas.

## <a name="external-spending-for-ea-customers"></a>Gastos externos para clientes do EA

Os clientes do EA podem visualizar os gastos de serviços externos e baixar relatórios no portal do EA. Consulte [Azure Marketplace para clientes do EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar.

## <a name="manage-payment-for-external-services"></a>Gerenciar o pagamento de serviços externos

Ao comprar um serviço externo, você escolhe uma assinatura do Azure para o recurso. A forma de pagamento da assinatura do Azure selecionada se torna a forma de pagamento do serviço externo. Para alterar a forma de pagamento de um serviço externo, é necessário [alterar a forma de pagamento da assinatura do Azure](billing-how-to-change-credit-card.md) vinculada a esse serviço externo. Você pode descobrir a assinatura a qual a pedido de serviço externo está vinculada seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
     ![captura de tela do item de menu Todos os recursos](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Pesquise o serviço externo.
1. Procure o nome da assinatura na coluna **Assinatura**.
    ![captura de tela do nome da assinatura para o recurso](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Clique no nome da assinatura e [atualize a forma de pagamento ativa](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Cancelar pedido de serviço externo
Se você deseja cancelar seu pedido de serviço externo, exclua o recurso no [portal do Azure](https://portal.azure.com).

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Clique em **Todos os recursos** no menu de navegação à esquerda.
    ![Captura de tela do item de menu Todos os recursos](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Pesquise o serviço externo.
1. Marque a caixa ao lado do recurso que deseja excluir.
1. Selecione **Excluir** na barra de comandos.
    ![Captura de tela do botão Excluir](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Digite *'Sim'* na folha de confirmação.
    ![Excluir Recurso](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Clique em **Excluir**.

## <a name="check-billing-account-type"></a>Verificar o tipo de conta de cobrança
[!INCLUDE [billing-check-account-type](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- [Começar a analisar os custos](../cost-management/quick-acm-cost-analysis.md)
