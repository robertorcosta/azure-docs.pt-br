---
title: Crie um aplicativo daemon que chama APIs web - plataforma de identidade da Microsoft | Azure
description: Aprenda a construir um aplicativo daemon que chama APIs da Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df06c4c55941f4424d6b90d2846af17bf055b2e4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885456"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicativo Daemon que chama APIs da Web

Aprenda tudo o que você precisa para construir um aplicativo daemon que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Visão geral

Seu aplicativo pode adquirir um token para chamar uma API web em nome de si mesmo (não em nome de um usuário). Este cenário é útil para aplicações daemon. Ele usa a concessão padrão de [credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0.

![Aplicativos Daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de uso para aplicativos daemon:

- Aplicações web que são usadas para provisionar ou administrar usuários ou fazer processos em lote em um diretório
- Aplicativos de desktop (como serviços do Windows no Windows ou processos daemon no Linux) que realizam trabalhos em lote, ou um serviço de sistema operacional que é executado em segundo plano
- APIs da Web que precisam manipular diretórios, não usuários específicos

Há outro caso comum em que aplicativos não-daemon usam credenciais de clientes: mesmo quando agem em nome dos usuários, eles precisam acessar uma API web ou um recurso sob sua própria identidade por razões técnicas. Um exemplo é o acesso a segredos no Azure Key Vault ou em um banco de dados SQL do Azure para um cache.

Aplicações que adquirem um token para suas próprias identidades:

- São aplicativos confidenciais para clientes. Esses aplicativos, uma vez que acessam recursos independentemente dos usuários, precisam provar sua identidade. Eles também são aplicativos bastante sensíveis. Eles precisam ser aprovados pelos administradores de inquilinos do Azure Active Directory (Azure AD).
- Ter registrado um segredo (senha de aplicativo ou certificado) com o Azure AD. Este segredo é passado durante a chamada para a Azure AD para obter um token.

## <a name="specifics"></a>Especificidades

> [!IMPORTANT]
>
> - Os usuários não podem interagir com um aplicativo daemon. Uma aplicação de daemon requer sua própria identidade. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando seu ID de aplicativo, credencial (senha ou certificado) e iD de aplicativo URI para a Azure AD. Após autenticação bem-sucedida, o daemon recebe um token de acesso (e um token de atualização) do ponto final da plataforma de identidade da Microsoft. Esse token é então usado para chamar a API da Web (e é atualizado conforme necessário).
> - Como os usuários não podem interagir com aplicativos daemon, o consentimento incremental não é possível. Todas as permissões de API necessárias precisam ser configuradas no registro do aplicativo. O código do aplicativo apenas solicita permissões estáticas definidas. Isso também significa que os aplicativos daemon não suportarão o consentimento incremental.

Para desenvolvedores, a experiência de ponta a ponta para este cenário tem os seguintes aspectos:

- Os aplicativos Daemon só podem funcionar em inquilinos Azure AD. Não faria sentido construir um aplicativo daemon que tenta manipular contas pessoais da Microsoft. Se você é um desenvolvedor de aplicativos de linha de negócios (LOB), você criará seu aplicativo daemon em seu inquilino. Se você é um ISV, você pode querer criar um aplicativo daemon multilocatário. Cada inquilino será o que precisará para dar consentimento.
- Durante o registro da [inscrição,](./scenario-daemon-app-registration.md)o URI de resposta não é necessário. Você precisa compartilhar segredos ou certificados ou afirmações assinadas com o Azure AD. Você também precisa solicitar permissões de solicitação e conceder consentimento do admin para usar essas permissões de aplicativo.
- A [configuração do aplicativo](./scenario-daemon-app-configuration.md) precisa fornecer credenciais do cliente compartilhadas com o Azure AD durante o registro do aplicativo.
- O [escopo](scenario-daemon-acquire-token.md#scopes-to-request) usado para adquirir um token com o fluxo de credenciais do cliente precisa ser um escopo estático.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo Daemon - registro de aplicativos](./scenario-daemon-app-registration.md)
