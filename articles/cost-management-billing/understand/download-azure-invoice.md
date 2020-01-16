---
title: Exibir e baixar sua fatura do Microsoft Azure
description: Descreve como exibir e baixar sua fatura do Microsoft Azure.
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: 0f413d38565202d379c81570b5cb169c2ed8effe
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987818"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Exibir e baixar sua fatura do Microsoft Azure

Para a maioria das assinaturas, você pode fazer o download da sua fatura no [portal do Microsoft Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-la por e-mail. Se você for um cliente do Azure com um Contrato Enterprise (cliente da EA), não poderá fazer o download das faturas da sua organização. As faturas são enviadas para quem está configurado para receber as faturas da inscrição.

Apenas determinadas funções têm permissão para exibir faturas. Por exemplo, o administrador da conta ou o administrador corporativo. Para obter mais informações sobre como obter acesso às informações de cobrança, confira [Gerenciar acesso à cobrança do Azure usando funções](../manage/manage-billing-access.md).

Caso você tenha um MCA (Contrato de Cliente da Microsoft), precisará ter uma das seguintes funções para receber suas faturas:

- Proprietário do perfil de cobrança
- Colaborador
- Leitor
- Gerenciador de faturas

Caso você tenha um MPA (Contrato de Parceiro da Microsoft), precisará ser o Administrador Global ou o Agente Administrativo na organização parceira para exibir e baixar faturas do Azure. [Verifique o tipo de conta de cobrança](#check-your-billing-account-type) para descobrir quais permissões são necessárias.

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> Por que você não pode receber uma fatura

Pode haver vários motivos pelos quais você não vê uma fatura:

- Faz menos de 30 dias que você se inscreveu no Azure.

- O Azure cobra você no final do período de faturamento. Portanto, uma fatura ainda pode não ter sido gerada. Aguarde até o final do período de cobrança.

- Você não tem permissão para exibir faturas. Caso tenha um MCA ou um MPA, você precisará ser o proprietário, o colaborador, o leitor ou o gerenciador de faturas do perfil de cobrança. Para outras assinaturas, talvez você não veja faturas antigas se não for o Administrador da Conta. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](../manage/manage-billing-access.md).

- Se você tiver uma Avaliação Gratuita ou uma quantidade de crédito mensal com sua assinatura, você só receberá uma fatura quando exceder a quantidade de crédito mensal. Caso tenha um Contrato de Cliente da Microsoft ou um Contrato de Parceiro da Microsoft, você sempre receberá uma fatura.

## <a name="download-invoices-in-the-azure-portal"></a>Baixar as faturas no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.
1. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança ou um perfil de cobrança.
1. No menu esquerdo, selecione **Faturas** em **Cobrança**.
1. Na grade da fatura, encontre a linha da fatura que você deseja baixar.
1. Clique no ícone de download ou nas reticências (`...`) no final da linha.
1. No menu de download, selecione **Fatura**.

Para saber mais sobre a fatura, confira [Entenda sua fatura do Microsoft Azure](review-individual-bill.md). Para obter ajuda com o gerenciamento de custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](../manage/getting-started.md).

## <a name="get-your-subscriptions-invoices-in-email"></a>Receber as faturas de sua assinatura no email

Você pode aceitar e configurar destinatários adicionais para receber sua fatura do Azure em um email. Esse recurso pode não estar disponível para determinadas assinaturas, como ofertas de suporte, Enterprise Agreements ou Azure via Open.

1. Selecione sua assinatura na [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Aceitar para cada assinatura que você possui. Clique em **Faturas** e em **Enviar minha fatura por email**.

    ![Captura de tela que mostra o fluxo de aceitação](./media/download-azure-invoice/invoicesdeeplink01.png)

2. Clique em **Aceitar** e aceite os termos.

    ![Captura de tela que mostra a etapa 2 do fluxo de aceitação](./media/download-azure-invoice/invoicearticlestep02.png)

3. Depois de aceitar o contrato, você poderá configurar destinatários adicionais. Quando um destinatário é removido, o endereço de email não é armazenado. Se você mudar de ideia, precisará adicioná-los novamente.

    ![Captura de tela que mostra a etapa 3 do fluxo de aceitação](./media/download-azure-invoice/invoicearticlestep03.png)

Se você não receber um email depois de seguir as etapas, verifique se seu endereço de email está correto nas [opções de comunicação de seu perfil](https://account.windowsazure.com/profile).

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Cancelar o recebimento das faturas de sua assinatura no email

Para cancelar o recebimento de sua fatura por email, siga as etapas anteriores e clique em **Recusar faturas enviadas por email**. Essa opção remove todos os endereços de e-mail configurados para receber faturas por e-mail. Você poderá reconfigurar os destinatários se aceitar receber novamente.

 ![Captura de tela que mostra o fluxo para recusar](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Receber as faturas do Contrato de Cliente da Microsoft no email

Se você tiver uma conta de cobrança para o contrato do cliente da Microsoft, poderá optar por obter sua fatura em um email. Todos os usuários com uma função de proprietário, colaborador, leitor ou gerente de fatura em um perfil de cobrança receberão sua fatura por email. 

1. Entre no [portal do Azure](https://portal.azure.com).

1. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa no portal para assinatura](./media/download-azure-invoice/search-cmb.png)

1. Selecione **perfis de cobrança** no lado esquerdo. Na lista perfis de cobrança, selecione um perfil de cobrança para obter suas faturas no email.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selecione **Propriedades** no lado esquerdo e, em seguida, selecione **Atualizar preferência de fatura de email**.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. Selecione **aceitar** e clique em **Atualizar**.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Cancelar o recebimento de suas faturas de Contrato de Cliente da Microsoft no email

Para cancelar a obtenção de sua fatura por email, siga as etapas anteriores e clique em **recusar**. Todos os usuários com uma função de proprietário, colaborador, leitor ou Gerenciador de faturas são recusados de obter a fatura por email. 

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>Fornecer a outros acesso às notas fiscais do contrato do cliente da Microsoft

Você pode conceder a outros acesso para exibir, baixar e pagar faturas atribuindo-lhes a função de gerente de faturas para um perfil de cobrança. Se você tiver optado por obter sua fatura por email, esses usuários também receberão as notas fiscais por email. 

1. Entre no [portal do Azure](https://portal.azure.com).

1. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa no portal para assinatura](./media/download-azure-invoice/search-cmb.png)

1. Selecione **perfis de cobrança** no lado esquerdo. Na lista perfis de cobrança, selecione um perfil de cobrança para o qual você deseja atribuir uma função de Gerenciador de faturas.

   [![captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. Selecione **controle de acesso (iam)** no lado esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![captura de tela que mostra a página de controle de acesso](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. Na lista suspensa função, selecione **Gerenciador de faturas**. Insira o endereço de email do usuário a quem você deseja permitir acesso. Selecione **Salvar** para atribuir a função.

   [![captura de tela que mostra a adição de um usuário como um Gerenciador de faturas](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>Verificar o tipo de conta de cobrança
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a fatura e os encargos, confira:

- [Exibir e baixar o uso e os encargos do Microsoft Azure](download-azure-daily-usage.md)
- [Entenda sua fatura do Microsoft Azure](review-individual-bill.md)
- [Noções básicas sobre os termos em sua fatura do Azure](understand-invoice.md)
- [Compreender os termos em uso detalhado do Microsoft Azure](understand-usage.md)
- [Exibir os preços do Azure da sua organização](../manage/ea-pricing.md)

Se você tiver um Contrato de Cliente da Microsoft, confira:

- [Noções básicas sobre encargos na fatura do seu perfil de cobrança](review-customer-agreement-bill.md)
- [Noções básicas sobre termos na fatura do seu perfil de cobrança](mca-understand-your-invoice.md)
- [Noções básicas sobre o arquivo de uso e encargos do Azure do seu perfil de cobrança](mca-understand-your-usage.md)
- [Exibir e baixar documentos de imposto do seu perfil de cobrança](mca-download-tax-document.md)
- [Exibir os preços do Azure da sua organização](../manage/ea-pricing.md)
