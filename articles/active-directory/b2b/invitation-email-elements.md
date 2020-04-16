---
title: Elementos do e-mail de convite B2B - Azure Active Directory | Microsoft Docs
description: Modelo de email de convite para colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407195"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Os elementos do email de convite para colaboração B2B – Azure Active Directory

Emails de convite são um componente essencial para ingressar parceiros como usuários de colaboração B2B no Azure AD. Embora não seja [necessário que você envie um e-mail para convidar alguém usando a colaboração B2B,](add-user-without-invite.md)isso dá ao usuário todas as informações necessárias para tomar uma decisão sobre se aceita seu convite. Também lhes dá um link a que eles sempre podem se referir no futuro, quando precisarem retornar aos seus recursos.

![Captura de tela mostrando o email do convite B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Este novo modelo de e-mail ainda está sendo implementado para todos os inquilinos, então alguns inquilinos ainda estão usando um design mais antigo. Até o final de maio de 2020, os convites de todos os inquilinos estarão usando este modelo.

## <a name="explaining-the-email"></a>Explicação do email

Vamos examinar alguns elementos do email para que você conheça a melhor maneira de usar esses recursos.

### <a name="subject"></a>Assunto

O assunto do e-mail segue este padrão:

&lt;o&gt; nome de usuário convidou você a acessar aplicativos dentro de sua organização.

### <a name="from-address"></a>Do endereço

Usamos um padrão parecido com o do LinkedIn para o endereço De. Esse padrão deve deixar claro que, invites@microsoft.comembora o e-mail venha, o convite é de outra organização. O formato é: <invites@microsoft.com> Microsoft Invitations ou &lt;Microsoft&gt; <invites@microsoft.com>convites em nome do nome do inquilino . 

### <a name="reply-to"></a>Responder Para

O email para resposta é definido como o email do emissor do convite, quando houver um disponível, para que a resposta envie um email ao emissor do convite.

### <a name="phishing-warning"></a>Aviso de phishing

O e-mail começa com um breve aviso ao usuário sobre phishing, alertando-o de que eles só devem aceitar convites que estão esperando. É uma boa prática garantir que os parceiros que você está convidando não serão surpreendidos pelo seu convite, mencionando-o com antecedência.

![Imagem do aviso de phishing no e-mail](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informações sobre o emissor do convite

O e-mail inclui informações sobre o convidado e a organização da quem estão enviando o convite. Isso inclui o nome do remetente e o endereço de e-mail, bem como o nome e o domínio principal associados à organização. Todas essas informações devem ajudar o convidado a tomar uma decisão informada sobre a aceitação do convite.

![Imagem das informações do convidado no e-mail](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Mensagem de convite

Se o convidado incluir uma mensagem como parte de seu convite quando convidar [um usuário convidado para o diretório, grupo ou aplicativo](add-users-administrator.md) ou quando eles usam a [API de convite,](customize-invitation-api.md)a mensagem é destacada na seção principal do e-mail. Também estão incluídos o nome e a imagem do perfil do convidado, se eles definirem um. A mensagem em si é uma área de texto, então, por razões de segurança, ela não processa tags HTML.

![Imagem da mensagem de convite no e-mail](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Aceitar botão e redirecionar URL

A próxima seção do e-mail contém informações sobre onde o convidado será levado após aceitar o convite, bem como um botão para fazê-lo.  No futuro, o convidado pode sempre usar este link para retornar diretamente aos seus recursos.

![Imagem do botão aceitar e redirecionar url no e-mail](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Seção de rodapé

O rodapé contém mais informações sobre o convite que está sendo enviado. Há sempre uma opção para o convidado bloquear convites futuros. Se a organização [tiver definido uma declaração de privacidade,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)o link para a declaração será exibido aqui.  Caso contrário, uma nota indica que a organização não definiu uma declaração de privacidade.

![Imagem da seção rodapé no e-mail](media/invitation-email-elements/footer-section.png)
 
## <a name="how-the-language-is-determined"></a>Como o idioma é determinado

O idioma apresentado ao usuário convidado no email de convite é determinado pelas configurações a seguir. Elas estão listadas na ordem de precedência. Se uma configuração não estiver definida, a próxima configuração na lista determinará o idioma.

- A propriedade **messageLanguage** do objeto [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) quando a API de criação de convite é usada
-   A propriedade **preferredLanguage** especificada no [objeto de usuário](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) do convidado
-   O **Idioma de notificação** definido nas propriedades do locatário da página inicial do usuário convidado (somente para locatários do Azure AD)
-   O **Idioma de notificação** definido nas propriedades do locatário do recurso

Se nenhuma das definições mencionadas estiver configurada, o idioma será inglês (EUA) por padrão.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD](what-is-b2b.md)
- [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicionar usuários de colaboração B2B sem um convite](add-user-without-invite.md)
