---
title: Autenticação baseada em senha com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de autenticação baseada em senha com o Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74815976036abafc56f97e622351a1018fe30504
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576925"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Autenticação baseada em senha com Azure Active Directory

O SSO (Sign-On único) baseado em senha usa o processo de autenticação existente para o aplicativo. Quando você habilita o SSO baseado em senha, o Azure Active Directory (Azure AD) coleta, criptografa e armazena com segurança as credenciais do usuário no diretório. O Azure AD fornece o nome de usuário e a senha para o aplicativo quando o usuário tenta entrar.

Escolha SSO baseado em senha quando um aplicativo for autenticado com um nome de usuário e senha em vez de tokens de acesso e cabeçalhos. O SSO baseado em senha dá suporte a qualquer aplicativo baseado em nuvem que tenha uma página de entrada baseada em HTML. 

## <a name="use-when"></a>Usar quando

Você precisa proteger com pré-autenticação e fornecer SSO por meio de compartimentação de senha para aplicativos Web.

![diagrama arquitetônico](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Componentes do sistema

* **Usuário** : acessa o aplicativo com base em forma de meus aplicativos ou visitando diretamente o site. 

* **Navegador da Web** : o componente com o qual o usuário interage para acessar a URL externa do aplicativo. O usuário acessa o aplicativo baseado em formulário por meio da extensão myapps. 

* **Extensão myapps** : identifica o aplicativo SSO baseado em senha configurado e fornece as credenciais para o formulário de entrada. A extensão myapps é instalada no navegador da Web. 

* **AD do Azure** : autentica o usuário.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementar SSO baseado em senha com o Azure AD

* [O que é SSO baseado em senha](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [Configurar SSO baseado em senha para aplicativos de nuvem ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [Configurar o SSO baseado em senha para aplicativos locais com o proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
