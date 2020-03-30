---
title: Como configurar o login único de senha para aplicativos AD do Azure | Microsoft Docs
description: Como configurar o SSO (Single Signon, assinatura de senha) para seus aplicativos corporativos Azure AD na plataforma de identidade Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063519"
---
# <a name="configure-password-single-sign-on"></a>Configurar o logon único com senha

Quando você [adiciona um aplicativo de galeria](add-gallery-app.md) ou um aplicativo web que não é [galeria](add-non-gallery-app.md) aos seus aplicativos Azure AD Enterprise, uma das opções únicas de login disponíveis para você é o login único baseado [em senha](what-is-single-sign-on.md#password-based-sso). Esta opção está disponível para qualquer web com uma página de login HTML. O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como nas contas de aplicativos de mídia social da sua organização. 

O SSO baseado em senha é uma ótima maneira de começar a integrar aplicativos no Azure AD rapidamente, e permite que você:

-   Habilitar o **logon único para os usuários** armazenando de forma segura e reproduzindo os nomes de usuário e senhas do aplicativo integrado ao Azure AD

-   **Dar suporte a aplicativos que exigem vários campos de entrada**, para aplicativos que exigem mais do que apenas os campos de nome de usuário e senha para entrar

-   **Personalizar os rótulos** dos campos de entrada de nome de usuário e senha que seus usuários veem no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando digitam suas credenciais

-   Permitir que os **usuários** forneçam seus próprios nomes de usuário e senhas para as contas de aplicativos existentes que eles estiverem digitando manualmente no [Painel de Acesso do Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócios** especifique os nomes de usuário e senhas atribuídos a um usuário usando o recurso de [Autoatendimento de Acesso ao Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que um **administrador** especifique um nome de usuário e senha para ser usado por indivíduos ou grupos ao fazer login no aplicativo usando o recurso Credenciais de atualização 

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, confira [Adicionar um aplicativo da galeria](add-gallery-app.md) ou [Adicionar um aplicativo inexistente na galeria](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Abra o aplicativo e selecione o login único de senha

1. Entre no [portal do Azure](https://portal.azure.com) como administrador do aplicativo em nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

2. Navegue até os aplicativos **do Azure Active Directory** > **Enterprise**. Uma amostra aleatória dos aplicativos em seu locatário do Azure AD é exibida. 

3. No menu **Tipo de Aplicativo**, selecione **Todos os aplicativos** e, em seguida, **Aplicar**.

4. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

5. Na seção **Gerenciar**, selecione **Logon único**. 

6. Selecione **baseado em senha**.

7. Digite a URL da página de login baseada na Web do aplicativo. Esta seqüência deve ser a página que inclui o campo de entrada de nome de usuário.

   ![Logon único baseado em senha](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selecione **Salvar**. O Azure AD tenta analisar a página de login para uma entrada de nome de usuário e uma entrada de senha. Se a tentativa for bem sucedida, você está feito. 
 
> [!NOTE]
> O próximo passo é [atribuir usuários ou grupos ao aplicativo](methods-for-assigning-users-and-groups.md). Depois de atribuir usuários e grupos, você pode fornecer credenciais para serem usadas em nome de um usuário quando eles fizerem login no aplicativo. Selecione **Usuários e grupos,** selecione a caixa de seleção para a linha do usuário ou do grupo e clique em **Credenciais de atualização**. Em seguida, digite o nome de usuário e senha a ser usado em nome do usuário ou grupo. Caso contrário, os usuários serão solicitados a inserir as credenciais em si mesmos no lançamento.
 

## <a name="manual-configuration"></a>Configuração manual

Se a tentativa de análise do Azure AD falhar, você pode configurar o login manualmente.

1. Em ** \<nome do aplicativo> Configuração,** selecione ** \<Configurar o nome do aplicativo> Configurações de login único de senha** para exibir a página configurar **login.** 

2. Selecione **Detectar manualmente campos de login**. Instruções adicionais descrevendo a detecção manual de campos de login são exibidas.

   ![Configuração manual do login único baseado em senha](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selecione **Capturar campos de login**. Uma página de status de captura é aberta em uma nova guia, mostrando que a **captura de metadados da**mensagem está em andamento .

4. Se a caixa **de extensão do painel de acesso necessária** aparecer em uma nova guia, **selecione Instalar agora** para instalar a extensão do navegador De **extensão de login seguro de meus aplicativos.** (A extensão do navegador requer o Microsoft Edge, o Chrome ou o Firefox.) Em seguida, instale, inicie e habilite a extensão e atualize a página de status de captura.

   Em seguida, a extensão do navegador abre outra guia que exibe a URL inserida.
5. Na guia com a URL inserida, passe pelo processo de login. Preencha os campos nome de usuário e senha e tente fazer login. (Você não precisa fornecer a senha correta.)

   Um aviso pede que você salve os campos de login capturados.
6. Selecione **OK**. A extensão do navegador atualiza a página de status de captura com a mensagem **Metadata foi atualizada para o aplicativo**. A guia do navegador fecha.

7. Na página de login do Azure AD **Configure,** selecione **Ok, eu fui capaz de fazer login no aplicativo com sucesso**.

8. Selecione **OK**.

Após a captura da página de login, você pode atribuir usuários e grupos e configurar políticas de credencial, assim como [aplicativos SSO](what-is-single-sign-on.md)de senha regular .

> [!NOTE]
> Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar Logotipo** na guia **Configurar** do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
