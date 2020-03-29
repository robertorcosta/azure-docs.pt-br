---
title: Suporte a Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Conheça o suporte ao AD FS (Active Directory Federation Services, serviços de federação de diretórios ativos) na Biblioteca de Autenticação microsoft para Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699539"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Suporte aos Serviços ativos da Federação de Diretórios no MSAL para Python

O Active Directory Federation Services (AD FS) no Windows Server permite adicionar o OpenID Connect e a autenticação e autorização baseadas no OAuth 2.0 aos seus aplicativos usando a Microsoft Authentication Library (MSAL) para Python. Usando a biblioteca MSAL for Python, seu aplicativo pode autenticar usuários diretamente contra AD FS. Para obter mais informações sobre cenários, consulte [AD FS Scenarios for Developers](/windows-server/identity/ad-fs/ad-fs-development).

Geralmente existem duas maneiras de autenticar contra a AD FS:

- O MSAL Python conversa com o Azure Active Directory, que por si só é federado com outros provedores de identidade. A federação acontece através do AD FS. O MSAL Python se conecta ao Azure AD, que sinaliza em usuários que são gerenciados no Azure AD (usuários gerenciados) ou usuários gerenciados por outro provedor de identidade, como o AD FS (usuários federados). O MSAL Python não sabe que um usuário é federado. Ele simplesmente fala com a Azure AD. A [autoridade](msal-client-application-configuration.md#authority) que você usa neste caso é a autoridade usual (nome de hospedagem de autoridade + inquilino, comum ou organizações).
- MSAL Python fala diretamente com uma autoridade da AD FS. Isso só é suportado pelo AD FS 2019 e posteriormente.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Conecte-se ao Active Directory federado com AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquira um token interativamente para um usuário federado

O seguinte se aplica se você se conecta diretamente aos Serviços de Federação de Diretórios Ativos (AD FS) ou através do Active Directory.

Quando você `acquire_token_by_authorization_code` `acquire_token_by_device_flow`liga ou , a experiência do usuário é tipicamente da seguinte forma:

1. O usuário insere seu ID de conta.
2. O Azure AD exibe brevemente a mensagem "Levando você para a página da sua organização" e o usuário é redirecionado para a página de login do provedor de identidade. A página de login geralmente é personalizada com o logotipo da organização.

As versões AD FS suportadas neste cenário federado são:
- Serviços ativos da Federação de Diretórios FS v2
- Serviços de Federação de Diretórios Ativos v3 (Windows Server 2012 R2)
- Serviços de Federação de Diretórios Ativos v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Adquira um token via nome de usuário e senha

O seguinte se aplica se você se conecta diretamente aos Serviços de Federação de Diretórios Ativos (AD FS) ou através do Active Directory.

Quando você adquire um `acquire_token_by_username_password`token usando, o MSAL Python faz com que o provedor de identidade entre em contato com base no nome de usuário. O MSAL Python recebe um [token SAML 1.1](reference-saml-tokens.md) do provedor de identidade, que ele fornece ao Azure AD, que retorna o JSON Web Token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Conectando-se diretamente ao AD FS

Quando você conectar diretório ao AD FS, a autoridade que você vai querer usar para construir seu aplicativo será algo como`https://somesite.contoso.com/adfs/`

O MSAL Python suporta o ADFS 2019.

Ele não suporta uma conexão direta com a ADFS 2016 ou a ADFS v2. Se você precisar suportar cenários que exijam uma conexão direta com o ADFS 2016, use a versão mais recente do ADAL Python. Depois de atualizar seu sistema local para o ADFS 2019, você pode usar o MSAL Python.

## <a name="next-steps"></a>Próximas etapas

- Para o caso federado, consulte [Configurar o login ativo do Azure no comportamento de um aplicativo usando uma diretiva Home Realm Discovery](../manage-apps/configure-authentication-for-federated-users-portal.md)