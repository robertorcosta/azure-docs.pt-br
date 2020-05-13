---
title: Resgate de convite na colaboração B2B – Azure AD
description: Descreve a experiência de resgate de convite de colaboração B2B do Azure AD para usuários finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bbfd0027f9de93ee939815853f5141044404c53
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199499"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate do convite de colaboração do Azure Active Directory B2B

Este artigo descreve as maneiras como os usuários convidados podem acessar seus recursos e o processo de consentimento que eles encontrarão. Se você enviar um email de convite para o convidado, o convite incluirá um link que o convidado pode resgatar para obter acesso ao seu aplicativo ou Portal. O email de convite é apenas uma das maneiras como os convidados podem obter acesso aos seus recursos. Como alternativa, você pode adicionar convidados ao seu diretório e dar a eles um link direto para o portal ou aplicativo que você deseja compartilhar. Independentemente do método que eles usam, os convidados são guiados por meio de um processo de consentimento pela primeira vez. Esse processo garante que seus convidados concordem com os termos de privacidade e aceitem os [termos de uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que você configurou.

Quando você adiciona um usuário convidado ao seu diretório, a conta de usuário convidado tem um status de consentimento (visível no PowerShell) inicialmente definido como **PendingAcceptance**. Essa configuração permanece até que o convidado aceite seu convite e concorde com a política de privacidade e os termos de uso. Depois disso, o status de consentimento muda para **aceito**e as páginas de consentimento não são mais apresentadas ao convidado.

   > [!IMPORTANT]
   > A **partir de 31 de março de 2021**, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários do Azure ad não gerenciado para cenários de colaboração B2B. Na preparação, incentivamos os clientes a aceitarem o [email de autenticação de senha de uso único](one-time-passcode.md). Agradecemos seus comentários sobre esse recurso de visualização pública e estamos empolgados em criar ainda mais maneiras de colaborar.

## <a name="redemption-through-the-invitation-email"></a>Resgate por meio de email de convite

Quando você adiciona um usuário convidado ao seu diretório [usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), um email de convite é enviado para o convidado no processo. Você também pode optar por enviar emails de convite quando estiver [usando o PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) para adicionar usuários convidados ao seu diretório. Aqui está uma descrição da experiência do convidado ao resgatar o link no email.

1. O convidado recebe um [email de convite](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) enviado por **convites da Microsoft**.
2. O convidado seleciona **aceitar convite** no email.
3. O convidado usará suas próprias credenciais para entrar no seu diretório. Se o convidado não tiver uma conta que possa ser federada para seu diretório e o recurso de [senha de uso único (OTP) de email](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) não estiver habilitado; o convidado é solicitado a criar uma [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) pessoal ou uma [conta de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup). Consulte o [fluxo de resgate de convite](#invitation-redemption-flow) para obter detalhes.
4. O convidado é guiado por meio da [experiência de consentimento](#consent-experience-for-the-guest) descrita abaixo.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de um link direto

Como alternativa ao email de convite, você pode dar a um convidado um link direto para seu aplicativo ou Portal. Primeiro, você precisa adicionar o usuário convidado ao seu diretório por meio do [portal do Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou do [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Em seguida, você pode usar qualquer uma das [maneiras personalizáveis para implantar aplicativos para usuários](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), incluindo links de logon diretos. Quando um convidado usa um link direto em vez do email de convite, ele ainda será guiado pela primeira experiência de consentimento.

> [!IMPORTANT]
> O link direto deve ser específico do locatário. Em outras palavras, ele deve incluir uma ID de locatário ou um domínio verificado para que o convidado possa ser autenticado em seu locatário, onde o aplicativo compartilhado está localizado. Uma URL comum como https://myapps.microsoft.com não funcionará para um convidado porque será redirecionada para seu locatário inicial para autenticação. Aqui estão alguns exemplos de links diretos com o contexto do locatário:
 > - Painel de acesso de aplicativos: https://myapps.microsoft.com/?tenantid=&lt ; ID do locatário&gt; 
 > - Painel de acesso de aplicativos para um domínio verificado: https://myapps.microsoft.com/&lt ; domínio verificado&gt;
 > - Portal do Azure: https://portal.azure.com/&lt ; ID do locatário&gt;
 > - Aplicativo individual: consulte como usar um [link de logon direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Há alguns casos em que o email de convite é recomendado em um link direto. Se esses casos especiais forem importantes para sua organização, recomendamos que você convide os usuários usando métodos que ainda enviam o email de convite:
 - O usuário não tem uma conta do Azure AD, uma MSA ou uma conta de email em uma organização federada. A menos que você esteja usando o recurso de senha de uso único, o convidado precisa resgatar o email de convite para ser guiado pelas etapas para criar um MSA.
 - Às vezes, o objeto de usuário convidado não pode ter um endereço de email devido a um conflito com um objeto de contato (por exemplo, um objeto de contato Outlook). Nesse caso, o usuário deve clicar na URL de resgate no email de convite.
 - O usuário pode entrar com um alias do endereço de email que foi convidado. (Um alias é um endereço de email adicional associado a uma conta de email.) Nesse caso, o usuário deve clicar na URL de resgate no email de convite.

## <a name="invitation-redemption-flow"></a>Fluxo de resgate de convite

Quando um usuário clica no link **aceitar convite** em um [email de convite](invitation-email-elements.md), o Azure ad automaticamente reconsidera o convite com base no fluxo de resgate, conforme mostrado abaixo:

![Captura de tela mostrando o diagrama de fluxo de resgate](media/redemption-experience/invitation-redemption-flow.png)

**Se o nome UPN do usuário corresponder a uma conta existente do Azure AD e do MSA pessoal, o usuário será solicitado a escolher a conta que deseja resgatar.*

1. O Azure AD executa a descoberta baseada no usuário para determinar se o usuário existe em um [locatário existente do Azure ad](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal).

2. Se um administrador tiver habilitado a [Federação direta](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation), o Azure ad verificará se o sufixo do domínio do usuário corresponde ao domínio de um provedor de identidade do SAML/WS-alimentado configurado e redireciona o usuário para o provedor de identidade pré-configurado.

3. Se um administrador tiver habilitado o [Google Federation](https://docs.microsoft.com/azure/active-directory/b2b/google-federation), o Azure ad verificará se o sufixo de domínio do usuário é gmail.com ou googlemail.com e redireciona o usuário para o Google.

4. O processo de resgate verifica se o usuário tem uma [MSA (conta Microsoft pessoal)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)existente.

5. Depois que o **diretório base** do usuário é identificado, o usuário é enviado ao provedor de identidade correspondente para entrar.  

6. Se as etapas 1 a 4 falharem ao localizar um diretório base para o usuário convidado, o Azure AD determinará se o locatário que está convidando habilitou o recurso de [senha de uso único (OTP) de email](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) para convidados.

7. Se o [email de senha de uso único para convidados estiver habilitado](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode), uma senha será enviada ao usuário por meio do email convidado. O usuário irá recuperar e inserir essa senha na página de entrada do Azure AD.

8. Se o email de senha de uso único para convidados estiver desabilitado, o Azure AD verificará o sufixo de domínio para determinar se ele pertence a uma conta de consumidor. Nesse caso, o usuário será solicitado a criar um [conta Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)pessoal. Caso contrário, o usuário será solicitado a criar uma [conta de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).

9. O Azure AD tenta criar uma [conta de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) verificando o acesso ao email. A verificação da conta é feita enviando um código para o email e fazendo com que o usuário a recupere e envie para o Azure AD. No entanto, se o locatário do usuário convidado for federado ou se o campo AllowEmailVerifiedUsers for definido como false no locatário do usuário convidado, o usuário não poderá concluir o resgate e o fluxo resultará em um erro. Para obter mais informações, consulte [solução de problemas Azure Active Directory colaboração B2B](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. O usuário é solicitado a criar uma [MSA (personal conta Microsoft)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Após a autenticação no provedor de identidade correto, o usuário é redirecionado para o Azure AD para concluir a [experiência de consentimento](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest).  

Para resgate JIT (just-in-time), onde o resgate é por meio de um link de aplicativo locatário, as etapas 8 a 10 não estão disponíveis. Se um usuário atingir a etapa 6 e o recurso de senha de uso único de email não estiver habilitado, o usuário receberá uma mensagem de erro e não poderá resgatar o convite. Para evitar esse erro, os administradores devem [habilitar o email de senha de uso único](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) ou garantir que o usuário clique em um link de convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o convidado

Quando um convidado entra para acessar recursos em uma organização parceira pela primeira vez, eles são guiados pelas páginas a seguir. 

1. O convidado revisa a página **examinar permissões** descrevendo a política de privacidade da organização que está convidando. Um usuário deve **aceitar** o uso de suas informações de acordo com as políticas de privacidade da organização que estão convidando para continuar.

   ![Captura de tela mostrando a página Analisar as permissões](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você, administrador de locatários, pode vincular a declaração de privacidade da sua organização, consulte [Como adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se os termos de uso estiverem configurados, o convidado será aberto e examinará os termos de uso e, em seguida, selecionará **aceitar**. 

   ![Captura de tela mostrando os novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   Você pode configurar os [termos de uso](../governance/active-directory-tou.md) em **relações organizacionais** (ou **identidades externas**) > **termos de uso**.

3. A menos que especificado de outra forma, o convidado é redirecionado para o painel de acesso de aplicativos, que lista os aplicativos que o convidado pode acessar.

   ![Captura de tela mostrando o painel de acesso de aplicativos](media/redemption-experience/myapps.png) 

No seu diretório, o valor de **convite aceito** do convidado muda para **Sim**. Se um MSA foi criado, a **origem** do convidado mostrará a **conta da Microsoft**. Para obter mais informações sobre as propriedades de conta de usuário convidado, consulte [Propriedades de um usuário de colaboração B2B do Azure ad](user-properties.md). 

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B ao Azure Active Directory?](add-users-information-worker.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory usando o PowerShell](customize-invitation-api.md#powershell)
- [Deixar uma organização como um usuário convidado](leave-the-organization.md)
