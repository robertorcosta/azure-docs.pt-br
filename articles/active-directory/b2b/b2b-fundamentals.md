---
title: As melhores práticas e recomendações do Azure Active Directory B2B
description: Aprenda as melhores práticas e recomendações para acesso do usuário convidado business-to-business (B2B) no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050856"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Práticas recomendadas do Azure Active Directory B2B
Este artigo contém recomendações e práticas recomendadas para a colaboração business-to-business (B2B) no Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **A partir de 31 de março de 2021**, a Microsoft não suportará mais o resgate de convites criando contas AD azure não gerenciadas e inquilinos para cenários de colaboração B2B. Na preparação, encorajamos os clientes a optar pela [autenticação única de senha por e-mail.](one-time-passcode.md) Damos as boas-vindas ao seu feedback sobre este recurso de pré-visualização pública e estamos entusiasmados em criar ainda mais maneiras de colaborar.

## <a name="b2b-recommendations"></a>Recomendações B2B
| Recomendação | Comentários |
| --- | --- |
| Para uma experiência de login ideal, federação com provedores de identidade | Sempre que possível, federa diretamente com os provedores de identidade para permitir que os usuários convidados entrem em seus aplicativos e recursos compartilhados sem ter que criar contas Microsoft (MSAs) ou Azure AD. Você pode usar o [recurso da federação](google-federation.md) do Google para permitir que usuários convidados B2B entrem com suas contas do Google. Ou, você pode usar o [recurso Direct federation (preview)](direct-federation.md) para configurar a federação direta com qualquer organização cujo provedor de identidade (IdP) suporte o protocolo SAML 2.0 ou WS-Fed. |
| Use o recurso Desenha única (visualização) de e-mail para hóspedes B2B que não podem autenticar por outros meios | O recurso [de senha única (visualização)](one-time-passcode.md) autentica os usuários convidados B2B quando eles não podem ser autenticados por outros meios como o Azure AD, uma conta Microsoft (MSA) ou a federação do Google. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, ele digita esse código para continuar o processo de entrada. |
| Adicionar identidade visual da empresa à página de entrada | Você pode personalizar sua página de login para que seja mais intuitiva para seus usuários convidados B2B. Veja como adicionar a [marca da empresa para fazer login e acessar páginas do Painel](../fundamentals/customize-branding.md). |
| Adicione sua declaração de privacidade à experiência de resgate do usuário convidado B2B | Você pode adicionar a URL da declaração de privacidade da sua organização ao processo de resgate de convites pela primeira vez, de modo que um usuário convidado deve consentir com seus termos de privacidade para continuar. Veja [como fazer: Adicione as informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement). |
| Use o recurso de convite em massa (visualização) para convidar vários usuários convidados B2B ao mesmo tempo | Convide vários usuários convidados para sua organização ao mesmo tempo usando o recurso de visualização de convites em massa no portal Azure. Esse recurso permite que você carregue um arquivo CSV para criar usuários convidados B2B e enviar convites em massa. Consulte [Tutorial para usuários B2B convidativos em massa](tutorial-bulk-invite.md). |
| Impor políticas de acesso condicional para autenticação multifatorial (MFA) | Recomendamos aplicar políticas de MFA nos aplicativos que você deseja compartilhar com usuários B2B parceiros. Desta forma, o MFA será consistentemente aplicado nos aplicativos do seu inquilino, independentemente de a organização parceira estar usando o MFA. Consulte [O Acesso Condicional para usuários de colaboração B2B](conditional-access.md). |
| Se você estiver aplicando políticas de acesso condicional baseadas em dispositivos, use listas de exclusão para permitir acesso a usuários B2B | Se as políticas de acesso condicional baseadas em dispositivos estiverem habilitadas em sua organização, os dispositivos de usuário convidados B2B serão bloqueados porque não são gerenciados pela sua organização. Você pode criar listas de exclusão contendo usuários parceiros específicos para excluí-los da política de acesso condicional baseado em dispositivos. Consulte [O Acesso Condicional para usuários de colaboração B2B](conditional-access.md). |
| Use uma URL específica do inquilino ao fornecer links diretos para seus usuários convidados B2B | Como alternativa ao e-mail do convite, você pode dar a um convidado um link direto para o seu aplicativo ou portal. Este link direto deve ser específico do inquilino, o que significa que ele deve incluir um ID de inquilino ou domínio verificado para que o hóspede possa ser autenticado em seu inquilino, onde o aplicativo compartilhado está localizado. Veja [a experiência de Redenção para o usuário convidado](redemption-experience.md). |
| Ao desenvolver um aplicativo, use UserType para determinar a experiência do usuário convidado  | Se você está desenvolvendo um aplicativo e deseja fornecer experiências diferentes para usuários inquilinos e usuários convidados, use a propriedade UserType. A reivindicação UserType não está incluída no token no momento. Os aplicativos devem usar a API do Microsoft Graph para consultar o diretório para que o usuário obtenha seu UserType. |
| Alterar a propriedade UserType *somente* se o relacionamento do usuário com a organização mudar | Embora seja possível usar o PowerShell para converter a propriedade UserType para um usuário de Membro para Convidado (e vice-versa), você deve alterar essa propriedade somente se a relação do usuário com sua organização mudar. Consulte [Propriedades de um usuário convidado B2B](user-properties.md).|

## <a name="next-steps"></a>Próximas etapas

[Gerenciar compartilhamento B2B](delegate-invitations.md)
