---
title: Autenticação na plataforma de identidade da Microsoft | Azure
description: Saiba mais sobre os conceitos básicos de autenticação na plataforma Microsoft Identity (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161734"
---
# <a name="authentication-basics"></a>Noções básicas de autenticação

Este artigo aborda muitos dos conceitos de autenticação que você precisará entender para criar aplicativos Web protegidos, APIs da Web ou aplicativos que chamam APIs da Web protegidas. Se você vir um termo com o qual não está familiarizado, experimente nosso [Glossário](developer-glossary.md) ou nossos [vídeos da plataforma de identidade da Microsoft](identity-videos.md) que abordam os conceitos básicos.

## <a name="authentication-vs-authorization"></a>Autenticação vs. autorização

A **autenticação** é o processo de provar que você é quem diz que está. Às vezes, a autenticação é abreviada para AuthN. A plataforma de identidade da Microsoft implementa o protocolo [OpenID Connect](https://openid.net/connect/) para lidar com a autenticação.

A **autorização** é o ato de conceder uma permissão de parte autenticada para fazer algo. Ele especifica quais dados você tem permissão para acessar e o que você pode fazer com esses dados. Às vezes, a autorização é abreviada para AuthZ. A plataforma de identidade da Microsoft implementa o protocolo [OAuth 2,0](https://oauth.net/2/) para lidar com a autorização.

Em vez de criar aplicativos que mantêm suas próprias informações de nome de usuário e senha, o que provoca uma alta carga administrativa quando você precisa adicionar ou remover usuários em vários aplicativos, os aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure Active Directory (AD do Azure) é um provedor de identidade centralizado na nuvem. A delegação de autenticação e autorização para ela permite cenários como políticas de acesso condicional que exigem que um usuário esteja em um local específico, o uso da autenticação multifator, bem como a habilitação de um usuário a entrar uma vez e, em seguida, ser automaticamente conectado a todos os aplicativos Web que compartilham o mesmo diretório centralizado. Esse recurso é conhecido como **SSO (logon único)**.

A plataforma de identidade da Microsoft simplifica a autenticação e a autorização para desenvolvedores de aplicativos, fornecendo identidade como um serviço, com suporte para protocolos padrão do setor, como OAuth 2,0 e OpenID Connect, bem como bibliotecas de software livre para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Ele permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft, obtenham tokens para chamar [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores criaram. Para obter mais informações, consulte [evolução da plataforma de identidade da Microsoft](about-microsoft-identity-platform.md).

## <a name="security-tokens"></a>Tokens de segurança

Um provedor de identidade centralizado é especialmente importante para aplicativos que têm usuários localizados em todo o mundo que não necessariamente se conectam da rede da empresa. A plataforma de identidade da Microsoft autentica os usuários e fornece tokens de segurança, como [token de acesso](developer-glossary.md#access-token), [token de atualização](developer-glossary.md#refresh-token)e [tokens de ID](developer-glossary.md#id-token), que permitem que um [aplicativo cliente](developer-glossary.md#client-application) acesse recursos protegidos em um [servidor de recursos](developer-glossary.md#resource-server).

Um token de **acesso** é um token de segurança que é emitido por um servidor de autorização. Ele contém informações sobre o usuário e o aplicativo para o qual o token se destina; que pode ser usado para acessar APIs da Web e outros recursos protegidos. Para saber mais sobre como a plataforma de identidade da Microsoft emite tokens de acesso, consulte [tokens de acesso](access-tokens.md).

Os tokens de acesso só são válidos por um curto período de tempo, portanto, os servidores de autorização, às vezes, emitirão um **token de atualização** ao mesmo tempo em que o token de acesso é emitido. O aplicativo cliente pode então trocar esse token de atualização para um novo token de acesso quando necessário. Para saber mais sobre como a plataforma de identidade da Microsoft usa tokens de atualização para revogar permissões, consulte [revogação de token](access-tokens.md#token-revocation).

Os **tokens de ID** são enviados ao aplicativo cliente como parte de um fluxo do [OpenID Connect](v2-protocols-oidc.md) . Eles podem ser enviados com um token de acesso ou em vez de um, e são usados pelo cliente para autenticar o usuário. Para saber mais sobre como a plataforma de identidade da Microsoft emite tokens de ID, consulte [tokens de ID](id-tokens.md).

### <a name="validating-security-tokens"></a>Validando tokens de segurança

Cabe ao aplicativo para o qual o token foi gerado, o aplicativo Web que conectou o usuário ou a API Web que está sendo chamada, para validar o token. O token é assinado pelo servidor de token de segurança (STS) com uma chave privada. O STS publica a chave pública correspondente. Para validar um token, o aplicativo verifica a assinatura usando a chave pública do STS para validar que a assinatura foi criada usando a chave privada.

Os tokens são válidos somente por uma quantidade limitada de tempo. Normalmente, o STS fornece um par de tokens:

* Um token de acesso para acessar o aplicativo ou recurso protegido e
* Um token de atualização usado para atualizar o token de acesso quando o token de acesso está perto de expirar.

Tokens de acesso são passados para uma API da Web como o token de `Authorization` portador no cabeçalho. Um aplicativo pode fornecer um token de atualização para o STS e, se o acesso do usuário ao aplicativo não tiver sido revogado, ele obterá um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém que sai da empresa é tratado. Quando o STS receber o token de atualização, ele não emitirá outro token de acesso válido se o usuário não estiver mais autorizado.

### <a name="json-web-tokens-jwts-and-claims"></a>JWTs (tokens Web JSON) e declarações

A plataforma de identidade da Microsoft implementa tokens de segurança como JWTs (tokens Web JSON) que contêm declarações.

Uma [declaração](developer-glossary.md#claim) fornece asserções sobre uma entidade, como um aplicativo cliente ou [proprietário do recurso](developer-glossary.md#resource-owner), para outra entidade, como um servidor de recursos.

As declarações são pares de nome/valor que retransmitem fatos sobre o assunto do token. Por exemplo, uma declaração pode conter fatos sobre a entidade de segurança que foi autenticada pelo [servidor de autorização](developer-glossary.md#authorization-server). As declarações presentes em um determinado token dependem de muitas coisas, incluindo o tipo de token, o tipo de credencial usado para autenticar o assunto, a configuração do aplicativo e assim por diante.

Os aplicativos podem usar declarações para várias tarefas, como:

* Validando o token
* Identificando o locatário da entidade do token
* Exibindo informações do usuário
* Determinando a autorização da entidade

Uma declaração consiste em pares chave-valor que fornecem informações como:

* Servidor de token de segurança que gerou o token
* Data em que o token foi gerado
* Assunto (como o usuário – exceto para daemons)
* Público, que é o aplicativo para o qual o token foi gerado
* Aplicativo (o cliente) que solicitou o token. No caso de aplicativos Web, isso pode ser o mesmo que o público-alvo

Para saber mais sobre como a plataforma de identidade da Microsoft implementa tokens e informações de declaração, consulte [tokens de acesso](access-tokens.md) e [tokens de ID](id-tokens.md).

### <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos

Dependendo de como o cliente é criado, ele pode usar um (ou vários) dos fluxos de autenticação com suporte pela plataforma de identidade da Microsoft. Esses fluxos podem produzir uma variedade de tokens (id_tokens, tokens de atualização, tokens de acesso), bem como códigos de autorização, e exigem tokens diferentes para fazê-los funcionar. Este gráfico fornece uma visão geral:

|Flow | Requisitos | id_token | o token de acesso | token de atualização | código de autorização |
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo de OIDC híbrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Resgate de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token de atualização | x | x | x| |
|[Fluxo em nome de](v2-oauth2-on-behalf-of-flow.md) | o token de acesso| x| x| x| |
|[Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (somente de aplicativo)| | |

Tokens emitidos por meio do modo implícito têm uma limitação de comprimento devido a ser passado de volta para o navegador `response_mode` por `query` meio `fragment`da URL (onde é ou).  Alguns navegadores têm um limite no tamanho da URL que pode ser colocado na barra do navegador e falham quando é muito longo.  Portanto, esses tokens não têm `groups` declarações `wids` ou.

## <a name="tenants"></a>Locatários

Um provedor de identidade de nuvem atende A muitas organizações. Para manter os usuários de diferentes organizações separadas, o Azure AD é particionado em locatários, com um locatário por organização.

Os locatários controlam os usuários e seus aplicativos associados. A plataforma Microsoft Identity também dá suporte a usuários que se conectam com contas pessoais da Microsoft.

O AD do Azure também fornece Azure Active Directory B2C para que as organizações possam conectar usuários, normalmente clientes, usando identidades sociais, como uma conta do Google. Para obter mais informações, consulte a [documentação do Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

Agora que você tem uma visão geral dos conceitos básicos, continue lendo para entender o modelo e a API do aplicativo de identidade, saiba como o provisionamento funciona na plataforma de identidade da Microsoft e obtenha links para informações detalhadas sobre cenários comuns com suporte da plataforma de identidade da Microsoft.

## <a name="application-model"></a>Modelo de aplicativo

Os aplicativos podem conectar os próprios usuários ou delegar a entrada para um provedor de identidade. Consulte [fluxos de autenticação e cenários de aplicativo](authentication-flows-app-scenarios.md) para saber mais sobre cenários de entrada com suporte pela plataforma de identidade da Microsoft.

Para que um provedor de identidade saiba que um usuário tem acesso a um aplicativo específico, o usuário e o aplicativo devem ser registrados com o provedor de identidade. Ao registrar seu aplicativo com o Azure AD, você está fornecendo uma configuração de identidade para seu aplicativo que permite que ele se integre à plataforma de identidade da Microsoft. O registro do aplicativo também permite que você:

* Personalize a identidade visual do seu aplicativo na caixa de diálogo de entrada. Isso é importante porque essa é a primeira experiência que um usuário terá com seu aplicativo.
* Decida se deseja permitir que os usuários se conectem somente se eles pertencem à sua organização. Este é um aplicativo de locatário único. Ou permitir que os usuários entrem usando qualquer conta corporativa ou de estudante. Este é um aplicativo multilocatário. Você também pode permitir contas pessoais da Microsoft ou uma conta social do LinkedIn, do Google e assim por diante.
* Permissões de escopo de solicitação. Por exemplo, você pode solicitar o escopo "User. Read", que concede permissão para ler o perfil do usuário conectado.
* Defina escopos que definem o acesso à sua API Web. Normalmente, quando um aplicativo deseja acessar sua API, ele precisa solicitar permissões para os escopos que você definir.
* Compartilhe um segredo com a plataforma de identidade da Microsoft que comprova a identidade do aplicativo.  Isso é relevante no caso em que o aplicativo é um aplicativo cliente confidencial. Um aplicativo cliente confidencial é um aplicativo que pode conter credenciais com segurança. Eles exigem um servidor de back-end confiável para armazenar as credenciais.

Depois de registrado, o aplicativo receberá um identificador exclusivo que o aplicativo compartilha com a plataforma de identidade da Microsoft quando solicitar tokens. Se o aplicativo for um [aplicativo cliente confidencial](developer-glossary.md#client-application), ele também compartilhará o segredo ou a chave pública *, dependendo se os certificados ou segredos foram usados.

A plataforma de identidade da Microsoft representa aplicativos que usam um modelo que atende a duas funções principais:

* Identificar o aplicativo pelos protocolos de autenticação aos quais ele dá suporte
* Forneça todos os identificadores, URLs, segredos e informações relacionadas que são necessários para autenticar

Plataforma de identidade da Microsoft:

* Mantém todos os dados necessários para dar suporte à autenticação em tempo de execução
* Mantém todos os dados para decidir quais recursos um aplicativo pode precisar acessar e em que circunstâncias uma determinada solicitação deve ser atendida
* Fornece a infraestrutura para implementar o provisionamento de aplicativos dentro do locatário do desenvolvedor do aplicativo e para qualquer outro locatário do Azure AD
* Manipula o consentimento do usuário durante o tempo de solicitação de token e facilita o provisionamento dinâmico de aplicativos entre locatários

Consentimento é o processo de um proprietário de recurso que concede autorização para um aplicativo cliente acessar recursos protegidos, sob permissões específicas, em nome do proprietário do recurso. Plataforma de identidade da Microsoft:

* Permite aos usuários e administradores conceder ou negar de forma dinâmica o consentimento ao aplicativo para o acesso de recursos em seu nome.
* Permite aos administradores decidir, em última análise, o que os aplicativos têm permissão para fazer, quais usuários podem usar aplicativos específicos e como os recursos do diretório são acessados.

Na plataforma Microsoft Identity, um [objeto Application](developer-glossary.md#application-object) descreve um aplicativo. No momento da implantação, a plataforma de identidade da Microsoft usa o objeto Application como um plano gráfico para criar uma [entidade de serviço](developer-glossary.md#service-principal-object), que representa uma instância concreta de um aplicativo dentro de um diretório ou locatário. A entidade de serviço define o que o aplicativo pode realmente fazer em um diretório de destino específico, que pode usá-lo, a quais recursos ele tem acesso e assim por diante. A plataforma de identidade da Microsoft cria uma entidade de serviço com base em um objeto de aplicativo por meio do **consentimento**.

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

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Fluxo de entrada do aplicativo Web com a plataforma de identidade da Microsoft

Quando um usuário navega no navegador para um aplicativo Web, acontece o seguinte:

* O aplicativo Web determina se o usuário está autenticado.
* Se o usuário não for autenticado, o aplicativo Web delegará ao Azure AD para conectar o usuário. Essa entrada será compatível com a política da organização, o que pode significar solicitar que o usuário insira suas credenciais, usando a autenticação multifator ou não usando uma senha (por exemplo, usando o Windows Hello).
* O usuário será solicitado a consentir o acesso de que o aplicativo cliente precisa. É por isso que os aplicativos cliente precisam ser registrados com o Azure AD, para que a plataforma de identidade da Microsoft possa fornecer tokens que representem o acesso ao qual o usuário tenha consentido.

Quando o usuário foi autenticado com êxito:

* A plataforma de identidade da Microsoft envia um token para o aplicativo Web.
* Um cookie é salvo, associado ao domínio do AD do Azure, que contém a identidade do usuário no jar do cookie do navegador. Na próxima vez que um aplicativo usar o navegador para navegar até o ponto de extremidade de autorização da plataforma de identidade da Microsoft, o navegador apresentará o cookie para que o usuário não precise entrar novamente. Isso também é a maneira como o SSO é obtido. O cookie é produzido pelo Azure AD e só pode ser compreendido pelo Azure AD.
* Em seguida, o aplicativo Web valida o token. Se a validação for realizada com sucesso, o aplicativo Web exibirá a página protegida e salvará um cookie de sessão no jar do cookie do navegador. Quando o usuário navega para outra página, o aplicativo Web sabe que o usuário é autenticado com base no cookie de sessão.

O diagrama de sequência a seguir resume essa interação:

![processo de autenticação do aplicativo Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como um aplicativo Web determina se o usuário é autenticado

Os desenvolvedores de aplicativos Web podem indicar se todas ou apenas determinadas páginas exigem autenticação. Por exemplo, no ASP.NET/ASP.NET Core, isso é feito adicionando o `[Authorize]` atributo às ações do controlador.

Esse atributo faz com que o ASP.NET Verifique a presença de um cookie de sessão que contém a identidade do usuário. Se um cookie não estiver presente, o ASP.NET redirecionará a autenticação para o provedor de identidade especificado. Se o provedor de identidade for o Azure AD, o aplicativo Web redireciona a autenticação `https://login.microsoftonline.com`para o, que exibe uma caixa de diálogo de entrada.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Como um aplicativo Web delega a entrada à plataforma Microsoft Identity e Obtém um token

A autenticação do usuário ocorre por meio do navegador. O protocolo OpenID usa mensagens de protocolo HTTP padrão.

* O aplicativo Web envia um HTTP 302 (redirecionamento) para o navegador para usar a plataforma de identidade da Microsoft.
* Quando o usuário é autenticado, a plataforma de identidade da Microsoft envia o token para o aplicativo Web usando um redirecionamento por meio do navegador.
* O redirecionamento é fornecido pelo aplicativo Web na forma de um URI de redirecionamento. Esse URI de redirecionamento é registrado com o objeto de aplicativo do Azure AD. Pode haver vários URIs de redirecionamento porque o aplicativo pode ser implantado em várias URLs. Portanto, o aplicativo Web também precisará especificar o URI de redirecionamento a ser usado.
* O Azure AD verifica se o URI de redirecionamento enviado pelo aplicativo Web é um dos URIs de redirecionamento registrados para o aplicativo.

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>Fluxo de entrada do aplicativo móvel e desktop com a plataforma de identidade da Microsoft

O fluxo descrito acima se aplica, com pequenas diferenças, a desktops e aplicativos móveis.

Aplicativos móveis e de desktop podem usar um controle da Web inserido ou um navegador do sistema para autenticação. O diagrama a seguir mostra como um desktop ou aplicativo móvel usa a MSAL (biblioteca de autenticação da Microsoft) para adquirir tokens de acesso e chamar APIs da Web.

![Aplicativo de desktop como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL usa um navegador para obter tokens. Assim como acontece com os aplicativos Web, a autenticação é delegada à plataforma Microsoft Identity.

Como o Azure AD salva o mesmo cookie de identidade no navegador, como ele faz para aplicativos Web, se o aplicativo nativo ou móvel usar o navegador do sistema, ele receberá imediatamente o SSO com o aplicativo Web correspondente.

Por padrão, o MSAL usa o navegador do sistema. A exceção é .NET Framework aplicativos de área de trabalho em que um controle incorporado é usado para fornecer uma experiência de usuário mais integrada.

## <a name="next-steps"></a>Próximas etapas

* Consulte o [Glossário de desenvolvedor da plataforma de identidade da Microsoft](developer-glossary.md) para se familiarizar com os termos comuns.
* Consulte [fluxos de autenticação e cenários de aplicativo](authentication-flows-app-scenarios.md) para saber mais sobre outros cenários de autenticação de usuários com suporte na plataforma de identidade da Microsoft.
* Consulte [bibliotecas do MSAL](msal-overview.md) para saber mais sobre as bibliotecas da Microsoft que ajudam você a desenvolver aplicativos que funcionam com contas da Microsoft, contas do Azure AD e Azure ad B2C usuários em um modelo de programação único e simplificado.
* Consulte [integrar o serviço de aplicativo com a plataforma de identidade da Microsoft](/azure/app-service/configure-authentication-provider-aad) para saber como configurar a autenticação para seu aplicativo do serviço de aplicativo.
