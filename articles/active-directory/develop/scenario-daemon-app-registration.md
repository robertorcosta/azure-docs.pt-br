---
title: Registre aplicativos daemon que chamam de APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo daemon que chama APIs web - registro de aplicativos
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773386"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplicativo Daemon que chama APIs web - registro de aplicativos

Para um aplicativo daemon, aqui está o que você precisa saber quando você registrar o aplicativo.

## <a name="supported-account-types"></a>Tipos de conta com suporte

As aplicações da Daemon só fazem sentido em inquilinos azure AD. Então, ao criar o aplicativo, você precisa escolher uma das seguintes opções:

- **Contas apenas neste diretório organizacional**. Essa escolha é a mais comum porque os aplicativos daemon geralmente são escritos por desenvolvedores de linha de negócios (LOB).
- **Contas em qualquer diretório organizacional**. Você fará essa escolha se você for um ISV fornecendo uma ferramenta de utilidade para seus clientes. Você precisará dos adínisas de seus clientes para aprová-lo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticação - não é necessário responder URI

No caso de seu aplicativo cliente confidencial usar *apenas* o fluxo de credenciais do cliente, o URI de resposta não precisa ser registrado. Não é necessário para a configuração ou construção do aplicativo. O fluxo de credenciais do cliente não o usa.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Permissões de API - permissões de aplicativos e consentimento de admin

Um aplicativo daemon pode solicitar apenas permissões de solicitação para APIs (não permissões delegadas). Na página **de permissões** da API para o registro do aplicativo, depois de selecionado **Adicionar uma permissão** e escolher a família API, escolha **permissões de aplicativo**e, em seguida, selecione suas permissões.

![Permissões de aplicativos e consentimento de admin](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> A API web que você deseja chamar precisa definir *permissões de aplicativos (funções de aplicativo)*, não permissões delegadas. Para obter detalhes sobre como expor tal API, consulte [API web protegida: Registro de aplicativos - quando sua API web é chamada por um aplicativo daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Os aplicativos Daemon exigem que um inquilino admin pré-consentimento para o aplicativo que chama a API web. Os admins do inquilino fornecem esse consentimento na mesma página de permissão da **API,** selecionando **o consentimento do grant admin para *nossa organização* **

Se você é um ISV construindo um aplicativo multilocatário, você deve ler a seção [Implantação - caso de aplicativos daemon multilocatários](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo Daemon - configuração de código de aplicativo](./scenario-daemon-app-configuration.md)
