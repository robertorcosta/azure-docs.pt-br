---
title: Modelo de aplicativo | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o processo de registro de seu aplicativo para que ele possa se integrar com a plataforma de identidade da Microsoft (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 774822eae5ab327f57da3eca4499ca14d3e0c7a1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584305"
---
# <a name="application-model"></a>Modelo de aplicativo

Os aplicativos podem conectar os próprios usuários ou delegar a entrada para um provedor de identidade. Este tópico discute as etapas necessárias para registrar um aplicativo com a plataforma de identidade da Microsoft.

## <a name="registering-an-application"></a>Registrar um aplicativo

Para que um provedor de identidade saiba que um usuário tem acesso a um aplicativo específico, o usuário e o aplicativo devem ser registrados com o provedor de identidade. Ao registrar seu aplicativo com o Azure AD, você está fornecendo uma configuração de identidade para seu aplicativo que permite que ele se integre à plataforma de identidade da Microsoft. O registro do aplicativo também permite que você:

* Personalize a identidade visual do seu aplicativo na caixa de diálogo de entrada. Isso é importante porque essa é a primeira experiência que um usuário terá com seu aplicativo.
* Decida se deseja permitir que os usuários se conectem somente se eles pertencem à sua organização. Este é um aplicativo de locatário único. Ou permitir que os usuários entrem usando qualquer conta corporativa ou de estudante. Este é um aplicativo multilocatário. Você também pode permitir contas pessoais da Microsoft ou uma conta social do LinkedIn, do Google e assim por diante.
* Permissões de escopo de solicitação. Por exemplo, você pode solicitar o escopo "User. Read", que concede permissão para ler o perfil do usuário conectado.
* Defina escopos que definem o acesso à sua API Web. Normalmente, quando um aplicativo deseja acessar sua API, ele precisa solicitar permissões para os escopos que você definir.
* Compartilhe um segredo com a plataforma de identidade da Microsoft que comprova a identidade do aplicativo.  Isso é relevante no caso em que o aplicativo é um aplicativo cliente confidencial. Um aplicativo cliente confidencial é um aplicativo que pode conter credenciais com segurança. Eles exigem um servidor de back-end confiável para armazenar as credenciais.

Depois de registrado, o aplicativo receberá um identificador exclusivo que o aplicativo compartilha com a plataforma de identidade da Microsoft quando solicitar tokens. Se o aplicativo for um [aplicativo cliente confidencial](developer-glossary.md#client-application), ele também compartilhará o segredo ou a chave pública, dependendo se os certificados ou segredos foram usados.

A plataforma de identidade da Microsoft representa aplicativos que usam um modelo que atende a duas funções principais:

* Identificar o aplicativo pelos protocolos de autenticação aos quais ele dá suporte
* Forneça todos os identificadores, URLs, segredos e informações relacionadas que são necessários para autenticar

Plataforma de identidade da Microsoft:

* Mantém todos os dados necessários para dar suporte à autenticação em tempo de execução
* Mantém todos os dados para decidir quais recursos um aplicativo pode precisar acessar e em que circunstâncias uma determinada solicitação deve ser atendida
* Fornece a infraestrutura para implementar o provisionamento de aplicativos dentro do locatário do desenvolvedor do aplicativo e para qualquer outro locatário do Azure AD
* Manipula o consentimento do usuário durante o tempo de solicitação de token e facilita o provisionamento dinâmico de aplicativos entre locatários

**Consentimento** é o processo de um proprietário de recurso que concede autorização para um aplicativo cliente acessar recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. Plataforma de identidade da Microsoft:

* Permite aos usuários e administradores conceder ou negar de forma dinâmica o consentimento ao aplicativo para o acesso de recursos em seu nome.
* Permite aos administradores decidir, em última análise, o que os aplicativos têm permissão para fazer, quais usuários podem usar aplicativos específicos e como os recursos do diretório são acessados.

## <a name="multi-tenant-apps"></a>Aplicativos multilocatário

Na plataforma Microsoft Identity, um [objeto Application](developer-glossary.md#application-object) descreve um aplicativo. No momento da implantação, a plataforma de identidade da Microsoft usa o objeto Application como um plano gráfico para criar uma [entidade de serviço](developer-glossary.md#service-principal-object), que representa uma instância concreta de um aplicativo dentro de um diretório ou locatário. A entidade de serviço define o que o aplicativo pode realmente fazer em um diretório de destino específico, que pode usá-lo, a quais recursos ele tem acesso e assim por diante. A plataforma de identidade da Microsoft cria uma entidade de serviço com base em um objeto de aplicativo por meio do [consentimento](developer-glossary.md#consent).

O diagrama a seguir mostra um fluxo de provisionamento simplificado da plataforma de identidade da Microsoft orientado por consentimento. Ele mostra dois locatários: *A* e *B*.

* O *locatário* a possui o aplicativo.
* O *locatário B* está instanciando o aplicativo por meio de uma entidade de serviço.

![Fluxo de provisionamento simplificado orientado por consentimento](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de provisionamento:

1. Um usuário do locatário B tenta entrar com o aplicativo; o ponto de extremidade da autorização solicita um token para o aplicativo.
1. As credenciais do usuário são adquiridas e verificadas quanto à autenticação.
1. O usuário é solicitado a fornecer consentimento para que o aplicativo tenha acesso ao locatário B.
1. A plataforma de identidade da Microsoft usa o objeto Application no locatário A como um plano gráfico para criar uma entidade de serviço no locatário B.
1. O usuário recebe o token solicitado.

Você pode repetir esse processo para locatários adicionais. O locatário A mantém o blueprint para o aplicativo (objeto de aplicativo). Usuários e administradores de todos os outros locatários em que o aplicativo recebe consentimento, mantêm o controle sobre o que o aplicativo tem permissão para fazer por meio do objeto de entidade de serviço correspondente em cada locatário. Para obter mais informações, confira [Objetos de entidade de serviço e aplicativo na plataforma de identidade da Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos que abrangem noções básicas de autenticação e autorização:

* Consulte [autenticação vs. autorização](authentication-vs-authorization.md) para saber mais sobre os conceitos básicos de autenticação e autorização na plataforma Microsoft Identity.
* Consulte [tokens de segurança](security-tokens.md) para saber como tokens de acesso, tokens de atualização e tokens de ID são usados em autenticação e autorização.
* Consulte [fluxo de entrada do aplicativo](app-sign-in-flow.md) para saber mais sobre o fluxo de entrada de aplicativos Web, de área de trabalho e móveis na plataforma Microsoft Identity.

Para saber mais sobre o modelo de aplicativo:

* Veja [como e por que os aplicativos são adicionados ao Azure ad](active-directory-how-applications-are-added.md) para obter mais informações sobre objetos de aplicativo e entidades de serviço na plataforma Microsoft Identity.
* Consulte [locação in Azure Active Directory](single-and-multi-tenant-apps.md) para obter mais informações sobre aplicativos de locatário único e aplicativos multilocatários.
* Consulte a [documentação Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) para obter mais informações sobre como o AD do Azure também fornece Azure Active Directory B2C para que as organizações possam conectar usuários, normalmente clientes, usando identidades sociais, como uma conta do Google.
