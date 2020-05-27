---
title: Visão geral da verificação do editor – Plataforma de identidade da Microsoft | Azure
description: Fornece uma visão geral do programa de verificação do editor (versão prévia) para a plataforma de identidade da Microsoft. Lista os benefícios, os requisitos do programa e as perguntas frequentes. Quando um aplicativo é marcado como verificado pelo editor, isso significa que o editor verificou sua identidade usando uma conta do Microsoft Partner Network que concluiu o processo de verificação e associou essa conta do MPN ao registro de aplicativo.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 73a96f295d5dfa74130927e5096e9278a0e348e8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682432"
---
# <a name="publisher-verification-preview"></a>Verificação do editor (versão prévia)

A verificação do editor (versão prévia) ajuda administradores e usuários finais a entender a autenticidade dos desenvolvedores de aplicativos que se integram com a plataforma de identidade da Microsoft. Quando um aplicativo é marcado como verificado pelo editor, isso significa que o editor verificou sua identidade usando uma conta do [Microsoft Partner Network](https://partner.microsoft.com/membership) que concluiu a [verificação](/partner-center/verification-responses). 

Um selo azul "verificado" aparece na solicitação de consentimento do Azure AD e em outras telas: ![Solicitação de consentimento](./media/publisher-verification-overview/consent-prompt.png)

Esse recurso destina-se principalmente a desenvolvedores que criam aplicativos multilocatários que utilizam o [OAuth 2.0 e o OpenID Connect](active-directory-v2-protocols.md) com a [plataforma de identidade da Microsoft](v2-overview.md). Esses aplicativos podem conectar usuários usando o OpenID Connect ou podem usar o OAuth 2.0 para solicitar acesso a dados usando APIs como o [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Benefícios
A verificação do editor fornece os seguintes benefícios:
- **Aumento da transparência e redução de riscos para os clientes** — esta funcionalidade ajuda os clientes a entender quais aplicativos usados em suas organizações são publicados pelos desenvolvedores em quem eles confiam. 

- **Melhoria da identidade visual** - um selo "verificado" é exibido na [solicitação de consentimento](application-consent-experience.md) do Azure AD, na página de aplicativos empresariais e nas superfícies de UX adicionais usadas pelos usuários finais e administradores. 

- **Adoção de empresas de forma mais tranquila** - os administradores podem configurar novas políticas de consentimento de usuário e o status de verificação do editor será um dos critérios de política primária. 

- **Avaliação de risco aprimorada** - as detecções da Microsoft para solicitações de consentimento "arriscadas" incluirão a verificação do editor como um sinal. 

## <a name="requirements"></a>Requisitos
Há alguns pré-requisitos para a verificação do editor, alguns dos quais já terão sido concluídos por muitos parceiros da Microsoft. Eles são: 

-  Uma ID de MPN para uma conta do [Microsoft Partner Network](https://partner.microsoft.com/membership) válida que concluiu o processo de [verificação](/partner-center/verification-responses). Essa conta MPN deve ser a [PGA (Conta do Parceiro Globlal)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) da sua organização. 

-  Um locatário do Azure AD com o [domínio personalizado](/azure/active-directory/fundamentals/add-custom-domain) verificado pelo DNS. O domínio personalizado deve corresponder ao domínio do endereço de email usado durante a verificação na etapa anterior. 

-  Um aplicativo registrado em um locatário do Azure AD, com um [Domínio do Editor](howto-configure-publisher-domain.md) configurado usando o mesmo domínio usado anteriormente. 

-  O usuário que está realizando a verificação deve ser autorizado a fazer alterações no registro do aplicativo no Azure AD e na conta do MPN no Partner Center. 

    -  No Azure Active Directory, esse usuário deve ser o proprietário do aplicativo ou ter uma das seguintes [funções](/azure/active-directory/users-groups-roles/directory-assign-admin-roles): Administrador de aplicativos, administrador de aplicativos de nuvem, administrador global. 

    -  No Partner Center, esse usuário deve ter as seguintes [funções](/partner-center/permissions-overview): Administrador de MPN, administrador de contas ou um administrador global (essa é uma função compartilhada controlada no Azure Active Directory).
    
-  O editor concorda com os [termos de uso da plataforma Microsoft Identity para desenvolvedores](/legal/microsoft-identity-platform/terms-of-use).

Os desenvolvedores que já atenderam a esses pré-requisitos podem ser verificados em questão de minutos. Se os requisitos não tiverem sido atendidos, a configuração será gratuita. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes 
Abaixo estão algumas perguntas frequentes sobre o programa de verificação do editor. Para consultar as perguntas frequentes relacionadas aos requisitos e ao processo, acesse [marcar um aplicativo como editor verificado](mark-app-as-publisher-verified.md).

- **Quais informações a verificação do editor __não__ fornece?**  Quando um aplicativo é marcado como editor verificado, isso não indica que o aplicativo ou seu editor obteve certificações específicas, ou que está em conformidade com os padrões do setor, obedece às práticas recomendadas, etc. Outros programas da Microsoft fornecem essas informações, incluindo a [Certificação do Aplicativo Microsoft 365](/microsoft-365-app-certification/overview).

- **Quanto isso custa? Ele requer alguma licença?** A Microsoft não cobra os desenvolvedores pela verificação do editor e não requer nenhuma licença específica. 

- **Como isso está relacionado com o Atestado de Editor do Microsoft 365? E quanto à Certificação de Aplicativo Microsoft 365?** Esses são programas complementares que os desenvolvedores podem usar para criar aplicativos confiáveis que podem ser adotados com segurança pelos clientes. A verificação do editor é a primeira etapa desse processo e deve ser concluída por todos os desenvolvedores que criam aplicativos que atendem aos critérios acima. 

  Os desenvolvedores que também estão se integrando ao Microsoft 365 podem receber benefícios adicionais desses programas. Para obter mais informações, consulte o [Atestado de Editor do Microsoft 365](/microsoft-365-app-certification/docs/attestation) e a [Certificação de Aplicativo do Microsoft 365](/microsoft-365-app-certification/docs/certification). 

- **Isso é a mesma coisa que a Galeria de aplicativos do Azure AD?** Não. A verificação de editor é um programa complementar, mas separado, para a [galeria de aplicativos do Azure Active Directory](/azure/active-directory/azuread-dev/howto-app-gallery-listing). Os desenvolvedores que se adequam aos critérios acima devem concluir o processo de verificação do editor independentemente da participação nesse programa. 

## <a name="next-steps"></a>Próximas etapas
* Saiba como [marcar um aplicativo como editor verificado](mark-app-as-publisher-verified.md).
* [Solucionar problemas](troubleshoot-publisher-verification.md) de verificação do editor.