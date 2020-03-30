---
title: SAML único login para aplicativos no local com proxy do aplicativo Azure AD
description: Saiba como fornecer o login único para aplicativos no local que são protegidos com autenticação SAML. Forneça acesso remoto a aplicativos locais com proxy de aplicativo.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481340"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML single sign-on para aplicativos no local com proxy de aplicativo

Você pode fornecer logon único (SSO) para aplicativos locais que são protegidos com autenticação SAML e fornecer acesso remoto a esses aplicativos através do Proxy do Aplicativo. Com o login único do SAML, o Azure Active Directory (Azure AD) autentica-se no aplicativo usando a conta Azure AD do usuário. O Azure AD comunica as informações do logon para o aplicativo por meio de um protocolo de conexão. Você também pode mapear usuários para funções específicas de aplicativos com base em regras definidas em suas reivindicações SAML. Ao ativar o Proxy de aplicativos, além do SAML SSO, seus usuários terão acesso externo ao aplicativo e uma experiência SSO perfeita.

Os aplicativos devem ser capazes de consumir tokens SAML emitidos pelo **Azure Active Directory**. Essa configuração não se aplica a aplicativos usando um provedor de identidade local. Para esses cenários, recomendamos a revisão de [recursos para migração de aplicativos para o Azure AD](migration-resources.md).

O SAML SSO com proxy de aplicativo também funciona com o recurso de criptografia de token SAML. Para obter mais informações, consulte Configurar a criptografia de [token AD SAML do Azure](howto-saml-token-encryption.md).

Os diagramas de protocolo abaixo descrevem a seqüência de login único para um fluxo iniciado pelo provedor de serviços (iniciado por SP) e um fluxo iniciado pelo provedor de identidade (iniciado por IdP). O Proxy de aplicativo funciona com o SAML SSO, cacheando a solicitação e resposta SAML para e a partir do aplicativo no local.

  ![Fluxo de SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Fluxo de SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Crie um aplicativo e configure o SAmL SSO

1. No portal Azure, selecione **o Azure Active Directory > aplicativos Enterprise** e selecione Novo **aplicativo**.

2. Digite o nome de exibição do seu novo aplicativo, **selecione Integrar qualquer outro aplicativo que você não encontrar na galeria**e, em seguida, selecione **Criar**.

3. Na página **Visão Geral** do aplicativo, selecione **Único login**.

4. Selecione **SAML** como o método de tipo único de inscrição.

5. Primeiro configure o SAmL SSO para trabalhar enquanto estiver na rede corporativa. Na **página Configurar único de login com SAML,** vá para o título **de configuração saml básica** e selecione seu ícone **Editar** (um lápis). Siga as etapas na [configuração SAML básica](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) para configurar a autenticação baseada em SAML para o aplicativo.

6. Adicione pelo menos um usuário ao aplicativo e certifique-se de que a conta de teste tenha acesso ao aplicativo. Enquanto estiver conectado à rede corporativa, use a conta de teste para ver se você tem um único login no aplicativo. 

   > [!NOTE]
   > Depois de configurar o Proxy de aplicativo, você voltará e atualizará a **URL de resposta saml**.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publique o aplicativo on-premises com proxy de aplicativo

Antes de fornecer SSO para aplicativos locais, você precisa ativar o Proxy do aplicativo e instalar um conector. Veja o tutorial [Adicione um aplicativo local para acesso remoto através do App Proxy no Azure AD](application-proxy-add-on-premises-application.md) para saber como preparar seu ambiente no local, instalar e registrar um conector e testar o conector. Em seguida, siga estas etapas para publicar seu novo aplicativo com o Proxy do aplicativo. Para outras configurações não mencionadas abaixo, consulte [adicionar um aplicativo on-premises à seção Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) no tutorial.

1. Com o aplicativo ainda aberto no portal Azure, selecione **Proxy de aplicativo**. Forneça a **URL interna** para o aplicativo. Se você estiver usando um domínio personalizado, você também precisa carregar o certificado TLS/SSL para o seu aplicativo. 
   > [!NOTE]
   > Como prática recomendada, use domínios personalizados sempre que possível para uma experiência de usuário otimizada. Saiba mais sobre [como trabalhar com domínios personalizados no Proxy de aplicativo Azure AD](application-proxy-configure-custom-domain.md).

2. Selecione **o Diretório Ativo do Azure** como o método de **pré-autenticação** para o seu aplicativo.

3. Copie a **URL externa** para o aplicativo. Você precisará desta URL para completar a configuração SAML.

4. Usando a conta de teste, tente abrir o aplicativo com a **URL externa** para validar que o Proxy do aplicativo está configurado corretamente. Se houver problemas, consulte [Problemas de proxy de aplicativo e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Atualize a configuração SAML

1. Com o aplicativo ainda aberto no portal Azure, selecione **Único login**. 

2. Na **página Configurar único de login com SAML,** vá para o título **de configuração saml básica** e selecione seu ícone **Editar** (um lápis). Certifique-se de que a **URL externa** configurada no Proxy do aplicativo esteja preenchida nos campos **Identificador,** URL de **resposta**e URL **de logout.** Esses URLs são necessários para que o Proxy do aplicativo funcione corretamente. 

3. Edite a **URL de resposta** configurada anteriormente para que seu domínio seja acessível pelo Proxy do aplicativo. Por exemplo, se a `https://contosotravel-f128.msappproxy.net` URL **externa** for e a URL de **resposta** original for, `https://contosotravel.com/acs`você precisará atualizar a URL de **resposta** original para `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Digite os dados básicos de configuração do SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Selecione a caixa de seleção ao lado da **URL de resposta** atualizada para marcá-la como padrão.

   * Se a **URL de resposta** necessária já estiver listada, marque esta URL de **resposta** como padrão e exclua a URL **de resposta**configurada anteriormente.

   * Para um fluxo iniciado por SP, certifique-se de que o aplicativo back-end especifique a URL de **resposta** ou url do Serviço do Consumidor de Afirmação correta para receber o token de autenticação.

    > [!NOTE]
    > Se o aplicativo back-end espera que a **URL de resposta** seja a URL interna, você precisará usar domínios personalizados para ter URLS internos e [externos correspondentes](application-proxy-configure-custom-domain.md) ou instalar a extensão de login seguro Meus Aplicativos nos dispositivos dos usuários. Essa extensão será redirecionado automaticamente para o Serviço proxy de aplicativo apropriado. Para instalar a extensão, consulte [Meus aplicativos garantem extensão de login](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testar seu aplicativo

Depois de concluir todas essas etapas, seu aplicativo estará pronto para execução. Para testar o aplicativo:

1. Abra um navegador e navegue até a **URL externa** que você criou quando publicou o aplicativo. 
1. Entre com a conta de teste que você atribuiu ao aplicativo. Você deve ser capaz de carregar o aplicativo e ter SSO no aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Como o Proxy de Aplicativo do Azure AD fornece logon único?](application-proxy-single-sign-on.md)
- [Solucionar problemas de Proxy de Aplicativo](application-proxy-troubleshoot.md)
