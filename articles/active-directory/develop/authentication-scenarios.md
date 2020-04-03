---
title: Autenticação na plataforma de identidade da Microsoft | Azure
description: Conheça o básico da autenticação na plataforma de identidade Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6191e67f097b5ab471c5b31eff11a0e570d1c990
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617036"
---
# <a name="authentication-basics"></a>Noções básicas de autenticação

## <a name="what-is-authentication"></a>O que é autenticação

Este artigo abrange muitos dos conceitos de autenticação que você precisará entender para criar aplicativos web protegidos, APIs web ou aplicativos que chamam de APIs da Web protegidas. Se você vir um termo que você não está familiarizado, experimente nosso [glossário](developer-glossary.md) ou [nossos vídeos da plataforma de identidade microsoft](identity-videos.md) que cobrem conceitos básicos.

**Autenticação** é o processo de provar que você é quem diz ser. Às vezes, a autenticação é abreviada para AuthN.

**Autorização** é o ato de conceder permissão a uma parte autenticada para fazer algo. Ele especifica quais dados você pode acessar e o que você pode fazer com esses dados. Às vezes, a autorização é abreviada para AuthZ.

Em vez de criar aplicativos que cada um mantenha seu próprio nome de usuário e informações de senha, o que incorre em uma alta carga administrativa quando você precisa adicionar ou remover usuários em vários aplicativos, os aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure Active Directory (Azure AD) é um provedor de identidade centralizado na nuvem. Delegar autenticação e autorização para ele permite que cenários como políticas de Acesso Condicional que exijam que um usuário esteja em um local específico, o uso de autenticação multifatorial, bem como permitir que um usuário faça login uma vez e, em seguida, seja automaticamente conectado a todos os aplicativos da Web que compartilham o mesmo diretório centralizado. Esse recurso é referido como Single Sign On (SSO).

Um provedor de identidade centralizado é ainda mais importante para aplicativos que têm usuários localizados em todo o mundo que não necessariamente acessam a rede da empresa. O Azure AD autentica os usuários e fornece tokens de acesso. Um [token de acesso](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) é um token de segurança que é emitido por um servidor de autorização. Ele contém informações sobre o usuário e o aplicativo para o qual o token é destinado; que podem ser usados para acessar APIs da Web e outros recursos protegidos.

A plataforma de identidade da Microsoft simplifica a autenticação para desenvolvedores de aplicativos, fornecendo identidade como serviço, com suporte para protocolos padrão do setor, como [OAuth 2.0](https://oauth.net/2/) e [OpenID Connect,](https://openid.net/connect/)bem como bibliotecas de código aberto para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Ele permite que os desenvolvedores construam aplicativos que assinam todas as identidades da Microsoft, obtenham tokens para chamar [o Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores construíram. Para obter mais informações, consulte [Evolution of Microsoft identity platform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Locatários

Um provedor de identidade em nuvem atende muitas organizações. Para manter os usuários de diferentes organizações separados, o Azure AD é dividido em inquilinos, com um inquilino por organização.

Os inquilinos acompanham os usuários e seus aplicativos associados. A plataforma de identidade da Microsoft também suporta usuários que acessam contas pessoais da Microsoft.

O Azure AD também fornece o Azure Active Directory B2C para que as organizações possam fazer login com usuários, normalmente clientes, usando identidades sociais como uma conta do Google. Para obter mais informações, consulte [a documentação B2C do Azure Active Directory](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Tokens de segurança

Os tokens de segurança contêm informações sobre usuários e aplicativos. O Azure AD usa tokens baseados em JSON (JWTs) que contêm reclamações.

Uma reclamação fornece afirmações sobre uma entidade, como um [aplicativo cliente](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) ou proprietário [de recursos,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)para outra entidade, como um [servidor de recursos.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)

As reivindicações são pares de nome/valor que transmitem fatos sobre o assunto do token. Por exemplo, uma reclamação pode conter fatos sobre o princípio de segurança que foi autenticado pelo [servidor de autorização.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server) As reivindicações presentes em um determinado token dependem de muitas coisas, incluindo o tipo de token, o tipo de credencial usada para autenticar o assunto, a configuração do aplicativo e assim por diante.

Os aplicativos podem usar reivindicações para várias tarefas, tais como:

* Validação do token
* Identificando o inquilino do sujeito do token
* Exibindo informações do usuário
* Determinando a autorização do sujeito

Uma reivindicação consiste em pares de valor-chave que fornecem informações como:

* Server de token de segurança que gerou o token
* Data em que o token foi gerado
* Assunto (como o usuário -- exceto para daemons)
* Audiência, que é o aplicativo para o qual o token foi gerado
* Aplicativo (o cliente) que pediu o token. No caso de aplicativos web, isso pode ser o mesmo que o público

Para obter informações mais detalhadas sobre a reivindicação, consulte [tokens de acesso](access-tokens.md) e [tokens de ID](id-tokens.md).

Cabe ao aplicativo para o qual o token foi gerado, o aplicativo web que fez o uso do usuário ou a API da Web a ser chamada, para validar o token. O token é assinado pelo Security Token Server (STS) com uma chave privada. O STS publica a chave pública correspondente. Para validar um token, o aplicativo verifica a assinatura usando a chave pública STS para validar que a assinatura foi criada usando a chave privada.

Os tokens são válidos apenas por um período limitado de tempo. Normalmente, o STS fornece um par de tokens: um token de acesso para acessar o aplicativo ou recurso protegido, e um token de atualização usado para atualizar o token de acesso quando o token de acesso está perto de expirar.

Os tokens de acesso são passados para uma `Authorization` API da Web como o token do portador no cabeçalho. Um aplicativo pode fornecer um token de atualização para o STS, e se o acesso do usuário ao aplicativo não foi revogado, ele receberá de volta um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém deixando a empresa é tratado. Quando o STS recebe o token de atualização, ele não emitirá outro token de acesso válido se o usuário não estiver mais autorizado.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos

Dependendo de como seu cliente é construído, ele pode usar um (ou vários) dos fluxos de autenticação suportados pelo Azure AD. Esses fluxos podem produzir uma variedade de tokens (id_tokens, tokens de atualização, tokens de acesso) bem como códigos de autorização, e exigem diferentes tokens para fazê-los funcionar. Este gráfico fornece uma visão geral:

|Flow | Requer | id_token | o token de acesso | token de atualização | código de autorização | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo oidc híbrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Atualizar resgate de tokens](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token de atualização | x | x | x| |
|[Fluxo em nome de](v2-oauth2-on-behalf-of-flow.md) | o token de acesso| x| x| x| |
|[Credenciais do cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (somente para aplicativos)| | |

Os tokens emitidos através do modo implícito têm uma limitação de `response_mode` `query` comprimento `fragment`devido ao ser passado de volta para o navegador através da URL (onde está ou ).  Alguns navegadores têm um limite no tamanho da URL que pode ser colocado na barra do navegador e falhar quando for muito longo.  Assim, esses tokens `groups` não `wids` têm ou reivindicações. 

Agora que você tem uma visão geral do básico, continue lendo para entender o modelo e a API do aplicativo de identidade, saiba como o provisionamento funciona no Azure AD e obtenha links para informações detalhadas sobre cenários comuns que o Azure AD suporta.

## <a name="application-model"></a>Modelo de aplicativo

Os aplicativos podem fazer login nos próprios usuários ou delegar o login a um provedor de identidade. Consulte [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md) para saber sobre cenários de login suportados pelo Azure AD.

Para que um provedor de identidade saiba que um usuário tem acesso a um determinado aplicativo, tanto o usuário quanto o aplicativo devem ser registrados junto ao provedor de identidade. Quando você registra seu aplicativo com o Azure AD, você está fornecendo uma configuração de identidade para o seu aplicativo que permite que ele se integre ao Azure AD. O registro do aplicativo também permite:

* Personalize a marca do seu aplicativo na caixa de diálogo de login. Isso é importante porque esta é a primeira experiência que um usuário terá com o seu aplicativo.
* Decida se você quer deixar os usuários entrarem somente se eles pertencem à sua organização. Esta é uma única aplicação de inquilino. Ou permitir que os usuários entrem usando qualquer conta de trabalho ou escola. Este é um aplicativo multi-inquilino. Você também pode permitir contas pessoais da Microsoft, ou uma conta social do LinkedIn, Google e assim por diante.
* Solicite permissões de escopo. Por exemplo, você pode solicitar o escopo "user.read", que concede permissão para ler o perfil do usuário inscrito.
* Defina escopos que definem o acesso à sua API web. Normalmente, quando um aplicativo deseja acessar sua API, ele precisará solicitar permissões para os escopos definidos.
* Compartilhe um segredo com o Azure AD que prova a identidade do aplicativo para o Azure AD.  Isso é relevante no caso de o aplicativo ser um aplicativo cliente confidencial. Um aplicativo cliente confidencial é um aplicativo que pode conter credenciais com segurança. Eles exigem um servidor backend confiável para armazenar as credenciais.

Uma vez registrado, o aplicativo receberá um identificador exclusivo que o aplicativo compartilha com o Azure AD quando solicita tokens. Se o aplicativo for um [aplicativo cliente confidencial,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)ele também compartilhará o segredo ou a chave pública*- dependendo se certificados ou segredos foram usados.

A plataforma de identidade da Microsoft representa aplicativos usando um modelo que cumpre duas funções principais:

* Identifique o aplicativo pelos protocolos de autenticação que ele suporta
* Forneça todos os identificadores, URLs, segredos e informações relacionadas que são necessárias para autenticar

A plataforma de identidade da Microsoft:

* Mantém todos os dados necessários para suportar a autenticação em tempo de execução
* Detém todos os dados para decidir quais recursos um aplicativo pode precisar acessar e, em que circunstâncias, uma determinada solicitação deve ser cumprida
* Fornece infra-estrutura para implementar o provisionamento de aplicativos dentro do inquilino do desenvolvedor de aplicativos e para qualquer outro inquilino azure AD
* Lida com o consentimento do usuário durante o tempo de solicitação de token e facilita o provisionamento dinâmico de aplicativos entre os inquilinos

Consentimento é o processo de um proprietário de recursos concedendo autorização para um aplicativo cliente acessar recursos protegidos, sob permissões específicas, em nome do proprietário dos recursos. A plataforma de identidade da Microsoft:

* Permite aos usuários e administradores conceder ou negar de forma dinâmica o consentimento ao aplicativo para o acesso de recursos em seu nome.
* Permite aos administradores decidir, em última análise, o que os aplicativos têm permissão para fazer, quais usuários podem usar aplicativos específicos e como os recursos do diretório são acessados.

Na plataforma de identidade da Microsoft, um [objeto de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) descreve um aplicativo. No momento da implantação, a plataforma de identidade da Microsoft usa o objeto de aplicativo como um modelo para criar um [principal de serviço](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object), o que representa uma instância concreta de um aplicativo dentro de um diretório ou inquilino. O diretor de serviço define o que o aplicativo pode realmente fazer em um diretório específico de destino, quem pode usá-lo, quais recursos ele tem acesso e assim por diante. A plataforma de identidade da Microsoft cria um principal de serviço a partir de um objeto de aplicativo através do **consentimento**.

O diagrama a seguir mostra um fluxo de provisionamento simplificado da plataforma de identidade da Microsoft orientado por consentimento. Mostra dois inquilinos: A e B. O inquilino A é dono do aplicativo. O inquilino B está instanciando o aplicativo através de um diretor de serviço.  

![Fluxo de provisionamento simplificado orientado por consentimento](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Neste fluxo de provisionamento:

1. Um usuário do locatário B tenta entrar com o aplicativo; o ponto de extremidade da autorização solicita um token para o aplicativo.
1. As credenciais do usuário são adquiridas e verificadas para autenticação.
1. O usuário é solicitado a fornecer consentimento para que o aplicativo tenha acesso ao inquilino B.
1. A plataforma de identidade da Microsoft usa o objeto de aplicativo no inquilino A como um projeto para criar um princípio de serviço no inquilino B.
1. O usuário recebe o token solicitado.

Você pode repetir este processo para inquilinos adicionais. O locatário A mantém o blueprint para o aplicativo (objeto de aplicativo). Os usuários e os admins de todos os outros inquilinos onde o aplicativo é dado consentimento mantêm o controle sobre o que o aplicativo é permitido fazer através do objeto principal do serviço correspondente em cada inquilino. Para obter mais informações, confira [Objetos de entidade de serviço e aplicativo na plataforma de identidade da Microsoft](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Fluxo de login do aplicativo web com o Azure AD

Quando um usuário navega no navegador para um aplicativo web, acontece o seguinte:

* O aplicativo web determina se o usuário está autenticado.
* Se o usuário não for autenticado, o aplicativo web delegará ao Azure AD para fazer login no usuário. Esse login estará em conformidade com a política da organização, o que pode significar pedir ao usuário para inserir suas credenciais, usando autenticação multifatorial ou não usando uma senha em tudo (por exemplo, usando o Windows Hello).
* O usuário é solicitado a consentir com o acesso que o aplicativo cliente precisa. É por isso que os aplicativos clientes precisam ser registrados no Azure AD, para que o Azure AD possa fornecer tokens representando o acesso ao qual o usuário consentiu.

Quando o usuário tiver autenticado com sucesso:

* O Azure AD envia um token para o aplicativo web.
* Um cookie é salvo, associado ao domínio do Azure AD, que contém a identidade do usuário no pote de cookies do navegador. Da próxima vez que um aplicativo usa o navegador para navegar até o ponto final de autorização do Azure AD, o navegador apresenta o cookie para que o usuário não precise fazer login novamente. Esta é também a maneira que o SSO é alcançado. O cookie é produzido pelo Azure AD e só pode ser entendido pelo Azure AD.
* O aplicativo web valida o token. Se a validação for bem-sucedida, o aplicativo da Web exibirá a página protegida e salvará um cookie de sessão no pote de cookies do navegador. Quando o usuário navega para outra página, o aplicativo da Web sabe que o usuário é autenticado com base no cookie da sessão.

O diagrama de seqüência a seguir resume essa interação:

![processo de autenticação de aplicativos web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Como um aplicativo web determina se o usuário é autenticado

Os desenvolvedores de aplicativos da Web podem indicar se todas ou apenas determinadas páginas requerem autenticação. Por exemplo, em ASP.NET/ASP.NET Core, isso `[Authorize]` é feito adicionando o atributo às ações do controlador. 

Esse atributo faz com que ASP.NET verifique a presença de um cookie de sessão contendo a identidade do usuário. Se um cookie não estiver presente, ASP.NET redireciona a autenticação para o provedor de identidade especificado. Se o provedor de identidade for Azure AD, `https://login.microsoftonline.com`o aplicativo web redirecionará a autenticação para , que exibe uma caixa de diálogo de login.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Como um aplicativo web delega login no Azure AD e obtém um token

A autenticação do usuário acontece através do navegador. O protocolo OpenID usa mensagens de protocolo HTTP padrão.
* O aplicativo web envia um HTTP 302 (redirecionamento) para o navegador para usar o Azure AD.
* Quando o usuário é autenticado, o Azure AD envia o token para o aplicativo web usando um redirecionamento através do navegador.
* O redirecionamento é fornecido pelo aplicativo web na forma de um URI de redirecionamento. Este URI de redirecionamento é registrado no objeto de aplicativo Azure AD. Pode haver várias URIs de redirecionamento porque o aplicativo pode ser implantado em várias URLs. Assim, o aplicativo web também precisará especificar o URI de redirecionamento para usar.
* O Azure AD verifica se o URI de redirecionamento enviado pelo aplicativo web é uma das URIs de redirecionamento registradas para o aplicativo.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Fluxo de login de aplicativos para desktop e celular com o Azure AD

O fluxo descrito acima se aplica, com pequenas diferenças, aos aplicativos desktop e mobile.

Aplicativos desktop e móveis podem usar um controle web incorporado, ou um navegador de sistema, para autenticação. O diagrama a seguir mostra como um aplicativo desktop ou móvel usa a biblioteca de autenticação da Microsoft (MSAL) para adquirir tokens de acesso e chamar APIs da Web.

![Aplicativo de desktop como parece ser](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

A MSAL usa um navegador para obter tokens. Assim como nos aplicativos web, a autenticação é delegada ao Azure AD.

Como o Azure AD salva o mesmo cookie de identidade no navegador como faz para aplicativos da Web, se o aplicativo nativo ou móvel usar o navegador do sistema, ele receberá imediatamente o SSO com o aplicativo web correspondente.

Por padrão, o MSAL usa o navegador do sistema. A exceção são os aplicativos de desktop .NET Framework, onde um controle incorporado é usado para fornecer uma experiência de usuário mais integrada.

## <a name="next-steps"></a>Próximas etapas

* Consulte o [glossário](developer-glossary.md) de desenvolvedor da plataforma de identidade da Microsoft para se familiarizar com termos comuns.
* Consulte [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md) para saber mais sobre outros cenários para autenticar usuários suportados pela plataforma de identidade microsoft.
* Consulte [bibliotecas MSAL](msal-overview.md) para aprender sobre as bibliotecas da Microsoft que ajudam você a desenvolver aplicativos que funcionam com contas Microsoft, contas Azure AD e usuários do Azure AD B2C em um único modelo de programação simplificado.
* Consulte [Integrar o Serviço de Aplicativos com](/azure/app-service/configure-authentication-provider-aad) a plataforma de identidade Microsoft para saber como configurar a autenticação para o seu aplicativo App Service.
