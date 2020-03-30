---
title: Adicione usuários de colaboração B2B no portal Azure - Azure AD
description: Mostra como um administrador pode adicionar usuários convidados ao diretório de uma organização parceira usando a colaboração B2B do Azure Active Directory (Microsoft Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263498"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure

Como um usuário que é atribuído a qualquer uma das funções de diretório de administrador limitado, você pode usar o portal Azure para convidar usuários de colaboração B2B. É possível convidar usuários convidados para o diretório, um grupo ou um aplicativo. Depois de convidar um usuário por qualquer um desses métodos, a conta do usuário convidado é adicionada ao Azure Active Directory (Microsoft Azure AD), com um tipo de usuário *Convidado*. O usuário convidado deve, então, resgata o convite para acessar recursos. Um convite de um usuário não expira.

Depois de adicionar um usuário convidado ao diretório, você poderá enviar ao usuário convidado um link direto para um aplicativo compartilhado, ou o usuário convidado poderá clicar na URL de resgate no email de convite. Para obter mais informações sobre o processo de resgate, consulte [Regaste de convite de colaboração B2B](redemption-experience.md).

> [!IMPORTANT]
> Você deve seguir as etapas em [Instruções: adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement) para adicionar a URL da declaração de privacidade da sua organização. Como parte do processo de resgate de convite pela primeira vez, um usuário convidado deve concordar com os termos de privacidade para continuar. 

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que as configurações de colaboração externa da sua organização estejam configuradas de forma que você possa convidar convidados. Por padrão, todos os usuários e admins podem convidar convidados. Mas as políticas de colaboração externa da sua organização podem ser configuradas para evitar que certos tipos de usuários ou admins convidem convidados. Para descobrir como visualizar e definir essas políticas, consulte [Ativar colaboração externa B2B e gerenciar quem pode convidar convidados](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Adicionar usuários convidados ao diretório

Para adicionar usuários de colaboração B2B ao diretório, siga estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com) como um usuário que é designado para uma função de diretório de administrador limitado ou a função Convidado convidado.
2. Procure e selecione **o Diretório Ativo do Azure em** qualquer página.
3. Em **Gerenciar**, selecione **Usuários**.
4. Selecione **Novo usuário convidado**.

   ![Mostra onde o novo usuário convidado está na interface do usuário](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > A opção **Novo usuário convidado** também está disponível na página **Relações organizacionais**. No **Azure Active Directory**, em **Gerenciar**, selecione **Relações organizacionais**.

5. Na página **Novo usuário**, selecione **Convidar usuário** e adicione as informações do usuário convidado. 

    > [!NOTE]
    > Não há suporte para endereços de email de grupo; insira o endereço de email para um indivíduo. Além disso, alguns provedores de email permitem que os usuários adicionem um sinal de adição (+) de símbolo e texto adicional aos seus endereços de email para ajudar com coisas como a filtragem de caixa de entrada. No entanto, o Microsoft Azure Active Directory atualmente não dá suporte além para símbolos de adição em endereços de email. Para evitar problemas de entrega, omita o símbolo de adição e quaisquer caracteres seguintes até o símbolo @.

   - **Nome.** O nome e o sobrenome do usuário convidado.
   - **Endereço de email (obrigatório)**. O endereço de email do usuário convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem pessoal de boas-vindas para o usuário convidado.
   - **Grupos**: Você pode adicionar o usuário convidado a um ou mais grupos existentes, ou você pode fazê-lo mais tarde.
   - **Função de diretório**: Se você precisar de permissões administrativas azure AD para o usuário, você pode adicioná-las a uma função Azure AD. 

7. Selecione **Convidar** para enviar automaticamente o convite ao usuário convidado. 
 
Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.


![Mostrar o usuário B2B com o tipo de usuário Convidado](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar usuários convidados a um grupo
Se você precisar adicionar manualmente usuários de colaboração B2B a um grupo, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Procure e selecione **o Diretório Ativo do Azure em** qualquer página.
3. Em **Gerenciar**, selecione **Grupos**.
4. Selecione um grupo (ou clique em **Novo grupo** para criar um novo). É recomendável incluir na descrição do grupo que o grupo contém usuários convidados B2B.
5. Selecione **Membros**. 
6. Realize um dos seguintes procedimentos:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário e clique em **Selecionar** para adicioná-lo ao grupo.
   - Se o usuário convidado ainda não existir no diretório, convide-o para fazer parte do grupo digitando seu endereço de email na caixa de pesquisa, digitando uma mensagem pessoal opcional e, em seguida, clicando em **Selecionar**. O convite é enviado automaticamente para o usuário convidado.
     
     ![Adicionar botão Convidar para adicionar membros convidados](./media/add-users-administrator/GroupInvite.png)
   
Também é possível usar grupos dinâmicos com a colaboração B2B do Microsoft Azure AD. Para obter mais informações, consulte [Grupos dinâmicos e colaboração B2B do Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar usuários convidados a um aplicativo

Para adicionar usuários de colaboração B2B a um aplicativo, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Procure e selecione **o Diretório Ativo do Azure em** qualquer página.
3. Em **Gerenciar**, selecione **Aplicativos empresariais** > **Todos aplicativos**.
4. Selecione o aplicativo ao qual você deseja adicionar usuários convidados.
5. No painel do aplicativo, selecione **Total de Usuários** para abrir o painel **Usuários e grupos**.

    ![Botão Total de Usuários para abrir Usuários e grupos](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecione **Adicionar usuário**.
7. Em **Adicionar Atribuição**, selecione **Usuários e grupos**.
8. Realize um dos seguintes procedimentos:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário, clique em **Selecionar** e, em seguida, clique em **Atribuir** para adicionar o usuário ao aplicativo.
   - Se o usuário convidado ainda não existir no diretório, em **Selecionar membro ou convidar um usuário externo**, digite o endereço de e-mail do usuário. Na caixa de mensagem, digite uma mensagem pessoal opcional. Na caixa de mensagem, clique em **convidar**.
           
       ![Adicionar botão Convidar para adicionar membros convidados](./media/add-users-administrator/AppInviteUsers.png)
   
      Clique em **Selecionar** e, em seguida, clique em **Atribuir** para adicionar o usuário ao aplicativo. Um convite é enviado automaticamente para o usuário convidado.

9. O usuário convidado é exibido na lista **Usuários e grupos** do aplicativo, com a função **Acesso Padrão** atribuída. Se quiser alterar a função, faça o seguinte:
   - Selecione o usuário convidado e, em seguida, selecione **Editar**. 
   - Em **Editar Atribuição**, clique em **Selecionar Função** e selecione a função que deseja atribuir ao usuário selecionado.
   - Clique em **Selecionar**.
   - Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenviar convites para usuários convidados

Se um usuário convidado ainda não tiver resgatado o convite, você poderá reenviar o email de convite.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Procure e selecione **o Diretório Ativo do Azure em** qualquer página.
3. Em **Gerenciar**, selecione **Usuários**.
5. Selecione a conta do usuário.
6. Em **Gerenciar**, selecione **Perfil**.
7. Se o usuário ainda não aceitou o convite, a opção **Reenviar convite** estará disponível. Selecione esse botão para reenviar.

   ![Reenviar opção de convite no perfil do usuário](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Se você reenviar um convite que originalmente direcionou o usuário para um aplicativo específico, entenda que o link no novo convite levará o usuário ao Painel de Acesso de nível superior.

## <a name="next-steps"></a>Próximas etapas

- Para saber como os administradores não Microsoft Azure AD podem adicionar usuários convidados B2B, consulte [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- Para obter informações sobre o email de convite, consulte [Os elementos do email de convite para colaboração B2Bl](invitation-email-elements.md).

