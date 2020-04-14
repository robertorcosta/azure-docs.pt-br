---
title: Migração de conta do Cloud Partner Portal para o Partner Center - mercado comercial para o Azure
description: Como migrar sua conta de CPP para Partner Center. - mercado comercial para Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e17a76d5a017400287644ad2da46caa5b6636654
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262291"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Migração de conta do Portal do Cloud Partner para o Partner Center

Se você tiver uma conta CPP (Cloud Partner Portal) existente, as configurações da sua conta precisam ser migradas para a Partner Center.

## <a name="account-migration-process"></a>Processo de migração de contas

Se você é um usuário com a função Proprietário no CPP para uma determinada conta, um banner amarelo será mostrado na página do Perfil do Editor. Você pode pertencer a um dos dois seguintes casos:

- Sua conta já foi migrada e você está pronto para gerenciar suas Configurações de Conta no Partner Center.
- Sua conta não foi migrada para o Partner Center e você precisa tomar mais medidas.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Sua conta foi migrada para o Partner Center

Para todas as contas que concluíram a migração do CPP para o Partner Center, o gerenciamento de contas acontecerá no Partner Center. Alterações como adição/exclusão do usuário serão sincronizadas de volta ao CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Você ainda não migrou sua conta para o Partner Center

Clique no banner para iniciar seu processo de migração da conta. Espera-se que você insira os seguintes itens:

1. Endereço de e-mail de trabalho: <br> <br> Na maioria dos casos, faça login com o endereço de e-mail que você usa para entrar no CPP. Em certos casos, um endereço de e-mail diferente deve ser usado:

    * Conta Microsoft: Se a conta CPP for uma conta da Microsoft, digite um e-mail de trabalho válido associado ao inquilino para quem o ID da Microsoft Partner Network (MPN) está registrado. Para obter mais informações, consulte [Inscreva-se no Microsoft Partner Network Program](#sign-up-for-microsoft-partner-network-program).

    * Incompatibilidade de inquilino: Se o seu endereço de e-mail de trabalho não pertencer ao inquilino associado ao Microsoft Partner Network ID presente em sua conta CPP, então você verá um erro. Para superar esse erro, digite um endereço de e-mail associado ao inquilino. Uma mensagem de erro fornecerá o nome do inquilino.

2. Inscreva-se no programa Microsoft Partner Network

    Se sua conta CPP não tiver um ID da Rede de Parceiros Microsoft ou tiver uma inválida, você precisará se inscrever no programa Microsoft Partner Network como parte do processo de ativação.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Inscreva-se no programa Microsoft Partner Network

As empresas que desejam fazer parceria com a Microsoft devem se juntar à Microsoft Partner Network (MPN) e obter um ID MPN. Se você já é um membro da Rede de Parceiros Microsoft e tem um ID MPN, mantenha as informações à mão, pois você precisará durante o processo de ativação da conta.  

Se você não é um membro da Microsoft Partner Network, você pode [se juntar aqui](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) para obter um ID MPN. Anote seu ID MPN, pois você precisará inseri-lo durante o processo de ativação da conta.

Para saber mais sobre a Microsoft Partner Network, consulte [Junte-se à Rede de Parceiros da Microsoft](https://partner.microsoft.com/en-US/membership) no site do parceiro. Para saber mais sobre os benefícios do ISV na Rede de Parceiros Microsoft, consulte o [ISV Resource Hub](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Move Dynamics 365 e PowerApps oferecem ao Partner Center

Para agilizar a gestão de contas e ofertas para as operações Dynamics 365 Customer Engagement, PowerApps e Dynamics 365, as ofertas foram transferidas para o [Partner Center](https://partner.microsoft.com/). A mudança garante que o mesmo conteúdo esteja disponível para catálogos públicos e vendedores.

Para obter informações específicas sobre o que precisa ser feito até **15 de outubro de 2019** para as ofertas de Engajamento do Cliente Dynamics 365, PowerApps e Dynamics 365, siga as instruções abaixo.

> [!NOTE]
> Isso não se aplica às ofertas da Dynamics 365 Business Central.  

1. Se sua conta de adesão ao MPN foi originalmente criada no Partner Membership Center (PMC), entre na [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) para confirmar se sua conta foi migrada. Se você vir uma tela de perfil com seu ID MPN, você está pronto para continuar. Caso assim, você precisa iniciar a migração da sua conta seguindo as instruções no [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Se você precisar de ajuda, visite [o suporte](https://partner.microsoft.com/support?issueid=100-0077).
2. Vá para a página de visão geral do [Mercado Comercial no Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Se você vir "Commercial Marketplace" no painel de navegação esquerdo, você está inscrito e deve continuar até o próximo passo. Se não, [inscreva-se no mercado comercial](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) agora.
3. Confirme se suas ofertas estão no AppSource [pesquisando por suas ofertas](https://appsource.microsoft.com/). Se suas ofertas já estiverem no AppSource, continue até o próximo passo. Para qualquer oferta que não seja no AppSource, crie uma [nova oferta de engajamento do cliente Dynamics 365](create-new-customer-engagement-offer.md) ou uma nova oferta de [operações Dynamics 365](create-new-operations-offer.md).
4. Na [página Contratos](https://partner.microsoft.com/dashboard/account/agreements)do Partner Center, certifique-se de ter revisado e aceito o **Adendo ISV de Aplicações de Negócios**.
5. Nas configurações da Conta do Partner Center, certifique-se de que suas informações de cobrança estão [completas.](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)
6. Envie cada oferta nova e existente para certificação e publicação, mesmo que suas ofertas tenham sido previamente certificadas.
    * Complete as telas de informações, incluindo o fornecimento de seu aplicativo para certificação, bem como informações de marketing. Selecione **Enviar** (canto superior direito da tela) até 15 de outubro de **2019**. Essas etapas devem ser concluídas para evitar impactona disponibilidade da oferta.
    * Se elegível, você pode solicitar para participar do nível premium durante este processo.
    * A certificação ou recertificação exige que seu aplicativo suporte a versão mais recente da nossa Plataforma de Aplicativos de Negócios.
    * Uma vez que seu aplicativo tenha sido aprovado, você receberá um e-mail para retornar à oferta e selecionará "ir ao vivo" para permitir que a oferta entre no ar no Microsoft AppSource.

## <a name="additional-resources"></a>Recursos adicionais

Junte-se ao call semanal da [comunidade ISV Dynamics](https://aka.ms/DynamicsISV-CommunityCall) para suporte e atualizações.

Se você precisar de ajuda para publicar, certificar ou gerenciar suas ofertas de marketplace, [envie um bilhete de suporte](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-steps"></a>Próximas etapas

- [Gerencie sua conta de mercado comercial no Partner Center](./manage-account.md)
