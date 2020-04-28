---
title: Problema ao entrar no site do painel de acesso | Microsoft Docs
description: Orientação para solução de problemas que você pode encontrar ao tentar entrar e usar o Painel de Acesso
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "65784316"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problema ao entrar no site do painel de acesso

O Painel de Acesso é um portal baseado na Web que permite a um usuário que tenha uma conta corporativa ou de estudante no Azure Active Directory (Azure AD) exibir e iniciar aplicativos baseados em nuvem para os quais o administrador do Azure AD concedeu acesso. Um usuário com as edições do Azure AD também pode usar os recursos de gerenciamento de grupo de autoatendimento e aplicativo por meio do Painel de Acesso. O Painel de Acesso é separado do Portal do Azure e não exige que os usuários tenham uma assinatura do Azure.

Os usuários podem entrar no Painel de Acesso se eles tiverem uma conta corporativa ou de estudante no Azure AD.

-   Os usuários podem ser autenticados diretamente no Azure AD.

-   Os usuários podem ser autenticados usando os Serviços de Federação do Active Directory (AD FS).

-   Os usuários podem ser autenticados pelo Windows Server Active Directory.

Se um usuário tiver uma assinatura do Azure ou Office 365 e estiver usando o Portal do Azure ou um aplicativo do Office 365, ele poderá usar o Painel de Acesso sem precisar entrar novamente. Os usuários que não são autenticados são solicitados a entrarem usando o nome de usuário e senha de sua conta no Azure Active Directory. Se a organização tiver configurado a federação, digitar o nome do usuário será suficiente.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro 

-   Verifique se o usuário está entrando na **URL correta**:<https://myapps.microsoft.com>

-   Verifique se o navegador do usuário adicionou a URL aos seus **sites confiáveis**

-   Certifique-se de que a conta do usuário está **habilitada** para entradas.

-   Verifique se a conta do usuário **não está bloqueada.**

-   Verifique se a **senha do usuário não expirou ou foi esquecida.**

-   Verifique se a **Autenticação Multifator** não está bloqueando o acesso do usuário.

-   Verifique se uma **Política de acesso condicional** ou política de **Proteção de Identidade** não está bloqueando o acesso do usuário.

-   Certifique-se de que as **informações de contato de autenticação** estão atualizadas para permitir a imposição de políticas de Acesso Condicional e Autenticação Multifator.

-   Tente também eliminar os cookies do navegador e tente entrar novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Atender aos requisitos de navegador para o Painel de Acesso

O Painel de Acesso exige um navegador com suporte para JavaScript e CSS habilitado. Para usar o SSO (logon único) baseado em senha no Painel de Acesso, a extensão do Painel de Acesso deve estar instalada no navegador do usuário. Essa extensão é baixada automaticamente quando um usuário seleciona um aplicativo configurado para SSO baseado em senha.

Para SSO baseado em senha, os navegadores do usuário final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   O Microsoft Edge na Edição de Aniversário do Windows 10 ou posterior 

-   Chrome – No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior, no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior


## <a name="problems-with-the-users-account"></a>Problemas com a conta do usuário

O acesso ao Painel de Acesso pode ser bloqueado devido a um problema com a conta do usuário. Seguem-se algumas maneiras de solucionar e resolver problemas com os usuários e as configurações de sua conta:

-   [Verificar se existe uma conta de usuário no Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Verificar o status da conta do usuário](#check-a-users-account-status)

-   [Redefinir a senha de um usuário](#reset-a-users-password)

-   [Habilitar a redefinição de senha por autoatendimento](#enable-self-service-password-reset)

-   [Verificar o status da Autenticação Multifator de um usuário](#check-a-users-multi-factor-authentication-status)

-   [Verificar as informações de contato de autenticação de um usuário](#check-a-users-authentication-contact-info)

-   [Verificar as associações de grupo de um usuário](#check-a-users-group-memberships)

-   [Verificar as licenças atribuídas de um usuário](#check-a-users-assigned-licenses)

-   [Atribuir uma licença a um usuário](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verificar se existe uma conta de usuário no Azure Active Directory

Para verificar se a conta de um usuário está presente, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Verifique as propriedades do objeto do usuário para ter certeza de que elas estejam definidas conforme o esperado e de que nenhum dado esteja faltando.

### <a name="check-a-users-account-status"></a>Verificar o status da conta do usuário

Para verificar o status da conta de um usuário, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **perfil**.

8.  Em **Configurações** verifique se **Bloquear entrada** está definido como **Não**.

### <a name="reset-a-users-password"></a>Redefinir a senha de um usuário

Para redefinir a senha de um usuário, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique no botão **Redefinir senha** na parte superior do painel do usuário.

8.  Clique no botão **Redefinir senha** no painel **Redefinir senha** que aparece.

9.  Copie a **senha temporária** ou **insira uma nova senha** para o usuário.

10. Informe essa nova senha para o usuário, e que ele precisa alterar essa senha durante o próximo logon no Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

Para habilitar a redefinição de senha por autoatendimento, siga estas etapas de implantação:

-   [Permitir que os usuários redefinam suas senhas do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Permitir que os usuários redefinam ou alterem suas senhas locais do Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verificar o status da Autenticação Multifator de um usuário

Para verificar o status da Autenticação Multifator de um usuário, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **usuários e grupos** no menu de navegação.

5. clique em **todos os usuários**.

6. Clique no botão **Autenticação Multifator** na parte inferior do painel.

7. Depois que o **portal de administração da autenticação multifator** carregar, verifique se você está na guia **usuários** .

8. Encontre o usuário na lista de usuários pesquisando, filtrando ou classificando.

9. Selecione o usuário na lista de usuários e **Habilite**, **Desabilite** ou **Imponha** a autenticação multifator conforme o desejado.

   >[!NOTE]
   >Se um usuário estiver em um estado **Imposto**, defina-o temporariamente como **Desabilitado** para deixá-lo entrar novamente na conta. Quando ele puder entrar novamente, altere novamente o estado para **Habilitado** para exigir o novo registro de suas informações de contato durante a próxima entrada. Como alternativa, execute as etapas em [Verificar as informações de contato de autenticação do usuário](#check-a-users-authentication-contact-info) para verificar ou definir esses dados para eles.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Verificar as informações de contato de autenticação de um usuário

Para verificar as informações de contato de autenticação do usuário usadas para Autenticação Multifator, Acesso Condicional, Proteção de Identidade e Redefinição de Senha, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **perfil**.

8.  Role a tela para baixo até **Informações de contato de autenticação**.

9.  **Revise** os dados registrados para o usuário e a atualização conforme o necessário.

### <a name="check-a-users-group-memberships"></a>Verificar as associações de grupo de um usuário

Para verificar as associações de grupo de um usuário, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  Clique em **Grupos** para ver de quais grupos o usuário é membro.

### <a name="check-a-users-assigned-licenses"></a>Verificar as licenças atribuídas de um usuário

Para verificar as licenças atribuídas de um usuário, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

### <a name="assign-a-user-a-license"></a>Atribuir uma licença a um usuário 

Para atribuir uma licença a um usuário, siga estas etapas:

1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  clique em **usuários e grupos** no menu de navegação.

5.  clique em **todos os usuários**.

6.  **Pesquise** pelo usuário no qual você está interessado e **clique na linha** para selecionar.

7.  clique em **Licenças** para ver quais licenças o usuário atribuiu atualmente.

8.  Clique no botão **atribuir** .

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique no item **opções de atribuição** para atribuir produtos granularmente. Clique em **OK** quando isso for concluído.

11. Clique no botão **Atribuir** para atribuir essas licenças para esse usuário.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se essas etapas de solução de problemas não resolverem o problema

Abra um tíquete de suporte com as informações a seguir, se estiverem disponíveis:

-   ID de erro de correlação

-   UPN (endereço de email de usuário)

-   ID do locatário

-   Tipo de navegador

-   Fuso horário e hora/cronograma durante o erro

-   Rastreamentos do Fiddler

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
