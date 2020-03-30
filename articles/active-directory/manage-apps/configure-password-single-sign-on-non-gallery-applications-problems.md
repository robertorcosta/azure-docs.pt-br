---
title: Problemas na configuração de senha SSO para aplicativos que não são galerias
description: Problemas comuns que ocorrem quando você configura o SSO (Single Signon, assinatura de senha) para aplicativos personalizados que não estão na galeria de aplicativos do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274141"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemas na configuração de login único de senha para um aplicativo que não é galeria

Este artigo descreve problemas comuns que podem ocorrer quando você configura *o sso de assinatura de senha* para um aplicativo que não é galeria.

## <a name="capture-sign-in-fields-for-an-app"></a>Capturar campos de login para um aplicativo

A captura de campo de login é suportada apenas para páginas de login habilitadas para HTML. Ele não é suportado para páginas de login não padrão, como aquelas que usam o Adobe Flash ou outras tecnologias não habilitadas para HTML.

Existem duas maneiras de capturar campos de login para seus aplicativos personalizados:

- **A captura automática de campo de login** funciona bem com a maioria das páginas de login habilitadas para HTML, se eles *usarem IDs DIV conhecidos* para os campos de nome de usuário e senha. O HTML na página é raspado para encontrar IDs DIV que correspondem a certos critérios. Esses metadados são salvos para que possam ser reproduzidos para o aplicativo mais tarde.

- **A captura manual do campo de login** é usada se o fornecedor do aplicativo não rotular os campos de entrada de *login*. A captura manual também é usada se o fornecedor *renderizar vários campos que não podem ser detectados automaticamente*. O Azure Active Directory (Azure AD) pode armazenar dados para tantos campos quanto houver na página de login, se você disser onde esses campos estão na página.

Em geral, se a captura automática de campo de login não funcionar, tente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capture automaticamente campos de login para um aplicativo

Para configurar o SSO baseado em senha usando a captura automática de campo de login, siga estas etapas:

1. Abra o [portal Azure.](https://portal.azure.com/) Faça login como administrador global ou co-administrador.

2. No painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** no topo da lista **Todos os Aplicativos.** Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para SSO.

7. Após as cargas do aplicativo, selecione **Single sign-on** no painel de navegação do lado esquerdo.

8. Selecione o modo **de login baseado em senha.**

9. Digite a **URL de login**, que é a URL da página onde os usuários digitam seu nome de usuário e senha para fazer login. *Certifique-se de que os campos de login estão visíveis na página para a URL que você fornece*.

10. Selecione **Salvar**.

    A página é raspada automaticamente para as caixas de entrada de nome de usuário e senha. Agora você pode usar o Azure AD para transmitir senhas com segurança para esse aplicativo usando a extensão do navegador Access Panel.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturar manualmente campos de login para um aplicativo

Para capturar manualmente campos de login, você deve ter a extensão do navegador do Painel de Acesso instalada. Além disso, seu navegador não pode estar sendo executado no modo *inPrivate,* *incognito*ou *privado.*

Para instalar a extensão, consulte a seção Instalar a seção de [extensão Access Panel Browser](#install-the-access-panel-browser-extension) deste artigo.

Para configurar o SSO baseado em senha para um aplicativo usando a captura manual de campo de login, siga estas etapas:

1. Abra o [portal Azure.](https://portal.azure.com/) Faça login como administrador global ou co-administrador.

2. No painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão Azure AD.

3. Digite **o Diretório Ativo do Azure** na caixa de pesquisa do filtro e, em seguida, selecione O Diretório Ativo do **Azure**.

4. Selecione **aplicações corporativas** no painel de navegação Azure AD.

5. Selecione **Todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE] 
   > Se você não ver o aplicativo que deseja, use o controle **Filtro** no topo da lista **Todos os Aplicativos.** Defina a opção **Mostrar** como "Todas as aplicações".

6. Selecione o aplicativo que deseja configurar para SSO.

7. Após as cargas do aplicativo, selecione **Single sign-on** no painel de navegação do lado esquerdo.

8. Selecione o modo **de login baseado em senha.**

9. Digite a **URL de login**, que é a página onde os usuários digitam seu nome de usuário e senha para fazer login. *Certifique-se de que os campos de login estão visíveis na página para a URL que você fornece*.

10. Selecione **Configurar * &lt;&gt; o nome do aplicativo* Senha Única Configurações de login**.

11. Selecione **Detectar manualmente campos de login**.

14. Selecione **Ok**.

15. Selecione **Salvar**.

16. Siga as instruções para usar o Painel de Acesso.

## <a name="troubleshoot-problems"></a>Solucionar problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Eu recebo um erro "Não conseguimos encontrar nenhum campo de login nessa URL"

Você recebe esta mensagem de erro quando a detecção automática de campos de login falha. Para resolver o problema, tente a detecção manual de campo de login. Consulte [os campos de login de captura manual para uma](#manually-capture-sign-in-fields-for-an-app) seção de aplicação deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Eu recebo um erro "Incapaz de salvar configuração de login único"

Raramente, a atualização da configuração SSO falha. Para resolver esse problema, tente salvar a configuração novamente.

Se você continuar recebendo o erro, abra um caso de suporte. Inclua as informações descritas nos [detalhes](#view-portal-notification-details) de notificação do portal View e [envie detalhes de notificação a um engenheiro](#send-notification-details-to-a-support-engineer-to-get-help) de suporte para obter seções de ajuda deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Não consigo detectar manualmente campos de login para o meu aplicativo

Você pode observar os seguintes comportamentos quando a detecção manual não está funcionando:

- O processo de captura manual parecia funcionar, mas os campos capturados não estão corretos.

- Os campos corretos não são destacados quando o processo de captura é executado.

- O processo de captura leva você à página de login do aplicativo como esperado, mas nada acontece.

- A captura manual parece funcionar, mas o SSO não acontece quando os usuários navegam até o aplicativo a partir do Painel de Acesso.

Se você tiver algum desses problemas, faça as seguintes coisas:

- Certifique-se de que você tem a versão mais recente da extensão do navegador Access Panel *instalada e ativada*. Consulte a seção [Instalar a extensão](#install-the-access-panel-browser-extension) do navegador do Painel de Acesso deste artigo.

- Certifique-se de que seu navegador não está no modo *anônimo,* *inPrivate*ou *Private* durante o processo de captura. A extensão do Painel de Acesso não é suportada nesses modos.

- Certifique-se de que seus usuários não estão tentando entrar no aplicativo do Painel de Acesso enquanto estiver no modo *anônimo,* *inPrivate*ou *Privado.*

- Tente o processo de captura manual novamente. Certifique-se de que os marcadores vermelhos estão sobre os campos corretos.

- Se o processo de captura manual parecer parar de responder ou a página de login não responder, tente o processo de captura manual novamente. Mas desta vez, após concluir o processo, pressione a tecla F12 para abrir o console de desenvolvedor do seu navegador. Selecione a guia **console.** **Digite window.location="*&lt;a URL de&gt;login que você especificou ao configurar o aplicativo*"** e, em seguida, pressione Enter. Isso força um redirecionamento de página que encerra o processo de captura e armazena os campos que foram capturados.

### <a name="contact-support"></a>Contate o suporte

Se você ainda tiver problemas, abra um caso com o Microsoft Support. Descreva o que tentou. Inclua os detalhes descritos nos [detalhes](#view-portal-notification-details) de notificação do portal Exibir e [envie detalhes de notificação a um engenheiro](#send-notification-details-to-a-support-engineer-to-get-help) de suporte para obter seções de ajuda deste artigo (se aplicável).

## <a name="install-the-access-panel-browser-extension"></a>Instale a extensão do navegador do Painel de Acesso

Siga estas etapas:

1. [Painel de acesso aberto](https://myapps.microsoft.com) em um navegador suportado. Faça login no Azure AD como *usuário*.

2. Selecione **o aplicativo Password-SSO** no Painel de Acesso.

3. Quando for solicitado a instalar o software, selecione **Instalar agora**.

4. Você será direcionado para uma página de download para o seu navegador. Escolha **adicionar** a extensão.

5. Se você for solicitado, selecione **Ativar** ou **Permitir**.

6. Após a instalação, reinicie seu navegador.

7. Faça login no Painel de Acesso. Veja se você pode abrir seus aplicativos habilitados para SSO por senha.

Você também pode baixar diretamente a extensão do navegador para Chrome e Firefox através desses links:

-   [Extensão do Painel de Acesso Cromo](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Ver detalhes da notificação do portal

Para ver os detalhes de qualquer notificação do portal, siga estas etapas:

1. Selecione o ícone **Notificações** (o sino) no canto superior direito do portal Azure.

2. Selecione qualquer notificação que mostre um estado *de erro.* (Eles têm um vermelho "!".)

   > [!NOTE]
   > Você não pode selecionar notificações que estão no estado *de sucesso* ou *em progresso.*

3. Isso abre o painel **Detalhes de Notificação**. Leia as informações para saber sobre o problema.

5. Se você ainda precisar de ajuda, compartilhe as informações com um engenheiro de suporte ou com o grupo de produtos. Selecione o ícone **de cópia** à direita da caixa de **erro Copiar** para copiar os detalhes de notificação a serem compartilhados.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Envie detalhes de notificação a um engenheiro de suporte para obter ajuda

É importante que você compartilhe *todos os* detalhes listados nesta seção com suporte para que eles possam ajudá-lo rapidamente. Para gravá-lo, você pode tirar uma captura de tela ou selecionar **erro copiar**.

As informações a seguir explicam o que cada item de notificação significa e fornecem exemplos.

### <a name="essential-notification-items"></a>Itens essenciais de notificação

- **Denominação**: o título descritivo da notificação.

   Exemplo: *Configurações de proxy de aplicativos*

- **Descrição**: o que ocorreu como resultado da operação.

   Exemplo: *Url interno inserido já está sendo usado por outro aplicativo.*

- **ID de notificação**: o ID único da notificação.

    Exemplo: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID de solicitação do cliente**: o ID de solicitação específico que seu navegador fez.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Carimbo de data UTC**: o carimbo de data de quando a notificação ocorreu, na UTC.

    Exemplo: *2017-03-23T19:50:43.7583681Z*

- **ID de transação interna**: o ID interno usado para procurar o erro em nossos sistemas.

    Exemplo: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: O usuário que comandou a operação.

    Exemplo: *tperkins\@f128.info*

- **ID do inquilino**: a identificação única do inquilino do que o usuário que executou a operação é membro.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID do objeto do usuário**: O ID exclusivo do usuário que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

- **Nome de exibição**: (pode estar vazio) um nome de exibição mais detalhado para o erro.

    Exemplo: *Configurações de proxy de aplicativos*

- **Status**: o status específico da notificação.

    Exemplo: *Falha*

- **ID do objeto**: (pode estar vazio) o ID do objeto contra o qual a operação foi executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Detalhes**: a descrição detalhada do que ocorreu como resultado da operação.

    Exemplo: *Url<https://bing.com/>interno ' é inválido, pois já está em uso.*

- **Erro de cópia**: Permite selecionar o ícone de **cópia** à direita da caixa de texto de **erro Copiar** para copiar os detalhes da notificação para ajudar com o suporte.

    Exemplo:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
