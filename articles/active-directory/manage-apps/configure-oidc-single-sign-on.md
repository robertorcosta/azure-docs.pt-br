---
title: Entenda o SSO (logon único) baseado em OIDC para aplicativos no Azure Active Directory
description: Entenda o SSO (logon único) baseado em OIDC para aplicativos no Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 487bf3c83be16ee80838b3bbe8bf8532eb6ffe12
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780052"
---
# <a name="understand-oidc-based-single-sign-on"></a>Entender o logon único baseado em OIDC
Na [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos, você aprendeu a usar o Azure ad como o IDP (provedor de identidade) para um aplicativo. Este artigo apresenta mais detalhes sobre os aplicativos que usam o padrão OpenID Connect para implementar o logon único. 

## <a name="before-you-begin"></a>Antes de começar
O processo de adicionar um aplicativo ao seu locatário de Azure Active Directory depende do tipo de logon único implementado pelo aplicativo. Para saber mais sobre as opções de logon único disponíveis para aplicativos que podem usar o Azure AD para gerenciamento de identidade, consulte [Opções de logon único](sso-options.md). Este artigo aborda os aplicativos baseados em OIDC.


## <a name="basic-oidc-configuration"></a>Configuração básica de OIDC
Na [série de início rápido](add-application-portal-setup-oidc-sso.md), há um artigo sobre como configurar o logon único. Nele, você aprende a adicionar um aplicativo baseado em OIDC ao seu locatário do Azure.

A boa coisa com a adição de um aplicativo que usa o padrão OIDC para logon único é que a configuração é mínima. Aqui está um breve vídeo que mostra como adicionar um aplicativo baseado em OIDC ao seu locatário.

Adicionando um aplicativo baseado em OIDC no Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Para saber mais sobre o consentimento do usuário e do administrador, consulte [entender o consentimento do usuário e do administrador](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Próximas etapas

- [Série de Início Rápido no Gerenciamento de Aplicativos](add-application-portal-setup-oidc-sso.md)
- [Opções de logon único](sso-options.md)
- [Como: Entrar em qualquer usuário do Azure Active Directory usando o padrão de aplicativo multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md)
