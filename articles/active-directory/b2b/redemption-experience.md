---
title: Resgate de convites em colaboração B2B - Azure AD
description: Descreve a experiência de resgate de convite de colaboração B2B do Azure AD para usuários finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050830"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate do convite de colaboração do Azure Active Directory B2B

Este artigo descreve como os usuários convidados podem acessar seus recursos e o processo de consentimento que encontrarão. Se você enviar um e-mail de convite para o hóspede, o convite inclui um link que o hóspede pode resgatar para ter acesso ao seu aplicativo ou portal. O e-mail convite é apenas uma das maneiras que os hóspedes podem ter acesso aos seus recursos. Como alternativa, você pode adicionar convidados ao seu diretório e dar-lhes um link direto para o portal ou aplicativo que você deseja compartilhar. Independentemente do método que eles usam, os hóspedes são guiados através de um processo de consentimento pela primeira vez. Este processo garante que seus hóspedes concordem com os termos de privacidade e aceitem quaisquer [termos de uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) que você tenha configurado.

Quando você adiciona um usuário convidado ao seu diretório, a conta de usuário convidado tem um status de consentimento (visível no PowerShell) que é inicialmente definido como **PendingAcceptance**. Esta configuração permanece até que o convidado aceite seu convite e concorde com sua política de privacidade e termos de uso. Depois disso, o status de consentimento muda para **Aceito**, e as páginas de consentimento não são mais apresentadas ao convidado.

   > [!IMPORTANT]
   > **A partir de 31 de março de 2021**, a Microsoft não suportará mais o resgate de convites criando contas AD azure não gerenciadas e inquilinos para cenários de colaboração B2B. Na preparação, encorajamos os clientes a optar pela [autenticação única de senha por e-mail.](one-time-passcode.md) Damos as boas-vindas ao seu feedback sobre este recurso de pré-visualização pública e estamos entusiasmados em criar ainda mais maneiras de colaborar.

## <a name="redemption-through-the-invitation-email"></a>Resgate por meio de email de convite

Quando você adiciona um usuário convidado ao seu diretório [usando o portal Azure,](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)um e-mail de convite é enviado ao convidado no processo. Você também pode optar por enviar e-mails de convite quando estiver [usando o PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) para adicionar usuários convidados ao seu diretório. Aqui está uma descrição da experiência do hóspede quando resgata o link no e-mail.

1. O hóspede recebe um [e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) de convite enviado do **Microsoft Invitations**.
2. O convidado seleciona **Começar no** e-mail.
3. Se o hóspede não tiver uma conta AD do Azure, uma Conta Microsoft (MSA) ou uma conta de e-mail em uma organização federada, ele será solicitado a criar um MSA (a menos que o recurso [de senha única](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) esteja ativado, o que não requer um MSA).
4. O hóspede é guiado através da [experiência de consentimento](#consent-experience-for-the-guest) descrita abaixo.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de um link direto

Como alternativa ao e-mail do convite, você pode dar a um convidado um link direto para o seu aplicativo ou portal. Primeiro você precisa adicionar o usuário convidado ao seu diretório através do [portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) ou [powerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Em seguida, você pode usar qualquer uma das [maneiras personalizáveis de implantar aplicativos para os usuários,](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)incluindo links de login direto. Quando um hóspede usa um link direto em vez do e-mail de convite, ele ainda será guiado através da experiência de consentimento pela primeira vez.

> [!IMPORTANT]
> O link direto deve ser específico do inquilino. Em outras palavras, ele deve incluir um ID de inquilino ou domínio verificado para que o hóspede possa ser autenticado em seu inquilino, onde o aplicativo compartilhado está localizado. Uma URL https://myapps.microsoft.com comum como não funcionará para um convidado porque será redirecionar para seu inquilino doméstico para autenticação. Aqui estão alguns exemplos de links diretos com o contexto do inquilino:
 > - Painel de https://myapps.microsoft.com/?tenantid=&ltacesso a aplicativos: ;id inquilino&gt; 
 > - Painel de acesso de aplicativos https://myapps.microsoft.com/&ltpara um domínio verificado: ;domínio verificado&gt;
 > - Portal Azure: https://portal.azure.com/&lt;id inquilino&gt;
 > - Aplicativo individual: veja como usar um [link de login direto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Há alguns casos em que o e-mail convite é recomendado através de um link direto. Se esses casos especiais são importantes para sua organização, recomendamos que você convide os usuários usando métodos que ainda enviam o e-mail do convite:
 - O usuário não tem uma conta AD do Azure, uma MSA ou uma conta de e-mail em uma organização federada. A menos que você esteja usando o recurso de senha única, o hóspede precisa resgatar o e-mail de convite para ser guiado através das etapas para a criação de um MSA.
 - Às vezes, o objeto de usuário convidado não pode ter um endereço de email devido a um conflito com um objeto de contato (por exemplo, um objeto de contato Outlook). Nesse caso, o usuário deve clicar na URL de resgate no email de convite.
 - O usuário pode entrar com um alias do endereço de email que foi convidado. (Um alias é um endereço de e-mail adicional associado a uma conta de e-mail.) Neste caso, o usuário deve clicar na URL de resgate no e-mail do convite.

## <a name="consent-experience-for-the-guest"></a>Experiência de consentimento para o hóspede

Quando um convidado faz o logpara acessar recursos em uma organização parceira pela primeira vez, eles são guiados pelas páginas a seguir. 

1. O convidado analisa a página **de permissões do Review** descrevendo a declaração de privacidade da organização convidativa. Um usuário deve **aceitar** o uso de suas informações de acordo com as políticas de privacidade da organização convidativa para continuar.

   ![Captura de tela mostrando a página Analisar as permissões](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Para obter informações sobre como você, administrador de locatários, pode vincular a declaração de privacidade da sua organização, consulte [Como adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement).

2. Se os termos de uso estiverem configurados, o convidado abrirá e revisará os termos de uso e, em seguida, **selecionará Aceitar**. 

   ![Captura de tela mostrando os novos termos de uso](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Você pode configurar [os termos de uso](../governance/active-directory-tou.md) no **Gerenciar** > **relações** > **organizacionais Termos de uso**.

3. Salvo especificação em contrário, o convidado é redirecionado para o painel de acesso aplicativos, que lista os aplicativos que o hóspede pode acessar.

   ![Captura de tela mostrando o painel de acesso de aplicativos](media/redemption-experience/myapps.png) 

Em seu diretório, o **Convite** do convidado aceitou mudanças de valor para **Sim**. Se um MSA foi criado, a **Fonte** do convidado mostrará **a Conta Microsoft**. Para obter mais informações sobre propriedades da conta de usuário convidado, consulte [Propriedades de um usuário de colaboração AZure AD B2B](user-properties.md). 

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração Azure AD B2B?](what-is-b2b.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B ao Azure Active Directory?](add-users-information-worker.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory usando o PowerShell](customize-invitation-api.md#powershell)
- [Deixar uma organização como um usuário convidado](leave-the-organization.md)
