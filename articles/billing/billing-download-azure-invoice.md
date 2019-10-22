---
title: Exibir e baixar sua fatura do Microsoft Azure
description: Descreve como exibir e baixar sua fatura do Microsoft Azure.
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375751"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Exibir e baixar sua fatura do Microsoft Azure

Para a maioria das assinaturas, você pode fazer o download da sua fatura no [portal do Microsoft Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-la por e-mail. Se você for um cliente do Azure com um Contrato Enterprise (cliente da EA), não poderá fazer o download das faturas da sua organização. As faturas são enviadas para quem está configurado para receber as faturas da inscrição.

Apenas determinadas funções têm permissão para exibir faturas. Por exemplo, o administrador da conta ou o administrador corporativo. Para obter mais informações sobre como obter acesso às informações de cobrança, confira [Gerenciar acesso à cobrança do Azure usando funções](billing-manage-access.md).

Caso você tenha um MCA (Contrato de Cliente da Microsoft), precisará ter uma das seguintes funções para receber suas faturas:

- Proprietário do perfil de cobrança
- Colaborador
- Leitor
- Gerenciador de faturas

Caso você tenha um MPA (Contrato de Parceiro da Microsoft), precisará ser o Administrador Global ou o Agente Administrativo na organização parceira para exibir e baixar faturas do Azure. [Verifique o tipo de conta de cobrança](#check-your-billing-account-type) para descobrir quais permissões são necessárias. 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Por que você não pode receber uma fatura

Pode haver vários motivos pelos quais você não vê uma fatura:

- Faz menos de 30 dias que você se inscreveu no Azure.

- O Azure cobra você no final do período de faturamento. Portanto, uma fatura ainda pode não ter sido gerada. Aguarde até o final do período de cobrança.

- Você não tem permissão para exibir faturas. Caso tenha um MCA ou um MPA, você precisará ser o proprietário, o colaborador, o leitor ou o gerenciador de faturas do perfil de cobrança. Para outras assinaturas, talvez você não veja faturas antigas se não for o Administrador da Conta. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

- Se você tiver uma Avaliação Gratuita ou uma quantidade de crédito mensal com sua assinatura, você só receberá uma fatura quando exceder a quantidade de crédito mensal. Caso tenha um Contrato de Cliente da Microsoft ou um Contrato de Parceiro da Microsoft, você sempre receberá uma fatura. 

## <a name="download-invoices-in-the-azure-portal"></a>Baixar as faturas no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.
1. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança ou um perfil de cobrança.
1. No menu esquerdo, selecione **Faturas** em **Cobrança**.
1. Na grade da fatura, encontre a linha da fatura que você deseja baixar.
1. Clique no ícone de download ou nas reticências (`...`) no final da linha.
1. No menu de download, selecione **Fatura**.

Para saber mais sobre a fatura, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda com o gerenciamento de custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](billing-getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Receber as faturas de sua assinatura no email

Você pode aceitar e configurar destinatários adicionais para receber sua fatura do Azure em um email. Esse recurso pode não estar disponível para determinadas assinaturas, como ofertas de suporte, Enterprise Agreements ou Azure via Open.

1. Selecione sua assinatura na [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Aceitar para cada assinatura que você possui. Clique em **Faturas** e em **Enviar minha fatura por email**.

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Clique em **Aceitar** e aceite os termos.

    ![Captura de tela que mostra a etapa 2 do fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Depois de aceitar o contrato, você poderá configurar destinatários adicionais. Quando um destinatário é removido, o endereço de email não é armazenado. Se você mudar de ideia, precisará adicioná-los novamente.

    ![Captura de tela que mostra a etapa 3 do fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se você não receber um email depois de seguir as etapas, verifique se seu endereço de email está correto nas [opções de comunicação de seu perfil](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Cancelar o recebimento das faturas de sua assinatura no email

Para cancelar o recebimento de sua fatura por email, siga as etapas anteriores e clique em **Recusar faturas enviadas por email**. Essa opção remove todos os endereços de e-mail configurados para receber faturas por e-mail. Você poderá reconfigurar os destinatários se aceitar receber novamente.

 ![Captura de tela que mostra o fluxo para recusar](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Receber as faturas do Contrato de Cliente da Microsoft no email

Se você tiver um Contrato de Cliente da Microsoft, poderá aceitar receber sua fatura em um email. Todos os perfis de cobrança Proprietários, Colaboradores, Leitores e Gerenciadores de faturas receberão a fatura por email. Os leitores não podem atualizar a preferência de fatura por email.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.
1. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança primeiro.
1. Em **Configurações**, selecione **Propriedades**.
1. Em **Fatura por Email**, selecione **Atualizar preferência de fatura por email**.

    ![Captura de tela que mostra propriedades da fatura por email](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecione **Aceitar**.
1. Clique em **Atualizar**.

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Cancelar o recebimento de suas faturas de Contrato de Cliente da Microsoft no email

Para cancelar o recebimento de sua fatura por email, siga as etapas anteriores e clique em **Recusar**. Todos os Proprietários, Colaboradores, Leitores e Gerenciadores de faturas também não recebem mais a fatura por email. Se você for Leitor, não poderá alterar a preferência de fatura por email.

## <a name="check-your-billing-account-type"></a>Verificar o tipo de conta de cobrança
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a fatura e os encargos, confira:

- [Exibir e baixar o uso e os encargos do Microsoft Azure](billing-download-azure-daily-usage.md)
- [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- [Noções básicas sobre os termos em sua fatura do Azure](billing-understand-your-invoice.md)
- [Compreender os termos em uso detalhado do Microsoft Azure](billing-understand-your-usage.md)
- [Exibir os preços do Azure da sua organização](billing-ea-pricing.md)

Se você tiver um Contrato de Cliente da Microsoft, confira:

- [Noções básicas sobre encargos na fatura do seu perfil de cobrança](billing-mca-understand-your-bill.md)
- [Noções básicas sobre termos na fatura do seu perfil de cobrança](billing-mca-understand-your-invoice.md)
- [Noções básicas sobre o arquivo de uso e encargos do Azure do seu perfil de cobrança](billing-mca-understand-your-usage.md)
- [Exibir e baixar documentos de imposto do seu perfil de cobrança](billing-mca-download-tax-document.md)
- [Exibir os preços do Azure da sua organização](billing-ea-pricing.md)
