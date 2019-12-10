---
title: Configuração de sessão e logon único
titleSuffix: Azure AD B2C
description: Configuração de sessão e logon único (SSO) no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950061"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Configuração de sessão e logon único no Azure Active Directory B2C

Esse recurso oferece um controle refinado, [com base em cada fluxo de usuário](active-directory-b2c-reference-policies.md), de:

- Vida útil de sessões dos aplicativos Web gerenciados pelo Azure AD B2C.
- Comportamento de logon único (SSO) em vários aplicativos e políticas no locatário do Azure AD B2C.

## <a name="session-behavior"></a>Comportamento da sessão

O Azure AD B2C dá suporte ao [protocolo de autenticação do OpenID Connect](active-directory-b2c-reference-oidc.md) para habilitar o logon seguro em aplicativos Web. Você pode usar as seguintes propriedades para gerenciar sessões de aplicativos da Web:

- **Duração da sessão do aplicativo Web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação bem-sucedida.
    - Padrão = 1440 minutos.
    - Mínimo (inclusive) = 15 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Tempo limite da sessão do aplicativo da Web**: se essa opção for definida como **Absoluta**, o usuário será forçado a autenticar novamente após o período especificado por **duração da sessão do aplicativo Web (minutos)** decorre. Se essa opção estiver definida como **Rolling** (a configuração padrão), o usuário permanecerá conectado desde que esteja ativo de forma contínua em seu aplicativo Web.

Os casos de uso a seguir são ativados usando estas propriedades:

- Atenda aos requisitos de conformidade e segurança de seu setor definindo adequadamente a vida útil da sessão do aplicativo Web.
- Forçar autenticação após um período de tempo definido durante a interação do usuário com uma parte de alto nível de segurança do seu aplicativo web.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

## <a name="single-sign-on-sso-configuration"></a>Configuração de SSO (Logon Único)

Se você tiver vários aplicativos e fluxos de usuário em seu locatário B2C, poderá gerenciar interações do usuário entre eles usando a propriedade **Configuração de logon único**. Você pode definir a propriedade com uma das seguintes configurações:

- **Locatário** -essa configuração é o padrão. O uso dessa configuração permite a vários aplicativos e fluxos de usuário em seu locatário B2C compartilhar a mesma sessão de usuário. Por exemplo, depois que um usuário faz login em um aplicativo, o usuário também pode fazer login em outro, a Contoso Pharmacy, ao acessá-lo.
- **Aplicação** - Esta configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independente de outros aplicativos. Por exemplo, se você quiser que o usuário entre na Contoso Pharmacy (com as mesmas credenciais), mesmo que o usuário já esteja conectado ao Contoso Shopping, outro aplicativo no mesmo locatário B2C.
- **Política** - Essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que a usam. Por exemplo, se o usuário já tiver feito login e concluído uma etapa de autenticação de vários fatores (MFA), o usuário poderá ter acesso a partes de maior segurança de vários aplicativos, desde que a sessão vinculada ao fluxo de usuário não expire.
- **Desabilitado** – essa configuração orces o usuário a percorrer o fluxo do usuário inteira em cada execução da política.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

