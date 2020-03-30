---
title: Adicionar usuários de colaboração B2B como um trabalhador de informações - Azure AD
description: A colaboração B2B permite que operadores de informações e proprietários de aplicativos adicionem usuários convidados ao Azure AD para acesso | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565410"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Como usuários na organização podem convidar usuários convidados para um aplicativo

Depois que um usuário convidado tiver sido adicionado ao diretório no Azure AD, o proprietário do aplicativo poderá enviar ao usuário convidado um link direto para o aplicativo que deseja compartilhar. Os administradores do Azure AD também podem configurar o gerenciamento de autoatendimento para aplicativos baseados em SAML ou galeria no locatário do Azure AD. Dessa forma, os proprietários de aplicativos podem gerenciar seus próprios usuários convidados, mesmo que os usuários convidados ainda não tenham sido adicionados ao diretório. Quando um aplicativo é configurado para autoatendimento, o proprietário do aplicativo usa o Painel de Acesso para convidar um usuário convidado para um aplicativo ou adicionar um usuário convidado a um grupo que tenha acesso ao aplicativo. O gerenciamento de aplicativos de autoatendimento para aplicativos baseados em galeria e SAML requer alguma configuração inicial por um administrador. A seguir está um resumo das etapas de configuração (para instruções mais [detalhadas, consulte pré-requisitos](#prerequisites) mais tarde nesta página):

 - Habilitar gerenciamento de grupos de autoatendimento para o locatário
 - Criar um grupo para atribuir ao aplicativo e tornar o usuário um proprietário
 - Configurar o aplicativo para autoatendimento e atribuir o grupo ao aplicativo

> [!NOTE]
> Este artigo descreve como configurar o gerenciamento de autoatendimento para aplicativos baseados em SAML e galeria que você adicionou ao locatário do Azure AD. Também é possível [configurar grupos do Office 365 de autoatendimento](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para que os usuários possam gerenciar o acesso aos próprios grupos do Office 365. Para obter mais maneiras de compartilhar arquivos e aplicativos do Office com usuários convidados, consulte [Acesso para convidado em grupos do Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e [Compartilhar arquivos ou pastas do SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Convidar um usuário convidado a um aplicativo do Painel de Acesso

Depois que um aplicativo é configurado para autoatendimento, os proprietários de aplicativos podem usar o próprio Painel de Acesso para convidar um usuário convidado para o aplicativo que deseja compartilhar. O usuário convidado não precisa necessariamente ser adicionado ao Azure AD com antecedência. 

1. Abra o Painel de Acesso, indo para `https://myapps.microsoft.com`.
2. Aponte para o aplicativo, selecione as reticências (**...**) e, em seguida, selecione **Gerenciar aplicativo**.
 
   ![Captura de tela mostrando o submenu gerenciar aplicativo para o aplicativo Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. No topo da lista de **+** usuários, selecione .
   
   ![Captura de tela mostrando o símbolo de adição de membros ao aplicativo](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Na caixa de pesquisa **Adicionar membros**, insira o endereço de email do usuário convidado. Opcionalmente, inclua uma mensagem de boas-vindas.
   
   ![Captura de tela mostrando a janela Adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
5. Selecione **Adicionar** para enviar um convite ao usuário convidado. Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Convidar alguém para ingressar em um grupo que tenha acesso ao aplicativo
Depois que um aplicativo é configurado para autoatendimento, os proprietários de aplicativos podem convidar usuários convidados para os grupos que gerenciam e que tenham acesso aos aplicativos que desejam compartilhar. Os usuários convidados não precisam existir no diretório. O proprietário do aplicativo segue essas etapas para convidar um usuário convidado para o grupo, de modo que ele possa acessar o aplicativo.

1. Certifique-se de que você é um proprietário do grupo de autoatendimento com acesso ao aplicativo que deseja compartilhar.
2. Abra o Painel de Acesso, indo para `https://myapps.microsoft.com`.
3. Selecione o aplicativo de **Grupos**.
   
   ![Captura de tela mostrando o aplicativo Grupos no Painel de Acesso](media/add-users-iw/access-panel-groups.png)
   
4. Em **Grupos que possuo**, selecione o grupo que tem acesso ao aplicativo que você deseja compartilhar.
   
   ![Captura de tela mostrando onde selecionar um grupo os grupos que possuo](media/add-users-iw/access-panel-groups-i-own.png)
   
5. No topo da lista de **+** membros do grupo, selecione .
   
   ![Captura de tela mostrando o símbolo de adição de membros ao grupo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Na caixa de pesquisa **Adicionar membros**, insira o endereço de email do usuário convidado. Opcionalmente, inclua uma mensagem de boas-vindas.
   
   ![Captura de tela mostrando a janela Adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
7. Selecione **Adicionar** para enviar automaticamente o convite ao usuário convidado. Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.


## <a name="prerequisites"></a>Pré-requisitos

O gerenciamento de aplicativos de autoatendimento exige alguma configuração inicial por um administrador global e um administrador do Azure AD. Como parte dessa configuração, você configurará o aplicativo para autoatendimento e atribuirá um grupo ao aplicativo que o proprietário do aplicativo poderá gerenciar. Também é possível configurar o grupo para permitir que alguém solicite a associação, mas isso exige a aprovação de um proprietário do grupo. (Saiba mais sobre [gerenciamento de grupos de autoatendimento](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Não é possível adicionar usuários convidados a um grupo dinâmico ou a um grupo que esteja sincronizado com o Active Directory local.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Habilitar gerenciamento de grupos de autoatendimento para o locatário
1. Faça login no [portal Azure](https://portal.azure.com) como administrador global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **Grupos**.
4. Em **Configurações**, selecione **Geral**.
5. Em **Gerenciamento de Grupos de Autoatendimento**, próximo a **Proprietários podem gerenciar solicitações de associação ao grupo no Painel de Acesso**, selecione **Sim**.
6. Selecione **Salvar**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Criar um grupo para atribuir ao aplicativo e tornar o usuário um proprietário
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador do Azure AD ou Administrador Global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **Grupos**.
4. Selecione **Novo grupo**.
5. Em **Tipo de grupo**, selecione **Segurança**.
6. Digite um **Nome de grupo** e a **Descrição do grupo**.
7. Em **Tipo de associação**, selecione **Atribuído**.
8. Selecione **Criar** e feche a página **Grupo**.
9. Na página **Grupos - Todos os grupos**, abra o grupo. 
10. Em **Gerenciar**, selecione **Proprietários** > **Adicionar proprietários**. Pesquise o usuário que deve gerenciar o acesso ao aplicativo. Selecione o usuário e, em seguida, clique em **Selecionar**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configurar o aplicativo para autoatendimento e atribuir o grupo ao aplicativo
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador do Azure AD ou Administrador Global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Aplicativos empresariais** > **Todos aplicativos**.
4. Na lista de aplicativos, localize e abra o aplicativo.
5. Em **Gerenciar**, selecione **Logon único**, e configure o aplicativo para logon único. (Para detalhes, consulte [como gerenciar logon único para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).)
6. Em **Gerenciar**, selecione **Autoatendimento** e configure o acesso ao aplicativo de autoatendimento. (Para detalhes, consulte [como usar o acesso ao aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Para a configuração **Para qual grupo os usuários atribuídos devem ser adicionados?**, selecione o grupo criado na seção anterior.
7. Em **Gerenciar**, selecione **Usuários e Grupos** e verifique se o grupo de autoatendimento que você criou aparece na lista.
8. Para adicionar o aplicativo ao Painel de Acesso do proprietário do grupo, selecione Adicionar**usuários e grupos**de **usuários** > . Pesquise o proprietário do grupo e selecione o usuário, clique em **Selecionar** e, em seguida, clique em **Atribuir** para adicionar o usuário ao aplicativo.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração Azure AD B2B?](what-is-b2b.md)
- [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Licenciamento da colaboração B2B do Azure AD](licensing-guidance.md)
