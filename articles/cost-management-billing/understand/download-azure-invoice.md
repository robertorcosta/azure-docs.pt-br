---
title: Exibir e baixar sua fatura do Azure
description: Descreve como exibir e baixar sua fatura do Azure.
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: banders
ms.openlocfilehash: 4e77b167f00e2cfa3838439143c6074bd4122976
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191369"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Exibir e baixar sua fatura do Microsoft Azure

Você pode baixar sua fatura no [portal do Azure](https://portal.azure.com/) ou recebê-la por email. Se você for um cliente do Azure com um cliente EA (Contrato Enterprise), não poderá baixar a fatura da sua organização. Em vez disso, as faturas são enviadas para a pessoa configurada para receber as faturas do registro.

## <a name="when-invoices-are-generated"></a>Quando as faturas são geradas

Uma fatura é gerada com base em seu tipo de conta de cobrança. As faturas são criadas para contas de cobrança do MOSP (Programa do Microsoft Online Services), MCA (Contrato de Cliente da Microsoft) e MPA (Contrato de Parceiro da Microsoft). As faturas também são geradas para contas de cobrança EA (Contrato Enterprise). No entanto, as faturas para contas de cobrança do EA não são mostradas no portal do Azure.

Para saber mais sobre contas de cobrança e identificar seu tipo conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](../manage/view-all-accounts.md).

## <a name="invoices-for-mosp-billing-accounts"></a>Faturas para contas de cobrança do MOSP

Uma conta de cobrança do MOSP é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Clientes em regiões selecionadas, que se inscrevem pelo site do Azure em uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), podem ter uma conta de cobrança para um MCA.

Se você não tiver certeza de qual é seu tipo de conta de cobrança, confira [Verificar seu tipo de conta de cobrança](../manage/view-all-accounts.md#check-the-type-of-your-account) antes de seguir as instruções neste artigo. 

Uma conta de cobrança do MOSP pode ter as seguintes faturas:

**Encargos de serviço do Azure** – uma fatura é gerada para cada assinatura do Azure que contém recursos do Azure usados pela assinatura. A fatura contém encargos para um período de cobrança. O período de cobrança é determinado pelo dia do mês em que a assinatura é criada.

Por exemplo, John cria *Azure sub 01* em 5 de março e *Azure sub 02* em 10 de março. A fatura de *Azure sub 01* terá encargos desde o quinto dia de um mês até o quarto dia do mês seguinte. A fatura de *Azure sub 02* terá encargos desde o décimo dia de um mês até o nono dia do mês seguinte. As faturas de todas as assinaturas do Azure normalmente são geradas no dia do mês em que a conta foi criada, mas isso pode ocorrer até dois dias depois. Nesse exemplo, se John tiver criado a conta no dia 2 de fevereiro, as faturas de *Azure sub 01* e de *Azure sub 02* normalmente serão geradas no segundo dia de cada mês, mas isso poderia ocorrer até dois dias depois.

**Azure Marketplace, reservas e VMs spot** – uma fatura é gerada para reservas, produtos de marketplace e VMs spot adquiridas usando uma assinatura. A fatura mostra os respectivos encargos do mês anterior. Por exemplo, John comprou uma reserva em 1º de março e outra reserva em 30 de março. Será gerada uma única fatura para as duas reservas feitas em abril. A fatura do Azure Marketplace, das reservas e das VMs spot sempre é gerada perto do nono dia do mês.

Se você pagar pelo Azure com um cartão de crédito e comprar uma reserva, o Azure vai gerar uma fatura imediata. No entanto, quando cobrado por uma fatura, a cobrança da reserva ocorrerá na fatura do próximo mês.

**Plano de suporte do Azure** – uma fatura é gerada a cada mês para a assinatura do seu plano de suporte. A primeira fatura é gerada no dia de compra ou até dois dias depois. As faturas do plano de suporte subsequentes normalmente são geradas no mesmo dia do mês que a conta foi criada, mas poderiam ser geradas até dois dias depois.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Baixe sua fatura de assinatura do Azure para o MOSP

Uma fatura só é gerada para uma assinatura pertencente a uma conta de cobrança para um MOSP. [Verifique seu acesso a uma conta MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Você deve ter uma função de administrador de conta para uma assinatura para baixar a fatura dela. Os usuários com funções proprietário, colaborador ou leitor poderão baixar a fatura se o administrador da conta tiver lhes concedido permissão. Para obter mais informações, confira [Permitir que os usuários baixem faturas](../manage/manage-billing-access.md#opt-in).

1. Selecione sua assinatura na [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.
1. Selecione **Faturas** na seção de cobrança.  
    ![Captura de tela que mostra um usuário selecionando a opção faturas para uma assinatura](./media/download-azure-invoice/select-subscription-invoice.png)
1. Selecione **Baixar** para baixar uma versão em PDF de sua fatura e, em seguida, selecione **Baixar** embaixo da seção da fatura.  
    ![Captura de tela mostrando os períodos de cobrança, a opção de download e os encargos totais para cada período de cobrança](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. Você também pode baixar um detalhamento diário de quantidades e encargos consumidos selecionando **Baixar** na seção de detalhes de uso. Pode levar alguns minutos para preparar o arquivo CSV.  
    ![Captura de tela que mostra a página Baixar fatura e uso](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Para saber mais sobre a fatura, confira [Entenda sua fatura do Microsoft Azure](../understand/review-individual-bill.md). Para obter ajuda com o gerenciamento de custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](../manage/getting-started.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Baixar a fatura do plano de suporte do MOSP

Uma fatura só é gerada para uma assinatura do plano de suporte pertencente a uma conta de cobrança para do MOSP. [Verifique seu acesso a uma conta MOSP](../manage/view-all-accounts.md#check-the-type-of-your-account).

Você deve ter uma função de administrador de conta na assinatura do plano de suporte para baixar a fatura dela.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/download-azure-invoice/search-cmb.png)
1. Selecione **Faturas** no lado esquerdo.
1. Selecione sua assinatura de plano de suporte o botão **Baixar**.  
    [![Captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. Selecione **Baixar** para baixar uma versão em PDF de sua fatura.  
    ![Captura de tela mostrando os períodos de cobrança, a opção de download e os encargos totais para cada período de cobrança](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-the-your-subscription-invoice"></a>Permitir que outras pessoas baixem uma fatura de assinatura

Para baixar uma fatura:

1.  Entre no [portal do Azure](https://portal.azure.com) como um administrador da conta para a assinatura.

2.  Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/download-azure-invoice/search-cmb.png)

3.  Selecione **Faturas** no lado esquerdo.

4.  Selecione sua assinatura do Azure e clique em **Permitir que outras pessoas baixem a fatura**.

    [![Captura de tela que mostra a seleção do acesso à fatura](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Selecione **Ativado** e, em seguida, **Salvar** na parte superior da página.  
    ![Captura de tela que mostra a seleção de ativado para o acesso à fatura](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Receber a fatura da assinatura do MOSP no email

Você deve ter uma função de administrador da conta em uma assinatura ou em um plano de suporte para aceitar receber a fatura por email. As faturas por email estão disponíveis apenas para assinaturas e planos de suporte, não para reservas ou compras do Azure Marketplace. Após aceitar, você poderá adicionar destinatários, que receberão a fatura por email também.

1.  Entre no [portal do Azure](https://portal.azure.com).
2.  Pesquise **Gerenciamento de Custos + Cobrança**.  
3.  Selecione **Faturas** no lado esquerdo.
4.  Selecione sua assinatura ou a assinatura do plano de suporte do Azure e, em seguida, selecione **Receber fatura por email**.  
    [![Captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. Clique em **Enviar fatura por email** e aceite os termos.  
    ![Captura de tela que mostra a etapa 2 do fluxo de aceitação](./media/download-azure-invoice/invoicearticlestep02.png)
6. A fatura é enviada para seu email de comunicação preferencial. Selecione **Atualizar perfil** para atualizar o email.  
    ![Captura de tela que mostra a etapa 3 do fluxo de aceitação](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoices"></a>Faturas do plano de suporte e assinatura de compartilhamento

Talvez seja interessante compartilhar as faturas da assinatura e do plano de suporte todos os meses com sua equipe de contabilidade ou enviá-las para um de seus outros endereços de email.

1. Siga as etapas em [Receber as faturas da assinatura e do plano de suporte no email](#get-mosp-subscription-invoice-in-email) e selecione **Configurar os destinatários**.  
    ![Captura de tela que mostra um usuário selecionando configurar destinatários](./media/download-azure-invoice/invoice-article-step03.png)
1. Digite um endereço de email e, em seguida, selecione **Adicionar destinatário**. Você pode adicionar vários endereços de email.  
    ![Captura de tela que mostra um usuário adicionando destinatários](./media/download-azure-invoice/invoice-article-step04.png)
1. Depois de adicionar todos os endereços de email, selecione **Concluído** na parte inferior da tela.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Faturas para as contas de cobrança do MCA e MPA

Uma conta de cobrança do MCA é criada quando sua organização trabalha com um representante da Microsoft para assinar um MCA. Alguns clientes em regiões selecionadas, que se inscrevem pelo site do Azure em uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), podem ter uma conta de cobrança para um MCA também. Para obter mais informações, confira [Introdução à conta de cobrança do MCA](../understand/mca-overview.md).

Uma conta de cobrança do MPA é criada para parceiros CSP (Provedor de Soluções na Nuvem) para gerenciar os clientes deles na nova experiência de comércio. Os parceiros precisam ter pelo menos um cliente com um [plano do Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) para gerenciar as respectivas contas de cobrança no portal do Azure. Para obter mais informações, confira [Introdução à conta de cobrança do MPA](../understand/mpa-overview.md).

Uma fatura mensal é gerada no início do mês para cada perfil de cobrança de sua conta. A fatura contém os encargos respectivos a todas as assinaturas do Azure e a outras compras do mês anterior. Por exemplo, John criou *Azure sub 01* em 5 de março e *Azure sub 02* em 10 de março. Ele comprou a assinatura *Suporte do Azure 01* em 28 de março usando o *Perfil de cobrança 01*. João receberá uma única fatura no início de abril que conterá encargos para as assinaturas e o plano de suporte do Azure.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Baixar uma fatura do perfil de cobrança do MCA ou MPA

É necessário ter uma função de proprietário, colaborador, leitor ou gerenciador de faturas em um perfil de cobrança para baixar a fatura no Azure. Os usuários com uma função proprietário, colaborador ou leitor em uma conta de cobrança podem baixar faturas para todos os perfis de cobrança na conta.

1.  Entre no [portal do Azure](https://portal.azure.com).

2.  Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/download-azure-invoice/search-cmb.png)

3. Selecione **Faturas** no lado esquerdo.

    [![Captura de tela que mostra a página de faturas para uma conta de cobrança do MCA](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. Na tabela de faturas, selecione a fatura que você deseja baixar.

5. Clique no botão **Baixar pdf da fatura** na parte superior da página.

    [![Captura de tela que mostra o download do pdf da fatura](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. Você também pode baixar um detalhamento diário das quantidades consumidas e dos preços estimados clicando em **Baixar uso do Azure**. Pode levar alguns minutos para o arquivo csv ser preparado.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Receber a fatura do seu perfil de cobrança no email

Você deve ter uma função proprietário ou colaborador no perfil de cobrança ou na conta de cobrança para atualizar a preferência de fatura por email. Após aceitar, todos os usuários com funções de proprietário, colaborador, leitor e gerenciador de faturas em um perfil de cobrança receberão as respectivas faturas por email. 

1.  Entre no [portal do Azure](https://portal.azure.com).
1.  Pesquise **Gerenciamento de Custos + Cobrança**.  
1.  Selecione **Faturas** no lado esquerdo e, em seguida, selecione **Enviar fatura por email** na parte superior da página.  
    [![Captura de tela que mostra a página de faturas para uma conta de cobrança do MCA](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Se você tiver vários perfis de cobrança, selecione um perfil de cobrança e, em seguida, selecione **Aceitar**.  
    ![Captura de tela que mostra a página de faturas para uma conta de cobrança do MCA](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Selecione **Atualização**.

2.  Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/download-azure-invoice/search-cmb.png)

3.  Selecione **Faturas** no lado esquerdo e, em seguida, selecione **Enviar fatura por email** na parte superior da página.

    [![Captura de tela que mostra a página de faturas para uma conta de cobrança do MCA](./media/download-azure-invoice/mca-billingprofile-select-emailinvoice.png)](./media/download-azure-invoice/mca-billingprofile-select-emailinvoice-zoomed-in.png)

4.  Se você tiver vários perfis de cobrança, selecione um perfil de cobrança e, em seguida, selecione **Aceitar**.

Permita que outras pessoas tenham acesso para exibir, baixar e pagar faturas atribuindo-lhes a função de gerenciador de faturas para um perfil de cobrança do MCA ou MPA. Se você tiver aceitado receber sua fatura por email, os usuários também receberão as faturas no email.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.  
1. Selecione **Perfis de cobrança** do lado esquerdo. Na lista perfis de cobrança, selecione um perfil de cobrança para o qual você deseja atribuir uma função de gerenciador de faturas.  
   ![Captura de tela que mostra a lista de perfis de cobrança](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Selecione **Controle de Acesso (IAM)** no lado esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.  
    ![Captura de tela que mostra a página de controle de acesso](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. Na lista suspensa Função, selecione **Gerenciador de Faturas**. Insira o endereço de email do usuário ao qual você permitirá o acesso. Selecione **Salvar** para atribuir a função.  
   ![Captura de tela que mostra como adicionar um usuário como um gerenciador de faturas](./media/download-azure-invoice/mca-added-invoice-manager.png)

1. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa no portal para assinatura](./media/download-azure-invoice/search-cmb.png)

1. Selecione **Perfis de cobrança** do lado esquerdo. Na lista perfis de cobrança, selecione um perfil de cobrança para o qual você deseja atribuir uma função de gerenciador de faturas.

   ![Captura de tela que mostra a lista de perfil de cobrança](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)

1. Selecione **Controle de Acesso (IAM)** no lado esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![Captura de tela que mostra a página de controle de acesso](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)

1. Na lista suspensa Função, selecione **Gerenciador de Faturas**. Insira o endereço de email do usuário ao qual você permitirá o acesso. Selecione **Salvar** para atribuir a função.

   [![Captura de tela que mostra a adição de um usuário como um gerenciador de faturas](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   
   
##  <a name="why-you-might-not-see-an-invoice"></a>Por que você pode não ver uma fatura?

<a name="noinvoice"></a>

Pode haver vários motivos pelos quais você não vê uma fatura:

- A fatura ainda não está pronta
    
    - Faz menos de 30 dias que você se inscreveu no Azure. 

    - O Azure cobra você alguns dias após o término do seu período de cobrança. Portanto, uma fatura ainda pode não ter sido gerada.

- Você não tem permissão para exibir faturas. 
    
    - Se você tiver uma conta de cobrança do MCA ou MPA, deverá ter uma função de Proprietário, Colaborador, Leitor ou Gerenciador de faturas em um perfil de cobrança ou uma função de Proprietário, Colaborador ou Leitor na conta de cobrança para exibir faturas. 
    
    - Para outras contas de cobrança, talvez você não veja as faturas se não for o administrador da conta.

- Sua conta não dá suporte a uma fatura.

    - Se você tiver uma conta de cobrança do MOSP (Programa de Assinatura Online da Microsoft) e tiver se inscrito em uma Conta Gratuita do Azure ou em uma assinatura com uma quantidade de crédito mensal, você só receberá uma fatura quando exceder a quantidade de crédito mensal.

    - Se tiver uma conta de cobrança para um MCA (Contrato de Cliente da Microsoft) ou um MPA (Contrato de Parceiro da Microsoft), você sempre receberá uma fatura.

- Você tem acesso à fatura por meio de uma de suas outras contas.

    - Essa situação normalmente acontece quando você clica em um link no email, solicitando que você exiba sua fatura no portal. Você clica no link e vê uma mensagem de erro – `We can't display your invoices. Please try again`. Verifique se você está conectado com o endereço de email que tem permissões para exibir as faturas.

- Você tem acesso à fatura por meio de uma identidade diferente. 

    - Alguns clientes têm duas identidades com o mesmo endereço de email: uma conta corporativa e uma conta Microsoft. Normalmente, apenas uma das identidades tem permissões para exibir faturas. Se os clientes entrarem com a identidade sem permissão, eles não verão as faturas. Verifique se você está usando a identidade correta para entrar.

- Você entrou no locatário incorreto do AAD (Azure Active Directory). 

    - Sua conta de cobrança está associada a um locatário do AAD. Se estiver conectado a um locatário incorreto, você não verá a fatura para assinaturas em sua conta de cobrança. Verifique se você está conectado ao locatário correto do AAD (Azure Active Directory). Se você não estiver conectado ao locatário correto, use o seguinte para alternar o locatário no portal do Azure:

        1. Selecione seu email no canto superior direito da página.

        2. Selecione **Mudar diretório**.

           ![Captura de tela que mostra a seleção do diretório de comutador no portal](./media/download-azure-invoice/select-switch-directory.png)

        3. Selecione um diretório na seção **Todos os diretórios**.

           ![Captura de tela que mostra a seleção de um diretório no portal](./media/download-azure-invoice/select-directory.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a fatura e os encargos, confira:

- [Exibir e baixar o uso e os encargos do Microsoft Azure](download-azure-daily-usage.md)
- [Entenda sua fatura do Microsoft Azure](review-individual-bill.md)
- [Noções básicas sobre os termos em sua fatura do Azure](understand-invoice.md)

Se você tiver um MCA, confira:

- [Noções básicas sobre encargos na fatura do seu perfil de cobrança](review-customer-agreement-bill.md)
- [Noções básicas sobre termos na fatura do seu perfil de cobrança](mca-understand-your-invoice.md)
- [Noções básicas sobre o arquivo de uso e encargos do Azure do seu perfil de cobrança](mca-understand-your-usage.md)