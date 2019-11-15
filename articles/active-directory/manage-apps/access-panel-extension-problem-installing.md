---
title: Instalar a extensão do navegador do painel de acesso do aplicativo - Azure | Microsoft Docs
description: Corrija os erros comuns encontrados quando você instala a extensão do navegador do painel de acesso.
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
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 580207bb10680e84cfda7d4b1874f2b460602973
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082178"
---
# <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do painel de acesso

O painel de acesso é um portal baseado na Web. Caso você tenha uma conta corporativa ou de estudante no Azure Active Directory (Azure AD), pode usar o painel de acesso para exibir e iniciar aplicativos baseados em nuvem para os quais um administrador do Azure AD concedeu acesso. 

Se você estiver usando as edições do Azure AD, você também pode usar grupos de autoatendimento e recursos de gerenciamento de aplicativo por meio do painel de acesso. 

O painel de acesso é separado do portal do Azure. Não é preciso ter uma assinatura do Azure.

## <a name="web-browser-requirements"></a>Requisitos de navegador da Web

O painel de acesso requer, no mínimo, um navegador que ofereça suporte ao JavaScript e CSS habilitados. Para entrarem em aplicativos por meio do SSO baseada em senha no painel de acesso, você deve ter a extensão do painel de acesso instalada em seu navegador. A extensão é baixada automaticamente quando você seleciona um aplicativo que está configurado para SSO baseado em senha.

Para SSO baseado em senha, você pode usar qualquer um dos seguintes navegadores:

- **Microsoft Edge**: na Edição de Aniversário do Windows 10 ou posterior. 
- **Chrome**: no Windows 7 ou posterior e no MacOS X ou posterior.
- **Firefox 26.0 ou posterior**: no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior.

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão do navegador do painel de acesso

Para instalar a extensão do navegador do painel de acesso, faça o seguinte:

1.  Em um dos navegadores com suporte, abra o [painel de acesso](https://myapps.microsoft.com) e entre como um usuário em sua conta do Azure AD.

2.  Selecione um aplicativo de SSO baseado em senha.

3.  Quando você for solicitado, selecione **Instalar agora**.  
    Você será direcionado para o link de download para o seu navegador selecionado. 
    
4.  Selecione **Adicionar**.

5.  Se você for solicitado, você pode **Habilitar** ou **Permitir** a extensão.

6.  Após a instalação ser concluída, reinicie o navegador.

7.  Entre no painel de acesso e verifique para ver se é possível iniciar os aplicativos de SSO baseados em senha.

Você também pode baixar a extensão para o Chrome e para o Microsoft Edge diretamente dos seguintes sites:

- [Extensão do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Extensão do Microsoft Edge](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Extensão do Firefox](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Use a extensão Conexão segura do Meus aplicativos
* Caso esteja usando uma URL dos Meus Aplicativos diferente de `https://myapps.microsoft.com`, configure sua URL padrão fazendo o seguinte:
   1. Enquanto você *não* estiver conectado à extensão, clique com o botão direito do mouse no ícone de extensão.
   2. No menu, selecione **URL dos Meus aplicativos**.
   3. Selecione a URL padrão.
   4. Selecione o ícone da extensão.
   5. Para conectar-se à extensão, selecione **Conectar-se para começar**.

* Para conectar-se diretamente a um aplicativo no navegador, faça o seguinte:
   1. Depois de instalar a extensão, conecte-se a ela selecionando **Conectar-se para começar**.
   2. Entrar no aplicativo com a URL de logon.  
       A URL de logon geralmente é a URL do aplicativo que exibe o formulário para iniciar sessão.
      A extensão deve alterar o estado e permite que você saiba que uma senha está disponível.
   3. Para conectar-se, selecione o ícone de extensão.

* Para iniciar um aplicativo a partir da extensão, faça o seguinte:
   1. Depois de instalar a extensão, conecte-se a ela selecionando **Conectar-se para começar**.
   2. Selecione o ícone de extensão para abrir o menu.
   3. Pesquise por um aplicativo que esteja disponível no portal Meus aplicativos.
   4. Na lista de resultados de pesquisa, selecione o aplicativo.  
       Os três aplicativos que você usou são exibidos na lista de atalho **Usados recentemente**.
       
* Para usar URLs internas da empresa ao remoto, faça o seguinte:
    1. [Configurar Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) no seu locatário
    2. [Publicar o aplicativo](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e a URL por meio do Application Proxy
    3. Instale a extensão, conecte-se a ela selecionando Conectar-se para começar
    4. Agora você pode navegar para a URL interna da empresa, mesmo em remoto

> [!NOTE]
> As opções acima estão disponíveis apenas para Microsoft Edge, Chrome e Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurar uma política de grupo para o Internet Explorer

Você pode configurar uma política de grupo que permite instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos seus usuários.

Antes de configurar uma política de grupo, verifique se:

-   Você configurou os [Serviços de Domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e os computadores dos usuários ingressaram no domínio.

-   Para editar o Objeto de Política de Grupo (GPO), você deve ter permissões para *Editar configurações*. Por padrão, essa permissão é concedida a membros dos grupos de segurança a seguir: administradores de domínio, administradores de empresa e proprietários de criadores de política de grupo.

Para obter instruções passo a passo sobre como configurar a política de grupo e implantá-la para os usuários, consulte [Implantar a extensão do painel de acesso para o Internet Explorer usando a política de grupo](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Solucionar problemas da extensão do painel de acesso no Internet Explorer

Para acessar uma ferramenta de diagnóstico e informações sobre como configurar a extensão para o Internet Explorer, consulte [Solucionar problemas da extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md).

> [!NOTE]
> O Internet Explorer está com suporte limitado e já não recebe mais atualizações de software. O Microsoft Edge é o navegador recomendado.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Se as etapas anteriores não resolverem o problema

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação
-   UPN (endereço de email do usuário)
-   TenantID
-   Tipo de navegador
-   Fuso horário e a hora ou o período de tempo durante o qual o erro ocorreu
-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[O que é o acesso a aplicativos e logon único com o Azure Active Directory?](what-is-single-sign-on.md)
