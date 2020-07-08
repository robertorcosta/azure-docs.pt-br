---
title: Autenticação e autorização
description: Saiba mais sobre o suporte interno de autenticação e autorização em Azure App serviço e Azure Functions e como ele pode ajudar a proteger seu aplicativo contra acesso não autorizado.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 04/15/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: f51a396e997a9e6392f3e86a6f77e581753d6ada
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83196447"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autenticação e autorização no serviço Azure App e Azure Functions

> [!NOTE]
> No momento, ASP.NET Core atualmente não dá suporte ao preenchimento do usuário atual com o recurso de autenticação/autorização.
>

O Serviço de Aplicativo do Azure dá suporte interno à autenticação e autorização para que você possa fazer login de usuários e acessar dados, gravando o mínimo ou nenhum código no aplicativo Web, na API RESTful e no back-end para dispositivos móveis, e também [Azure Functions](../azure-functions/functions-overview.md). Este artigo descreve como o Serviço de Aplicativo ajuda a simplificar a autenticação e autorização do aplicativo.

A autenticação e autorização seguras exigem um profundo entendimento de segurança, incluindo federação, criptografia, [gerenciamento JWT (Token Web JSON)](https://wikipedia.org/wiki/JSON_Web_Token), [tipos de concessão](https://oauth.net/2/grant-types/), e assim por diante. O Serviço de Aplicativo disponibiliza esses utilitários para que você possa investir mais tempo e energia no fornecimento de valor comercial ao seu cliente.

> [!IMPORTANT]
> Não é necessário usar esse recurso para autenticação e autorização. Você pode usar os recursos de segurança agrupados em sua estrutura da Web de sua escolha ou pode escrever seus próprios utilitários. No entanto, tenha em mente que [o Chrome 80 está fazendo alterações significativas em sua implementação de SameSite para cookies](https://www.chromestatus.com/feature/5088147346030592) (data de lançamento em relação a 2020 de março), e a autenticação remota personalizada ou outros cenários que dependem de postagem de cookie entre sites podem ser interrompidos quando os navegadores Chrome do cliente são atualizados. A solução alternativa é complexa porque precisa dar suporte a diferentes comportamentos de SameSite para diferentes navegadores. 
>
> As versões ASP.NET Core 2,1 e acima hospedadas pelo serviço de aplicativo já estão corrigidas para essa alteração significativa e lidam com os navegadores Chrome 80 e mais antigos adequadamente. Além disso, o mesmo patch para o ASP.NET Framework 4.7.2 está sendo implantado nas instâncias do serviço de aplicativo em janeiro de 2020. Para obter mais informações, incluindo como saber se seu aplicativo recebeu o patch, consulte [Azure app Service SameSite cookie Update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

Para informações específicas sobre aplicativos móveis nativos, consulte [Autenticação e autorização de usuários para aplicativos móveis com Serviço de Aplicativo do Azure ](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Como funciona

O módulo de autenticação e autorização executa na mesma caixa restrita que o código do aplicativo. Quando habilitado, toda solicitação HTTP de entrada passa por ele antes de ser manipulada pelo código do aplicativo.

![](media/app-service-authentication-overview/architecture.png)

Esse módulo trata diversos pontos do aplicativo:

- Autentica usuários com o provedor especificado
- Valida, armazena e atualiza tokens
- Gerencia a sessão autenticada
- Injeta informações de identidade em cabeçalhos da solicitação

O módulo executa separadamente do código do aplicativo e é configurado usando as configurações do aplicativo. Não há necessidade de SDKs, idiomas específicos ou alterações no código do aplicativo. 

### <a name="userapplication-claims"></a>Declarações de usuário/aplicativo

Para todas as estruturas de linguagem, o serviço de aplicativo faz as declarações no token de entrada (seja por meio de um usuário final autenticado ou um aplicativo cliente) disponíveis para seu código injetando-as nos cabeçalhos de solicitação. Para aplicativos ASP.NET 4.6, o Serviço de Aplicativo preenche [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) com as declarações do usuário autenticado, de modo que seja possível seguir o padrão de código .NET Standard, incluindo o atributo `[Authorize]`. Da mesma forma, para aplicativos PHP, o Serviço de Aplicativo preenche a variável `_SERVER['REMOTE_USER']`. Para aplicativos Java, as declarações podem [ser acessadas do servlet Tomcat](containers/configure-language-java.md#authenticate-users-easy-auth).

Por [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` o não é populado para o código .net, mas você ainda pode encontrar as declarações do usuário nos cabeçalhos da solicitação ou obter o `ClaimsPrincipal` objeto do contexto da solicitação ou mesmo por meio de um parâmetro de associação. Consulte [trabalhando com identidades de cliente](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) para obter mais informações.

Para mais informações, consulte [Acessar declarações de usuário](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Token Store

O Serviço de Aplicativo fornece um armazenamento de token integrado, que é um repositório de tokens associados aos usuários dos aplicativos Web, APIs ou aplicativos móveis nativos. Ao habilitar a autenticação com qualquer provedor, esse armazenamento de token ficará imediatamente disponível para o aplicativo. Se o código do aplicativo precisar acessar dados desses provedores em nome do usuário, como: 

- postar na linha do tempo do Facebook do usuário autenticado
- ler os dados corporativos do usuário usando a API de Microsoft Graph

Normalmente, é necessário gravar código para coletar, armazenar e atualizar esses tokens no aplicativo. Com o Token Store, você apenas [recupera os tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando precisar deles e [informa ao Serviço de Aplicativo para atualizá-los](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando tornarem-se inválidos. 

Os tokens de ID, tokens de acesso e tokens de atualização são armazenados em cache para a sessão autenticada e são acessíveis somente pelo usuário associado.  

Se você não precisar trabalhar com tokens no aplicativo, poderá desabilitar o Token Store.

### <a name="logging-and-tracing"></a>Registro em log e rastreamento

Se você [habilitar o log de aplicativo](troubleshoot-diagnostic-logs.md), verá os rastreamentos de autenticação e autorização diretamente nos arquivos de log. Se você vir um erro de autenticação que não esperava, poderá encontrar convenientemente todos os detalhes examinando os logs de aplicativo existentes. Se você habilitar [rastreamento de solicitação com falha](troubleshoot-diagnostic-logs.md), será possível visualizar exatamente qual função o módulo de autenticação e autorização pode ter desempenhado em uma solicitação com falha. Nos logs de rastreamento, procure referências para um módulo nomeado `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Provedores de identidade

O Serviço de Aplicativo usa [identidade federada](https://en.wikipedia.org/wiki/Federated_identity), na qual um provedor de identidade de terceiros gerencia as identidades do usuário e o fluxo de autenticação para você. Cinco provedores de identidade estão disponíveis por padrão: 

| Provedor | Ponto de extremidade de logon |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Conta da Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Ao habilitar a autenticação e autorização com um desses provedores, seu ponto de extremidade de logon estará disponível para autenticação de usuário e validação de tokens de autenticação do provedor. Você pode fornecer aos usuários qualquer número dessas opções de entrada com facilidade. Você também pode integrar outro provedor de identidade ou [sua própria solução de identidade personalizada][custom-auth].

## <a name="authentication-flow"></a>Fluxo de autenticação

O fluxo de autenticação é o mesmo para todos os provedores, mas difere dependendo se você deseja entrar com o SDK do provedor:

- Sem SDK do provedor: o aplicativo delega o logon federado ao Serviço de Aplicativo. Esse é tipicamente o caso de aplicativos do navegador que podem apresentar a página de logon do provedor ao usuário. O código do servidor gerencia o processo de logon, portanto, ele também é chamado de _fluxo direcionado ao servidor_ ou _fluxo do servidor_. Este caso aplica-se a aplicativos de navegador. É aplicável também aos aplicativos nativos que conectam usuários usando o SDK de cliente dos Aplicativos Móveis porque o SDK abre um modo de exibição da Web para conectar usuários com a autenticação do Serviço de Aplicativo. 
- Com o provedor SDK: o aplicativo assina os usuários no provedor manualmente e, em seguida, envia o token de autenticação para o Serviço de Aplicativo para validação. Esse é tipicamente o caso de aplicativos sem navegador, que não podem apresentar a página de entrada do provedor ao usuário. O código do aplicativo gerencia o processo de entrada, portanto, ele também é chamado de _fluxo direcionado ao cliente_ ou _fluxo do cliente_. Esse caso aplica-se a APIs REST, [Azure Functions](../azure-functions/functions-overview.md) e clientes do navegador JavaScript, bem como aplicativos de navegador que precisam de mais flexibilidade no processo de entrada. Aplica-se também a aplicativos móveis nativos que conectam usuários usando o SDK do provedor.

> [!NOTE]
> Chamadas de um aplicativo de navegador confiável no serviço de aplicativo para outra API REST no serviço de aplicativo ou [Azure Functions](../azure-functions/functions-overview.md) podem ser autenticadas usando o fluxo direcionado ao servidor. Para obter mais informações, consulte [Personalizar autenticação e autorização no serviço de aplicativos](app-service-authentication-how-to.md).
>

A tabela abaixo mostra as etapas do fluxo de autenticação.

| Etapa | Sem SDK do provedor | Com SDK do provedor |
| - | - | - |
| 1. conectar o usuário | Redireciona o cliente para `/.auth/login/<provider>`. | O código do cliente coneta o usuário diretamente no SDK do provedor e recebe um token de autenticação. Para obter informações, consulte a documentação do provedor. |
| 2. pós-autenticação | Provedor redireciona o cliente para `/.auth/login/<provider>/callback`. | O código [do cliente posta o token do provedor](app-service-authentication-how-to.md#validate-tokens-from-providers) para `/.auth/login/<provider>` para validação. |
| 3. estabelecer sessão autenticada | O Serviço de Aplicativo adiciona um cookie autenticado à resposta. | O Serviço de Aplicativo retorna o próprio token de autenticação para o código do cliente. |
| 4. fornecer conteúdo autenticado | O cliente inclui o cookie de autenticação em solicitações subsequentes (manipuladas automaticamente pelo navegador). | O código do cliente apresenta o token de autenticação no cabeçalho `X-ZUMO-AUTH` (manipulado automaticamente pelos SDKs de cliente dos Aplicativos Móveis). |

Para navegadores do cliente, o Serviço de Aplicativo pode direcionar automaticamente todos os usuários não autenticados para `/.auth/login/<provider>`. Você também pode apresentar aos usuários um ou mais links `/.auth/login/<provider>` para entrar no aplicativo usando o provedor escolhido.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Comportamento de autorização

Na [portal do Azure](https://portal.azure.com), você pode configurar a autorização do serviço de aplicativo com vários comportamentos quando a solicitação de entrada não for autenticada.

![](media/app-service-authentication-overview/authorization-flow.png)

Os cabeçalhos a seguir descrevem as opções.

### <a name="allow-anonymous-requests-no-action"></a>Permitir solicitações anônimas (nenhuma ação)

Essa opção adia a autorização de tráfego não autenticado para o código do aplicativo. Para solicitações autenticadas, o Serviço de Aplicativo também passa informações de autenticação nos cabeçalhos HTTP. 

Essa opção oferece mais flexibilidade no processamento de solicitações anônimas. Por exemplo, permite que você [apresente vários provedores de entrada](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aos usuários. No entanto, é necessário gravar o código. 

### <a name="allow-only-authenticated-requests"></a>Permitir apenas solicitações autenticadas

A opção é **fazer logon com \<provider> **o. O Serviço de Aplicativo redireciona todas as solicitações anônimas para `/.auth/login/<provider>` do provedor escolhido. Se a solicitação anônima originar-se de um aplicativo móvel nativo, a resposta retornada será `HTTP 401 Unauthorized`.

Com essa opção, você não precisa gravar nenhum código de autenticação no aplicativo. Uma autorização mais precisa, como autorização específica de função, pode ser tratada inspecionando as declarações do usuário (consulte [Acessar declarações do usuário](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única.

> [!NOTE]
> A autenticação/autorização era conhecida anteriormente como autenticação fácil.
>

## <a name="more-resources"></a>Mais recursos

[Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure (Windows)](app-service-web-tutorial-auth-aad.md)  
[Tutorial: Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure para Linux](containers/tutorial-auth-aad.md)  
[Personalizar a autenticação e a autorização no serviço](app-service-authentication-how-to.md) 
 de aplicativo [Integração do .NET Core do Azure AppService EasyAuth (terceiros)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) 
 [Obtendo Azure app a autenticação do serviço funcionando com o .NET Core (terceiros)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Guias de instruções específicas do provedor:

* [Como configurar seu aplicativo para usar o logon do Azure Active Directory][AAD]
* [Como configurar seu aplicativo para usar o logon do Facebook][Facebook]
* [Como configurar seu aplicativo para usar o logon do Google][Google]
* [Como configurar seu aplicativo para usar o logon da Conta da Microsoft][MSA]
* [Como configurar seu aplicativo para usar o logon do Twitter][Twitter]
* [Como: usar autenticação personalizada para o aplicativo][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
