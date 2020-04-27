---
title: Problemas para entrar em um aplicativo usando um DeepLink| Microsoft Docs
description: Como solucionar problemas de acesso a um aplicativo de uma URL de Deeplink usando o Azure AD
services: active-directory
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
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "65825423"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemas para entrar em um aplicativo usando um DeepLink

O Painel de Acesso é um portal baseado na Web que permite a um usuário com uma conta corporativa ou de estudante no Azure AD (Azure Active Directory) exibir e iniciar aplicativos baseados em nuvem aos quais o administrador do Azure AD concedeu acesso. 

Esses aplicativos são configurados em nome do usuário no portal do Azure AD. O aplicativo deve ser configurado corretamente e atribuído ao usuário ou a um grupo no qual o usuário é membro para ver o aplicativo no Painel de Acesso.

DeepLinks ou URLs de acesso do Usuário são links que seus usuários podem usar para acessar seus aplicativos SSO de senha diretamente de suas barras de URLs de navegadores. Ao navegar até esse link, os usuários serão automaticamente conectados ao aplicativo sem ter que ir primeiro ao Painel de Acesso. Esse é o mesmo link que os usuários usam para acessar esses aplicativos a partir do aplicativo de inicialização do Office 365.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Verifique se você está usando um **navegador** que atenda aos requisitos mínimos para o Painel de Acesso.

-   Verifique se o navegador do usuário adicionou a URL do aplicativo ao seus **sites confiáveis**.

-   Verifique se o aplicativo está **configurado** corretamente.

-   Certifique-se de que a conta do usuário está **habilitada** para entradas.

-   Verifique se a conta do usuário **não está bloqueada.**

-   Verifique se a **senha do usuário não expirou ou foi esquecida.**

-   Verifique se a **Autenticação Multifator** não está bloqueando o acesso do usuário.

-   Verifique se uma **Política de acesso condicional** ou política de **Proteção de Identidade** não está bloqueando o acesso do usuário.

-   Verifique se as **informações de contato de autenticação** de um usuário estão atualizadas para permitir a aplicação da Autenticação Multifator ou de políticas de Acesso Condicional.

-   Tente também eliminar os cookies do navegador e tente entrar novamente.

## <a name="checking-the-deeplink"></a>Verificar o DeepLink

Para verificar se você tem o DeepLink correto, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

7. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

8. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

9. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

10. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

    * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

11. Selecione o aplicativo que você deseja verificar o DeepLink.

12. Localize o rótulo **URL de acesso do Usuário**. O DeepLink deve corresponder a essa URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga estas etapas:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) em um dos navegadores compatíveis e entre como um **usuário** no Azure AD.

2.  Clique no **aplicativo de SSO com senha** no Painel de Acesso.

3.  No prompt solicitando a instalação do software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você é direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se o navegador solicitar, selecione como **Habilitar** ou **Permitir** a extensão.

6.  Quando estiver instalado, **reinicie** a sessão do navegador.

7.  Entre no painel de acesso e veja se você pode **Iniciar** seus aplicativos de SSO de senha

Você também pode baixar a extensão para o Chrome e o Firefox a partir desses links diretos:

-   [Extensão do Painel de Acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar o login único com senha para um aplicativo na galeria do Azure AD

Para configurar um aplicativo da galeria do Azure Active Directory, é necessário:

-   [Adicionar um aplicativo da galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar um aplicativo da galeria do Azure AD

Para adicionar um aplicativo da Galeria do Azure Active Directory, siga estas etapas:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou **coadministrador**.

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  Na caixa de texto **Inserir um nome** da seção **Adicionar da galeria**, digite o nome do aplicativo.

7.  Selecione o aplicativo para o qual você deseja configurar o logon único.

8.  Antes de adicionar o aplicativo, é possível alterar seu nome pela caixa de texto **Nome**.

9.  Para adicionar o aplicativo, clique em **Adicionar**.

Após um curto período, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione o modo **Logon baseado em senha.**

9. [Atribua usuários ao aplicativo](#how-to-assign-a-user-to-an-application-directly).

10. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo inexistente na galeria

Para configurar um aplicativo da galeria do Azure Active Directory, é necessário:

-   [Adicionar um aplicativo inexistente na galeria](#add-a-non-gallery-application)

-   [Configurar o aplicativo para logon único com senha](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na galeria

Para adicionar um aplicativo da Galeria do Azure Active Directory, siga estas etapas:

1.  Abra o [portal do Azure](https://portal.azure.com) e entre como um **administrador global** ou **coadministrador**.

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique no botão **Adicionar** no canto superior direito do painel **Aplicativos Empresariais**.

6.  clique em **Aplicativo inexistente na galeria.**

7.  Insira o nome do aplicativo na caixa de texto **Nome**. Selecione **Adicionar.**

Após um curto período, você poderá ver o painel de configuração do aplicativo.

### <a name="configure-the-application-for-password-single-sign-on"></a>Configurar o aplicativo para logon único com senha

Para configurar o logon único para um aplicativo, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

    1.  Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8.  Selecione o modo **Logon baseado em senha.**

9.  Insira a **URL de Entrada**, a URL onde os usuários inserem o nome de usuário e senha para entrar. Verifique se os campos de entrada estão visíveis na URL.

10. Atribuir usuários a um aplicativo.

11. Além disso, também é possível fornecer credenciais em nome do usuário selecionando as linhas dos usuários, clicando em **Atualizar Credenciais** e digitando o nome de usuário e a senha em nome dos usuários. Caso contrário, os usuários serão solicitados a inserir as próprias credenciais na inicialização.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Como atribuir um usuário diretamente a um aplicativo

Para atribuir um ou mais usuários a um aplicativo diretamente, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Na lista, selecione o aplicativo ao qual deseja atribuir um usuário.

7. Após o carregamento do aplicativo, clique em **Usuários e Grupos** no menu de navegação esquerdo do aplicativo.

8. Clique no botão **Adicionar** na parte superior da lista **Usuários e Grupos** para abrir o painel **Adicionar Atribuição**.

9. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**.

10. Digite o **nome completo** ou o **endereço de email** do usuário que você deseja atribuir na caixa de pesquisa **Pesquisar por nome ou endereço de email**.

11. Passe o mouse sobre o **usuário** na lista para mostrar uma **caixa de seleção**. Clique na caixa de seleção ao lado do logotipo ou da foto de perfil do usuário para adicioná-lo à lista **Selecionado**.

12. **Opcional:** caso queira **adicionar mais de um usuário**, digite outro **nome completo** ou **endereço de email** na caixa de pesquisa **Pesquisar por nome ou endereço de email** e clique na caixa de seleção para adicionar esse usuário à lista **Selecionado**.

13. Ao concluir a seleção dos usuários, clique no botão **Selecionar** para adicioná-los à lista de usuários e grupos a serem atribuídos ao aplicativo.

14. **Opcional:** clique no seletor **Selecionar Função** no painel **Adicionar Atribuição** para selecionar uma função que será atribuída aos usuários selecionados.

15. Clique no botão **Atribuir** para atribuir o aplicativo aos usuários selecionados.

Após um breve período, os usuários selecionados poderão iniciar esses aplicativos no Painel de Acesso.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema. 

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação

-   UPN (endereço de email de usuário)

-   TenantID

-   Tipo de navegador

-   Fuso horário e hora/cronograma durante o erro

-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
