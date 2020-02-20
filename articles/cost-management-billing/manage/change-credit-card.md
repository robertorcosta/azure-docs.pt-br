---
title: Alterar seu cartão de crédito para o Azure
description: Descreve como alterar o cartão de crédito usado para pagar uma assinatura do Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 0cf0911193cd379da60edb3064d3c6f195cf0190
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199850"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Adicionar, atualizar ou remover um cartão de crédito para o Azure

Este documento aplica-se a clientes que se inscreveram no Azure online com um cartão de crédito.

No portal do Azure, você pode alterar o método de pagamento padrão para um novo cartão de crédito, atualizar os detalhes do cartão de crédito e excluir os cartões de crédito que você não usa. Você deve ser um [administrador da conta](billing-subscription-transfer.md#whoisaa) para fazer essas alterações.

Os métodos de pagamento com suporte para Microsoft Azure são cartões de crédito e cheque/transferência eletrônica. Para ser aprovado para pagar por cheque/transferência eletrônica, confira [Pagar assinaturas do Azure por fatura](pay-by-invoice.md).

Se você tiver um Contrato de Cliente da Microsoft, as formas de pagamento serão associadas aos perfis de cobrança. Saiba como [verificar o acesso ao Contrato de Cliente da Microsoft](#check-the-type-of-your-account). Se você tiver um MCA, pule para [gerenciar cartões de crédito para um Contrato de Cliente da Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Gerenciar cartões de crédito de uma assinatura do Azure

As seções a seguir se aplicam a clientes que têm uma conta de cobrança do programa Microsoft Online Services. Saiba como [verificar o tipo de sua conta de cobrança](#check-the-type-of-your-account). Se o tipo de conta de cobrança for o programa Microsoft Online Services, os métodos de pagamento serão associados a assinaturas individuais do Azure.

### <a name="change-credit-card-for-a-subscription"></a>Alterar o cartão de crédito de uma assinatura

Você pode alterar o crédito padrão da sua assinatura do Azure para um novo cartão de crédito ou um cartão de crédito salvo anteriormente no portal do Azure. Você precisa ser administrador da conta para alterar o cartão de crédito. Se mais de uma de suas assinaturas tiverem a mesma forma de pagamento ativa, a alteração dessa forma de pagamento ativa em qualquer uma dessas assinaturas também atualizará a das demais.


Use estas etapas para alterar o cartão de crédito padrão da sua assinatura para um novo:

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)

1. Selecione a assinatura à qual você deseja adicionar o cartão de crédito.
1. Selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. No canto superior esquerdo, selecione "+" para adicionar um cartão. Um formulário de cartão de crédito será exibido à direita.
1. Insira os detalhes do cartão de crédito.

    ![Captura de tela que mostra como adicionar um novo cartão.](./media/change-credit-card/sub-add-new-x.png)

1. Para tornar este cartão sua forma de pagamento ativa, marque a caixa ao lado de **Tornar esta minha forma de pagamento ativa** acima do formulário. Esse cartão se tornará o meio de pagamento ativo para todas as assinaturas usando o mesmo cartão, como a assinatura selecionada.

1. Selecione **Avançar**.

Se você receber um erro depois de adicionar o cartão de crédito, consulte [Cartão de crédito recusado na inscrição do Azure](../../billing/billing-credit-card-fails-during-azure-sign-up.md).

Também é possível executar estas etapas para alterar o cartão de crédito padrão da sua assinatura para um que já esteja salvo em sua conta:

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)

1. Selecione a assinatura à qual você deseja adicionar o cartão de crédito.
1. Selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. Marque a caixa ao lado do cartão que você gostaria de tornar a forma de pagamento ativa.
1. Clique em **Definir como ativo**.
    ![Captura de tela que mostra o cartão de crédito selecionado e definido como ativo.](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Editar os detalhes do cartão de crédito

Caso seu cartão de crédito seja renovado e o número permaneça o mesmo, atualize os detalhes do cartão de crédito existente, como a data de vencimento. Se o número de seu cartão de crédito for alterado devido a perda, roubo ou vencimento do cartão, siga as etapas da seção [Adicionar um cartão de crédito como uma forma de pagamento](#addcard). Você não precisa atualizar o CVV.

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)

1. Selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. Clique no cartão de crédito que você deseja editar. Um formulário de cartão de crédito será exibido à direita.

    ![Captura de tela que mostra o cartão de crédito selecionado.](./media/change-credit-card/edit-card-x.png)

1. Atualize os detalhes do cartão de crédito.
1. Clique em **Salvar**.

### <a name="delete-a-credit-card-from-the-account"></a>Excluir um cartão de crédito da conta

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta.
1. Selecione **Gerenciamento de Custos + Cobrança** do lado esquerdo da página.

    ![Captura de tela que mostra a pesquisa](./media/change-credit-card/search.png)

1. Em **Cobrança**, selecione **Formas de pagamento**.

    ![Captura de tela que mostra a opção Gerenciar formas de pagamento selecionada.](./media/change-credit-card/payment-methods-blade-x.png)

1. Selecione a caixa ao lado do cartão que você deseja remover.
1. Clique em **Excluir**.

Se o seu cartão de crédito for a forma de pagamento ativa para as assinaturas da Microsoft, não será possível removê-lo de sua conta do Azure. Altere a forma de pagamento ativa para todas as assinaturas vinculadas a esse cartão de crédito e tente novamente

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Gerenciar cartões de crédito para um Contrato de Cliente da Microsoft

As seções a seguir se aplicam a clientes que têm um Contrato de Cliente da Microsoft e se inscreveram no Azure Online com um cartão de crédito. [Saiba como verificar se você tem um Contrato de Cliente da Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Alterar o cartão de crédito padrão
Se você tiver um Contrato de Cliente da Microsoft, seu cartão de crédito será associado a um perfil de cobrança. Para alterar a forma de pagamento de um perfil de cobrança, você deve ser a pessoa que se inscreveu no Azure e criou a conta de cobrança.

Se você quiser alterar o método de pagamento padrão do perfil de cobrança para cheque/transferência eletrônica, confira [Pagar assinaturas do Azure por fatura](pay-by-invoice.md).

Para alterar seu cartão de crédito, execute estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. No menu à esquerda, clique em **Perfis de cobrança**.
1. Selecione um perfil de cobrança.
1. No menu à esquerda, selecione **Formas de pagamento**.

   ![Captura de tela que mostra as formas de pagamento no menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. Na seção **Método de pagamento padrão**, clique em **Alterar**.

    ![Captura de tela que mostra o botão alterar](./media/change-credit-card/change-payment-method-mca.png)

1. Na folha nova à direita, selecione um cartão existente na lista suspensa ou adicione um novo clicando no link azul "Adicionar novo método de pagamento".

### <a name="edit-or-delete-a-credit-card"></a>Editar ou excluir um cartão de crédito

É possível editar detalhes do cartão de crédito (como atualizar a data de validade) e excluir cartões de crédito da sua conta no portal do Azure. Você só poderá excluir um cartão de crédito se ele não estiver associado a nenhuma assinatura do Azure ou perfil de cobrança. Se ele estiver associado a uma assinatura desabilitada do Azure, você deverá aguardar a exclusão da assinatura (30 a 90 dias após o cancelamento) para excluir o cartão de crédito.

Para editar ou excluir um cartão de crédito, execute estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. No menu à esquerda, clique em **Perfis de cobrança**.
1. Selecione um perfil de cobrança.
1. No menu à esquerda, selecione **Formas de pagamento**.

   ![Captura de tela que mostra as formas de pagamento no menu](./media/change-credit-card/payment-methods-tab-mca.png)

1. Na seção **Seus cartões de crédito**, encontre o cartão de crédito que você quer editar ou excluir.
1. Selecione as reticências (`...`) no final da linha.

    ![Captura de tela que mostra as reticências](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. Para editar os detalhes do seu cartão de crédito, selecione **Editar** no menu de contexto.
1. Para excluir seu cartão de crédito, selecione **Excluir** no menu de contexto.

## <a name="troubleshooting"></a>Solução de problemas
Não damos suporte a cartões virtuais ou pré-pagos. Se estiver recebendo erros ao adicionar ou atualizar um cartão de crédito válido, tente abrir o navegador no modo privado.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
As seções a seguir respondem às perguntas mais frequentes sobre como alterar as informações do seu cartão de crédito.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Minha assinatura está desabilitada. Por que não é possível remover o cartão de crédito agora?

Depois que sua assinatura estiver desabilitada ou cancelada, poderemos esperar 90 dias antes de excluir permanentemente a sua assinatura. Mantemos sua forma de pagamento no arquivo durante o período de retenção caso você deseje reativar a assinatura. Após isso, a assinatura será excluída permanentemente.

Se precisar remover seu cartão de crédito antes do período de retenção de 90 dias terminar, [reative sua assinatura](subscription-disabled.md). Se não conseguir reativar, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Por que continuo recebendo "Sua sessão de logon expirou. Clique aqui para fazer logon novamente"?

Se você continuar recebendo essa mensagem de erro mesmo que já tenha feito o logout e voltado, tente novamente com uma sessão de navegação privada.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Como usar um cartão diferente para cada assinatura que tenho?

Infelizmente, se suas assinaturas já estiverem usando o mesmo cartão, não será possível usar cartões diferentes. No entanto, ao se inscrever para uma nova assinatura, você pode escolher usar uma nova forma de pagamento para essa assinatura.

### <a name="how-do-i-make-payments"></a>Como faço pagamentos?

Se você tiver configurado um cartão de crédito como forma de pagamento, faremos a cobrança no cartão automaticamente após o período de cobrança. Você não precisa fazer nada.

Se estiver [pagando por fatura](pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

### <a name="how-do-i-change-the-tax-id"></a>Como alterar a ID de imposto?

Para adicionar ou atualizar o TID, atualize seu perfil no [Centro de Contas do Azure](https://account.azure.com/Profile), em seguida, selecione **Registro fiscal**. Essa ID de imposto é usada para cálculos de isenção de impostos e aparece em sua fatura.

## <a name="check-the-type-of-your-account"></a>Verifique o tipo de sua conta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [reservas do Azure](../reservations/save-compute-costs-reservations.md) para ver se elas podem fazer você economizar dinheiro.
