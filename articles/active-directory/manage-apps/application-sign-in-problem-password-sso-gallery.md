---
title: Problema sacaneando o aplicativo de galeria Azure AD configurado para SSO | Microsoft Docs
description: Como solucionar problemas com um aplicativo azure AD Gallery configurado para o login único de senha.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381299"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemas de login com um aplicativo de galeria AD do Azure configurado para SSO

O Painel de Acesso é um portal baseado na Web. Ele permite que os usuários que têm o Azure Active Directory (Azure AD) trabalhem ou contas escolares acessem aplicativos baseados na nuvem para os seus aplicativos. Os usuários que possuem edições Azure AD também podem usar recursos de autoatendimento em grupo e gerenciamento de aplicativos através do Painel de Acesso.

O Painel de Acesso é separado do portal Azure. Os usuários não precisam de uma assinatura do Azure para usar o Painel de Acesso.

Para usar o SSO (Single Logon, assinatura de assinatura) baseado em senha no Painel de Acesso, a extensão do Painel de Acesso deve ser instalada no seu navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha.

## <a name="browser-requirements-for-access-panel"></a>Requisitos do navegador para painel de acesso

O Painel de Acesso requer um navegador que suporte JavaScript e tenha CSS ativado.

Os seguintes navegadores suportam SSO baseado em senha:

- Internet Explorer 8, 9, 10 e 11 no Windows 7 ou posterior

- Chrome no Windows 7 ou posterior ou no MacOS X ou posterior

- Firefox 26.0 ou posterior no Windows XP SP2 ou posterior ou no Mac OS X 10.6 ou posterior

>[!NOTE]
>A extensão SSO baseada em senha fica disponível para o Microsoft Edge no Windows 10 quando o suporte para extensões do navegador foi adicionado ao Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do Access Panel Browser

Siga estas etapas:

1. [Abra o Painel de Acesso](https://myapps.microsoft.com) em um navegador suportado e faça login como usuário no Azure AD.

2. Selecione um aplicativo habilitado para Senha SSO no Painel de Acesso.

3. Quando for solicitado, selecione **Instalar agora**.

4. Você será direcionado para um link de download com base no seu navegador. Selecione **Adicionar** para instalar a extensão do navegador.

5. Se você for solicitado, selecione **Ativar** ou **Permitir**.

6. Após a instalação, reinicie seu navegador.

7.  Faça login no Painel de Acesso e veja se você pode iniciar seus aplicativos habilitados para Senha SSO.

Você também pode baixar diretamente as extensões para Chrome e Firefox através desses links:

-   [Extensão do Painel de Acesso Cromo](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Você pode configurar uma diretiva de grupo que permite instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas de seus usuários.

Estes são os pré-requisitos:

-   [Os Serviços ativos de domínio do diretório](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) devem ser configurados e as máquinas de seus usuários devem ser unidas ao seu domínio.

-   Você tem permissão "Editar configurações" para editar o GPO (Group Policy Object, objeto de política de grupo). Por padrão, os membros dos grupos de segurança a seguir têm esta permissão: Administradores de Domínio, Administradores de Empresa e Proprietários Criadores de Política de Grupo. [Saiba mais](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Para configurar a diretiva de grupo e implantá-la aos usuários, consulte [Como implantar a extensão do Painel de Acesso para o Internet Explorer usando a diretiva de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Painel de acesso para solucionar problemas no Internet Explorer

Para acessar uma ferramenta de diagnóstico e instruções para configurar a extensão, consulte [Solucionar problemas na extensão do Painel de Acesso para o Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configure senha SSO para um aplicativo de galeria AD do Azure

Para configurar um aplicativo da galeria Ad do Azure, você precisa fazer essas coisas:

-   Adicione o aplicativo da galeria Azure AD
-   [Configure o aplicativo para senha de login único](#configure-the-app-for-password-sso)
-   [Atribuir usuários ao aplicativo](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Adicione o aplicativo da galeria Azure AD

Siga estas etapas:

1. Abra o [portal Azure](https://portal.azure.com) e faça login como administrador global ou co-administrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Adicionar** no canto superior direito do painel **Aplicações Corporativas.**

6. Na **seção Adicionar na** seção galeria, digite o nome do aplicativo na **caixa Enter a name.**

7. Selecione o aplicativo que deseja configurar para SSO.

8. *Opcional:* Antes de adicionar o aplicativo, você pode alterar seu nome na caixa **Nome.**

9. Clique em **Adicionar** para adicionar o aplicativo.

   Após um breve atraso, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-app-for-password-sso"></a>Configure o aplicativo para senha SSO

Siga estas etapas:

1. Abra o [portal Azure](https://portal.azure.com/) e faça login como administrador global ou co-administrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** na parte superior da **Lista todos os aplicativos**. Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para SSO.

7. Após as cargas do aplicativo, selecione **Single sign-on** no painel do lado esquerdo do aplicativo.

8. Selecione o modo **de login baseado em senha.**

9. Atribuir usuários ao aplicativo.

10. Você também pode fornecer credenciais para os usuários. (Caso contrário, os usuários serão solicitados a inserir credenciais na inicialização do aplicativo.) Para isso, selecione as linhas dos usuários. Em seguida, **selecione Atualizar credenciais** e digite seus nomes de usuário e senhas.

### <a name="assign-users-to-the-app"></a>Atribuir usuários ao aplicativo

Para atribuir usuários diretamente a um aplicativo, siga estas etapas:

1. Abra o [portal Azure](https://portal.azure.com/) e faça login como administrador global.

2. Selecione **Todos os serviços** na dor de navegação no lado esquerdo para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** na parte superior da **Lista todos os aplicativos**. Defina a opção **Mostrar** como "Todas as aplicações".

6. Na lista, selecione o aplicativo ao que deseja atribuir a um usuário.

7. Após as cargas do aplicativo, selecione **Usuários e Grupos** no painel de navegação do aplicativo no lado esquerdo.

8. Selecione **Adicionar** no topo da lista **Usuários e Grupos** para abrir o painel Adicionar **atribuição.**

9. Selecione **Usuários e grupos** no painel Adicionar **atribuição.**

10. Na **caixa pesquisar por nome ou endereço de e-mail,** digite o nome completo ou endereço de e-mail do usuário que deseja atribuir.

11. Passar o passar por cima do usuário da lista. Selecione a caixa de seleção ao lado da foto ou logotipo do perfil do usuário para adicionar esse usuário à lista **Selecionada.**

12. *Opcional:* Para adicionar outro usuário, digite outro nome ou endereço de e-mail na caixa **de nome ou endereço de e-mail da Pesquisa** e selecione a caixa de seleção para adicionar esse usuário à lista **Selecionada.**

13. Quando terminar de selecionar usuários, clique em **Selecionar** para adicioná-los à lista de usuários e grupos atribuídos ao aplicativo.

14. *Opcional:* Clique em **Selecionar função** no painel **Adicionar atribuição** para selecionar uma função para atribuir aos usuários selecionados.

15. Selecione **Atribuir** para atribuir o aplicativo aos usuários selecionados.

    Após um breve atraso, os usuários poderão acessar esses aplicativos do Painel de Acesso.

## <a name="request-support"></a>Solicitar suporte 
Se você receber uma mensagem de erro ao configurar o SSO e atribuir usuários, abra um ticket de suporte. Inclua o máximo de informações possíveis:

-   ID de erro de correlação
-   UPN (endereço de email de usuário)
-   TenantID
-   Tipo de navegador
-   Fuso horário e horário/horário quando ocorreu o erro
-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
