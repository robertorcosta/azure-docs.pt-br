---
title: Autenticação de área de trabalho virtual do Windows – Azure
description: Métodos de autenticação para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038452"
---
# <a name="supported-authentication-methods"></a>Métodos de autenticação com suporte

Neste artigo, daremos uma breve visão geral de quais tipos de autenticação você pode usar na área de trabalho virtual do Windows.

## <a name="session-host-authentication"></a>Autenticação de host de sessão

A área de trabalho virtual do Windows dá suporte ao NTLM (NT LAN Manager) e ao Kerberos para autenticação de host de sessão. No entanto, para usar o Kerberos, o cliente precisa obter tíquetes de segurança Kerberos de um serviço centro de distribuição de chaves (KDC) em execução em um controlador de domínio. Para obter tíquetes, o cliente precisa de uma linha de visão direta para o controlador de domínio. Você pode obter uma linha de visão direta usando sua rede corporativa. Você também pode usar uma conexão VPN para sua rede corporativa.

Estes são os métodos de entrada com suporte no momento:

- Cliente da Área de trabalho do Windows
    - Nome de usuário e senha
    - Cartão inteligente
    - Windows Hello
- Cliente para Microsoft Store
    - Nome de usuário e senha
- Cliente Web
    - Nome de usuário e senha
- Android
    - Nome de usuário e senha
- iOS
    - Nome de usuário e senha
- macOS
    - Nome de usuário e senha

>[!NOTE]
>O cartão inteligente e o Windows Hello só podem usar o Kerberos para entrar. Entrar com o Kerberos requer a linha de visão para o controlador de domínio.

## <a name="single-sign-on-sso"></a>Logon único (SSO)

A área de trabalho virtual do Windows atualmente não dá suporte a Serviços de Federação do Active Directory (AD FS) (ADFS) para autenticação ou SSO.

A única maneira de evitar a solicitação de suas credenciais para o host de sessão é salvá-las no cliente. Recomendamos que você faça isso apenas com dispositivos seguros para impedir que outros usuários acessem seus recursos.

## <a name="next-steps"></a>Próximas etapas

Curioso sobre outras maneiras de manter sua implantação segura? Confira [as práticas recomendadas de segurança](security-guide.md).
