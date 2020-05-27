---
title: Provedores de identidade para Identidades Externas – Azure AD
description: A colaboração B2B do Azure Active Directory dá suporte à autenticação multifator (MF) para acesso seletivo aos seus aplicativos corporativos
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595705"
---
# <a name="identity-providers-for-external-identities"></a>Provedores de identidade para Identidades Externas

Um *provedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicativos. Ao compartilhar seus aplicativos e recursos com usuários externos, o Azure AD é o provedor de identidade padrão de compartilhamento. Isso significa que, quando você convida usuários externos que já têm uma conta do Azure AD ou da Microsoft, eles podem entrar automaticamente sem precisar de configuração adicional.

No entanto, você pode permitir que os usuários entrem com vários provedores de identidade. Por exemplo, você pode configurar a federação com provedores de identidade social compatíveis com o Azure AD, incluindo o Google e o Facebook. Você também pode fazer a federação com qualquer provedor de identidade externo compatível com o protocolo SAML ou WS-Fed. Com a federação do provedor de identidade externa, você pode oferecer aos usuários externos a capacidade de entrar em seus aplicativos com suas contas de redes sociais ou empresariais existentes.

## <a name="how-it-works"></a>Como ele funciona

O serviço Identidades Externas do Azure AD está pré-configurado para federação com o Google e o Facebook. Para configurar esses provedores de identidade em seu locatário do Azure AD, você criará um aplicativo em cada provedor de identidade e configurará as credenciais. Você obterá uma ID de cliente ou de aplicativo e um segredo de cliente ou de aplicativo, que poderá então adicionar ao seu locatário do Azure AD.

Depois de adicionar um provedor de identidade ao seu locatário do Azure AD:

- Quando você convidar um usuário externo para aplicativos ou recursos em sua organização, o usuário externo pode entrar usando a própria desse provedor de identidade.
- Quando você habilitar a [inscrição por autoatendimento](self-service-sign-up-overview.md) para seus aplicativos, os usuários externos poderão se inscrever em seus aplicativos usando as próprias contas junto aos provedores de identidade que você adicionou. 

Ao resgatar seu convite ou se inscrever no seu aplicativo, o usuário externo terá a opção de entrar e se autenticar no provedor de identidade social:

![Captura de tela mostrando as opções de credenciais do Google e do Facebook](media/identity-providers/sign-in-with-social-identity.png)

Para obter uma experiência de entrada ideal, faça a federação com provedores de identidade sempre que possível para que você possa dar aos seus convidados uma experiência de conexão perfeita ao acessar seus aplicativos.  

## <a name="next-steps"></a>Próximas etapas

Para saber como adicionar provedores de identidade para entrar em seus aplicativos, consulte os seguintes artigos:

- [Adicionar o Google](google-federation.md) à sua lista de provedores de identidade social
- [Adicionar o Facebook](facebook-federation.md) à sua lista de provedores de identidade social
- [Configure a federação direta](direct-federation.md) com qualquer organização cujo provedor de identidade dê suporte ao protocolo SAML 2.0 ou WS-Fed. A federação direta não é uma opção para fluxos de usuário com inscrição por autoatendimento.
