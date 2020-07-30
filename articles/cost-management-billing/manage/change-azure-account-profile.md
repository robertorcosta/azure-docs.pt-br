---
title: Alterar as informações de contato de sua conta do Azure
description: Descreve como alterar as informações de contato de sua conta administrativa do Azure
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 449f1e50bda2991db8eec15464a32d295a054c6f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286590"
---
# <a name="change-the-contact-information-for-your-azure-account"></a>Alterar as informações de contato de sua conta do Azure

Este artigo ajuda você a atualizar as informações de contato para sua conta no portal do Azure. As instruções para atualizar as informações de contato variam de acordo com o tipo de conta de cobrança. Para saber mais sobre contas de cobrança e identificar seu tipo conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](view-all-accounts.md).

*Endereço do comprador* – o endereço do comprador é composto pelo endereço e as informações de contato da organização ou do indivíduo responsável por uma conta de cobrança. Ele é exibido em todas as faturas geradas para a conta de cobrança.

*Endereço para cobrança* – o endereço para cobrança é composto pelo endereço e pelas informações de contato da organização ou do indivíduo responsável pelas faturas geradas para uma conta de cobrança. Para uma conta de cobrança de um MOSP (Programa de Serviço Online da Microsoft), há um endereço para cobrança, que é exibido em todas as faturas geradas para a conta. Para uma conta de cobrança para um MCA (Contrato de Cliente da Microsoft), há um endereço para cobrança para cada perfil de cobrança e ele é exibido na fatura gerada para o perfil de cobrança.

*Endereço de email de contato para emails de serviço e marketing* – para receber notificações importantes de cobrança, serviço e recomendação relacionadas à sua conta do Azure, você pode especificar um endereço de email diferente daquele com o qual você entra.  

## <a name="update-an-mosp-billing-account-address"></a>Atualizar um endereço da conta de cobrança do MOSP

1. Entre no [portal do Azure](https://portal.azure.com/) usando o endereço de email, que tem permissão de administrador da conta na conta em questão.
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/change-azure-account-profile/search-cmb.png)
1. Selecione **Propriedades** no lado esquerdo.  
    ![Captura de tela que mostra a página de atualização de endereço](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Selecione **Atualizar o endereço para cobrança** para atualizar o endereço do comprador e o endereço para cobrança. Insira o novo endereço e selecione **Salvar**.  
    ![Captura de tela que mostra a página de atualização de endereço](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Atualizar um endereço do comprador de conta de cobrança do MCA

1. Entre no [portal do Azure](https://portal.azure.com/) usando o endereço de email, que tem uma função de proprietário ou colaborador na conta de cobrança para um Contrato de Cliente da Microsoft.
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/change-azure-account-profile/search-cmb.png)
1. Selecione **Propriedades** do lado esquerdo e escolha **Atualizar o campo Comprador**.  
    ![Captura de tela que mostra a seleção da opção Atualizar o campo Comprador](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Insira o novo endereço e depois selecione **Salvar**.  
    ![Captura de tela que mostra a atualização do endereço](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Algumas contas exigem verificação adicional antes que as respectivas informações de comprador possam ser atualizadas. Se a sua conta exigir aprovação manual, será solicitado que você entre em contato com o Suporte do Azure.

## <a name="update-an-mca-billing-account-address"></a>Atualizar um endereço da conta de cobrança do MCA

1. Entre no [portal do Azure](https://portal.azure.com/) usando o endereço de email, que tem uma função de proprietário ou colaborador em uma conta de cobrança ou um perfil de cobrança para um MCA.
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/change-azure-account-profile/search-cmb.png)
1. Selecione **Perfis de cobrança** do lado esquerdo.
1. Selecione um perfil de cobrança para atualizar o endereço para cobrança.  
    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Selecione **Propriedades** no lado esquerdo.
1. Selecione **Atualizar endereço**.  
    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Insira o novo endereço e selecione **Salvar**.  
    ![Captura de tela que mostra a atualização do endereço](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>Emails de serviço e de marketing

No [portal do Azure](https://portal.azure.com), você é solicitado a verificar ou atualizar seu endereço de email a cada 90 dias. A Microsoft envia emails para esse endereço de email com as informações relacionadas à conta do Azure para:

- Notificações de serviço
- Alertas de segurança
- Fins de cobrança
- Suporte
- Comunicações de marketing
- Recomendações de melhores práticas, com base no seu uso do Azure

Insira o endereço de email em que você deseja receber comunicações sobre sua conta. Ao inserir um endereço de email, você aceitará receber comunicações da Microsoft.

![Exemplo do prompt para atualizar suas informações de contato](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Altere seu endereço de email de contato

É possível alterar seu endereço de email de contato usando um dos métodos a seguir. A atualização do seu endereço de email de contato não atualiza o endereço de email com o qual você entra.

* Se você for um administrador de uma conta do MOSP, siga as instruções em [Atualizar um endereço da conta de cobrança do MOSP](#update-an-mosp-billing-account-address) e selecione **Atualizar informações de contato** na última etapa. Em seguida, insira o novo endereço de email.

* Acesse a área [Informações de contato](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) no portal do Azure e insira o novo endereço de email. 

* No [portal do Azure](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade), selecione o ícone com suas iniciais ou imagem. Em seguida, selecione o menu de contexto ( **...** ). Em seguida, selecione **Minhas Informações de Contato** no menu e insira o novo endereço de email.

![Exemplo de atualizar um endereço de email no Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Cancelar o recebimento de emails de marketing

Para cancelar o recebimento de emails de marketing:

1. Acesse o [formulário de solicitação](https://account.microsoft.com/profile/permissions-link-request) para enviar uma solicitação usando o endereço de email do seu perfil. Você receberá um link por email para atualizar suas preferências.
1. Selecione o link para abrir a página **Gerenciar permissões de comunicação**. Esta página mostra os tipos de comunicações de marketing que o endereço de email optou por receber. Desmarque as seleções cujo recebimento você deseja recusar e selecione **Salvar**.  
    ![Exemplo da página para gerenciar permissões de comunicação](./media/change-azure-account-profile/manage-communication-permissions.png)

Quando você cancela o recebimento de comunicações de marketing, você ainda recebe notificações de serviço, com base em sua conta.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Atualizar o endereço de email com o qual você entra

Não é possível atualizar o endereço de email que você usa para acessar sua conta. No entanto, se você tiver uma conta de cobrança para um MOSP, poderá se inscrever para outra conta usando o novo endereço de email e transferir a propriedade de suas assinaturas para a próxima conta. Para uma conta de cobrança do MCA, [você pode conceder as novas permissões de endereço de email em sua conta](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="update-your-credit-card"></a>Atualizar seu cartão de crédito

Para saber como atualizar seu cartão de crédito, confira [Alterar o cartão de crédito usado para pagar por uma assinatura do Azure](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Atualize suas informações de país ou região

A alteração do país ou região de uma conta existente não é um procedimento compatível. No entanto, você pode criar uma conta em um país ou região diferente e contatar o Suporte do Azure para transferir sua assinatura para a nova conta.

## <a name="change-the-subscription-name"></a>Alterar o nome da assinatura

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Assinatura** no painel esquerdo e, em seguida, selecione a assinatura que você deseja renomear.
1. Selecione **Visão Geral** e, em seguida, selecione **Renomear** na barra de comandos.  
    ![Exemplo de renomeação da assinatura do Azure](./media/change-azure-account-profile/rename-sub.png)
1. Depois de alterar o nome, selecione **Salvar**.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Exibir suas contas de cobrança](view-all-accounts.md)
