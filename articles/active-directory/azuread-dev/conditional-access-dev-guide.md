---
title: Orientação do desenvolvedor do Azure AD Conditional Access
description: Orientação do desenvolvedor e cenários para acesso condicional do Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: 92acb1a475fbd41bfb7351d73c61db866ce2bbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154926"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Orientação do desenvolvedor para acesso condicionado ao diretório ativo do Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O recurso Conditional Access no Azure Active Directory (Azure AD) oferece uma das várias maneiras que você pode usar para proteger seu aplicativo e proteger um serviço. O Conditional Access permite que desenvolvedores e clientes corporativos protejam os serviços de várias maneiras, incluindo:

* Autenticação multifator
* Permissão para que somente dispositivos inscritos no Intune acessem serviços específicos
* Restrição de locais de usuário e intervalos de IP

Para obter mais informações sobre os recursos completos do Acesso Condicional, consulte [Conditional Access no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Para desenvolvedores que constroem aplicativos para o Azure AD, este artigo mostra como você pode usar o Conditional Access e também aprenderá sobre o impacto do acesso a recursos que você não tem controle sobre os quais podem ter políticas de acesso condicional aplicadas. O artigo também explora as implicações do Acesso Condicional no nome do fluxo, aplicativos web, acessando o Microsoft Graph e chamando APIs.

Conhecimento de aplicativos de [único locatário e multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json), além de [padrões comuns de autenticação](v1-authentication-scenarios.md) é assumido.

## <a name="how-does-conditional-access-impact-an-app"></a>Como o Conditional Access impacta um aplicativo?

### <a name="app-types-impacted"></a>Tipos de aplicativo afetados

Na maioria dos casos, o Conditional Access não altera o comportamento de um aplicativo ou requer qualquer alteração do desenvolvedor.Somente em certos casos em que um aplicativo solicita indiretamente ou silenciosamente um token para um serviço, um aplicativo requer alterações de código para lidar com os "desafios" do Acesso Condicional.O que pode ser tão simples quanto executar uma solicitação de entrada interativa.

Especificamente, os seguintes cenários exigem código para lidar com "desafios" de acesso condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando ADAL.js
* Aplicativos Web chamando um recurso

As políticas de acesso condicional podem ser aplicadas ao aplicativo, mas também podem ser aplicadas a uma API web que seu aplicativo acessa. Para saber mais sobre como configurar uma política de acesso condicional, consulte [Quickstart: Exija MFA para aplicativos específicos com acesso condicionado ao diretório ativo do Azure](../conditional-access/app-based-mfa.md).

Dependendo do cenário, um cliente corporativo pode aplicar e remover políticas de Acesso Condicional a qualquer momento. Para que o aplicativo continue funcionando quando uma nova política é aplicada, você precisa implementar o tratamento de "desafio". Os exemplos a seguir ilustram o tratamento de desafio.

### <a name="conditional-access-examples"></a>Exemplos de Acesso Condicional

Alguns cenários exigem alterações de código para lidar com o Acesso Condicional, enquanto outros funcionam como estão. Aqui estão alguns cenários usando o Acesso Condicional para fazer autenticação multifatorial que dá alguma visão sobre a diferença.

* Você está construindo um aplicativo para iOS de um único inquilino e aplicar uma política de Acesso Condicional. O aplicativo conecta um usuário e não solicita acesso a uma API. Quando o usuário entra, a política é invocada automaticamente e o usuário precisa realizar a MFA (autenticação multifator). 
* Você está criando um aplicativo nativo que usa um serviço de camada intermediária para acessar a API downstream. Um cliente empresarial na empresa usando esse aplicativo aplica uma política à API downstream. Quando um usuário final se conecta, o aplicativo nativo solicita acesso à camada intermediária e envia o token. A camada intermediária executa o fluxo “em nome de” para solicitar acesso à API downstream. Nesse momento, um "desafio" claims é apresentado à camada intermediária. O nível intermediário envia o desafio de volta para o aplicativo nativo, que precisa cumprir a política de Acesso Condicional.

#### <a name="microsoft-graph"></a>Microsoft Graph

O Microsoft Graph tem considerações especiais ao criar aplicativos em ambientes de Acesso Condicional. Geralmente, a mecânica do Acesso Condicional se comporta da mesma forma, mas as políticas que seus usuários vêem serão baseadas nos dados subjacentes que seu aplicativo está solicitando no gráfico. 

Especificamente, todos os escopos do Microsoft Graph representam algum conjunto de dados que pode ter políticas aplicadas individualmente. Uma vez que as políticas de acesso condicional são atribuídas aos conjuntos de dados específicos, o Azure AD aplicará políticas de acesso condicional com base nos dados por trás do Graph - em vez do próprio Graph.

Por exemplo, se um aplicativo solicitar os seguintes escopos do Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Um aplicativo pode esperar que seus usuários cumpram todas as políticas definidas em Reservas e Intercâmbio. Alguns escopos podem mapear para vários conjuntos de dados se ele conceder acesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Cumprindo uma política de acesso condicional

Para várias topologias diferentes, uma política de Acesso Condicional é avaliada quando a sessão é estabelecida. Como uma política de Acesso Condicional opera na granularidade de aplicativos e serviços, o ponto em que é invocado depende muito do cenário que você está tentando realizar.

Quando seu aplicativo tenta acessar um serviço com uma política de Acesso Condicional, ele pode encontrar um desafio de Acesso Condicional. Este desafio está codificado `claims` no parâmetro que vem em uma resposta do Azure AD. Veja um exemplo desse parâmetro de desafio: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Os desenvolvedores podem aceitar esse desafio e acrescentá-lo em uma nova solicitação no Azure AD. A aprovação deste estado solicita ao usuário final que execute qualquer ação necessária para cumprir a política de Acesso Condicional. Nos cenários a seguir, são explicadas as especificações do erro e como extrair o parâmetro.

## <a name="scenarios"></a>Cenários

### <a name="prerequisites"></a>Pré-requisitos

Azure AD Conditional Access é um recurso incluído no [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Você pode saber mais sobre os requisitos de licenciamento no [relatório de uso não licenciado](../active-directory-conditional-access-unlicensed-usage-report.md). Os desenvolvedores podem ingressar no [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), que inclui uma assinatura gratuita para o Enterprise Mobility Suite que, por sua vez, inclui o Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações só se aplicam nestes cenários de Acesso Condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando ADAL.js

As seções a seguir discutem cenários comuns que são mais complexos. O princípio principal de funcionamento é que as políticas de Acesso Condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: aplicativo executando o fluxo em nome de

Nesse cenário, vamos acompanhar o caso em que um aplicativo nativo chama um serviço/API Web. Por sua vez, esse serviço faz o fluxo "em nome de" chamar um serviço downstream. No nosso caso, aplicamos nossa política de acesso condicional ao serviço de downstream (Web API 2) e estamos usando um aplicativo nativo em vez de um aplicativo de servidor/daemon. 

![Diagrama de aplicativo executando o fluxo em nome de](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

A solicitação de token inicial para a API Web 1 não solicita ao usuário final a autenticação multifator, pois a API Web 1 nem sempre pode acessar a API downstream. Depois que a API Web 1 tenta solicitar um token em nome do usuário para a API Web 2, a solicitação falha, uma vez que o usuário não se conectou com a autenticação multifator.

O Azure AD retorna uma resposta HTTP com alguns dados interessantes:

> [!NOTE]
> Neste caso, é uma descrição de erro de autenticação de vários `interaction_required` fatores, mas há uma ampla gama de possíveis referentes ao Acesso Condicional.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Na API Web 1, capturamos o erro `error=interaction_required` e enviamos de volta o desafio `claims` ao aplicativo da área de trabalho. Nesse ponto, o aplicativo da área de trabalho pode fazer uma nova chamada `acquireToken()` e acrescentar o desafio `claims` como um parâmetro extra de cadeia de consulta. Essa nova solicitação requer que o usuário faça a autenticação multifator e envie esse novo token de volta à API Web 1 e complete o fluxo em nome de.

Para testar esse cenário, veja nosso [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ele demonstra como passar o desafio claims de volta da API Web 1 para o aplicativo nativo e construir uma nova solicitação dentro do aplicativo cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: aplicativo acessando vários serviços

Neste cenário, caminhamos através do caso em que um aplicativo web acessa dois serviços, um dos quais tem uma política de Acesso Condicional atribuída. Dependendo da lógica do seu aplicativo, pode existir um caminho no qual seu aplicativo não exige acesso a ambos os serviços Web. Nesse cenário, a ordem na qual você solicita um token tem um papel importante na experiência do usuário final.

Vamos supor que temos o serviço web A e B e o serviço web B tem nossa política de Acesso Condicional aplicada. Embora a solicitação auth interativa inicial exija consentimento para ambos os serviços, a política de Acesso Condicional não é necessária em todos os casos. Se o aplicativo solicitar um token para o serviço Web B, a política será invocada e as solicitações subsequentes para o serviço Web A também serão bem-sucedidas, como se segue.

![Diagrama do fluxo de aplicativo acessando vários serviços](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternativamente, se o aplicativo solicitar inicialmente um token para o serviço web A, o usuário final não invocará a política de Acesso Condicional. Isso permite que o desenvolvedor do aplicativo controle a experiência do usuário final e não force a política de Acesso Condicional a ser invocada em todos os casos. O caso complicado é se o aplicativo solicitar posteriormente um token para o serviço web B. Neste ponto, o usuário final precisa cumprir a política de Acesso Condicional. Quando o aplicativo tenta `acquireToken`, ele pode gerar o seguinte erro (ilustrado no diagrama a seguir):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplicativo acessando vários serviços que solicitam um novo token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se o aplicativo estiver usando a biblioteca ADAL, uma falha ao adquirir o token será sempre repetida interativamente. Quando essa solicitação interativa ocorre, o usuário final tem a oportunidade de cumprir o Acesso Condicional. Isso é verdade, a `AcquireTokenSilentAsync` `PromptBehavior.Never` menos que a solicitação seja ```AcquireToken``` um ou nesse caso o aplicativo precisa realizar uma solicitação interativa para dar ao usuário final a oportunidade de cumprir a política.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Cenário: SPA (aplicativo de página única) usando ADAL.js

Neste cenário, nós caminhamos sobre o caso quando temos um aplicativo de uma única página (SPA), usando ADAL.js para chamar uma API web protegida de Acesso Condicional. Esta é uma arquitetura simples, mas tem algumas nuances que precisam ser levadas em conta ao se desenvolver em torno do Acesso Condicional.

No ADAL.js, há algumas funções que obtêm tokens: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `login()`obtém um token de ID através de uma solicitação de login interativa, mas não obtém tokens de acesso para qualquer serviço (incluindo uma API web protegida de Acesso Condicional).
* `acquireToken(…)` pode ser usada para obter silenciosamente um token de acesso, o que significa que ela não mostra a interface do usuário em nenhuma circunstância.
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são usadas para solicitar interativamente um token para um recurso, o que significa que elas sempre mostram a interface do usuário de entrada.

Quando um aplicativo precisa de um token de acesso para chamar uma API Web, ele tenta uma função `acquireToken(…)`. Se a sessão de token estiver expirada ou precisarmos cumprir uma política de Acesso Condicional, a função *AcquireToken* falhará e o aplicativo usará `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Diagrama do fluxo de aplicativo de página única usando ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Vamos dar um exemplo com nosso cenário de Acesso Condicional. O usuário final acabou de desembarcar no site e não tem uma sessão. Executamos uma chamada `login()`, obtemos um token de ID sem autenticação multifator. Em seguida, o usuário pressiona um botão que requer que o aplicativo solicite dados de uma API Web. O aplicativo tenta `acquireToken()` fazer uma chamada, mas falha, já que o usuário ainda não realizou a autenticação multifatorial e precisa cumprir a política de Acesso Condicional.

O Azure AD envia a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nosso aplicativo precisa capturar `error=interaction_required`. O aplicativo pode usar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O usuário é forçado a fazer uma autenticação multifator. Depois que o usuário conclui a autenticação multifator, o aplicativo recebe um novo token de acesso para o recurso solicitado.

Para testar esse cenário, veja nosso [exemplo de código Em nome de SPA JS](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Esta amostra de código usa a política de acesso condicional e a API web que você registrou anteriormente com um JS SPA para demonstrar esse cenário. Ele mostra como tratar corretamente o desafio claims e obter um token de acesso que pode ser usado para sua API Web. Como alternativa, verifique o [exemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) geral para obter orientação sobre um SPA Angular

## <a name="see-also"></a>Confira também

* Para saber mais sobre os recursos, confira [Acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Para obter mais exemplos de código do Azure AD, confira o [Repositório de exemplos de código do GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Para saber mais sobre SDKs da ADAL e como acessar a documentação de referência, confira o [guia da biblioteca](active-directory-authentication-libraries.md).
* Para saber mais sobre cenários de multilocatários, confira [Como conectar usuários usando o padrão de multilocatário](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
