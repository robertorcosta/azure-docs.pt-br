---
title: Orientação do desenvolvedor para acesso condicionado ao diretório ativo do Azure
description: Orientação de desenvolvedores e cenários para a plataforma de identidade Azure AD Conditional Access e Microsoft.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 03/16/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: aae1b8aa27363e8f1d3c72d3934146c47b0cf2c9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535886"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Orientação do desenvolvedor para acesso condicionado ao diretório ativo do Azure

O recurso Conditional Access no Azure Active Directory (Azure AD) oferece uma das várias maneiras que você pode usar para proteger seu aplicativo e proteger um serviço. O Conditional Access permite que desenvolvedores e clientes corporativos protejam os serviços de várias maneiras, incluindo:

* Autenticação multifator
* Permissão para que somente dispositivos inscritos no Intune acessem serviços específicos
* Restrição de locais de usuário e intervalos de IP

Para obter mais informações sobre os recursos completos do Acesso Condicional, consulte [Conditional Access no Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Para desenvolvedores que constroem aplicativos para o Azure AD, este artigo mostra como você pode usar o Conditional Access e também aprenderá sobre o impacto do acesso a recursos que você não tem controle sobre os quais podem ter políticas de acesso condicional aplicadas. O artigo também explora as implicações do Acesso Condicional no nome do fluxo, aplicativos web, acessando o Microsoft Graph e chamando APIs.

Conhecimento de aplicativos de [único locatário](quickstart-register-app.md) e [multilocatário](howto-convert-app-to-be-multi-tenant.md), além de [padrões comuns de autenticação](authentication-scenarios.md) é assumido.

> [!NOTE]
> O uso desse recurso requer uma licença do Azure AD Premium P1. Para localizar a licença correta para os requisitos, consulte [Comparar recursos geralmente disponíveis nas edições Gratuita, Básica e Premium](https://azure.microsoft.com/pricing/details/active-directory/).
> Os clientes com [licenças do Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a recursos de Acesso Condicional.

## <a name="how-does-conditional-access-impact-an-app"></a>Como o Conditional Access impacta um aplicativo?

### <a name="app-types-impacted"></a>Tipos de aplicativo afetados

Na maioria dos casos, o Conditional Access não altera o comportamento de um aplicativo ou requer qualquer alteração do desenvolvedor.Somente em certos casos em que um aplicativo solicita indiretamente ou silenciosamente um token para um serviço, um aplicativo requer alterações de código para lidar com os "desafios" do Acesso Condicional.O que pode ser tão simples quanto executar uma solicitação de entrada interativa.

Especificamente, os seguintes cenários exigem código para lidar com "desafios" de acesso condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando MSAL.js
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

Azure AD Conditional Access é um recurso incluído no [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Os clientes com [licenças do Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a recursos de Acesso Condicional.

### <a name="considerations-for-specific-scenarios"></a>Considerações para cenários específicos

As seguintes informações só se aplicam nestes cenários de Acesso Condicional:

* Aplicativos executando o fluxo em nome de
* Aplicativos acessando vários serviços/recursos
* Aplicativos de página única usando MSAL.js

As seções a seguir discutem cenários comuns que são mais complexos. O princípio principal de funcionamento é que as políticas de Acesso Condicional são avaliadas no momento em que o token é solicitado para o serviço que tem uma política de acesso condicional aplicada.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Cenário: aplicativo executando o fluxo em nome de

Nesse cenário, vamos acompanhar o caso em que um aplicativo nativo chama um serviço/API Web. Por sua vez, esse serviço faz o fluxo "em nome de" chamar um serviço downstream. No nosso caso, aplicamos nossa política de acesso condicional ao serviço de downstream (Web API 2) e estamos usando um aplicativo nativo em vez de um aplicativo de servidor/daemon.

![Diagrama de aplicativo executando o fluxo em nome de](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

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

Para testar esse cenário, veja nosso [exemplo de código .NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Ele demonstra como passar o desafio claims de volta da API Web 1 para o aplicativo nativo e construir uma nova solicitação dentro do aplicativo cliente.

## <a name="scenario-app-accessing-multiple-services"></a>Cenário: aplicativo acessando vários serviços

Neste cenário, caminhamos através do caso em que um aplicativo web acessa dois serviços, um dos quais tem uma política de Acesso Condicional atribuída. Dependendo da lógica do seu aplicativo, pode existir um caminho no qual seu aplicativo não exige acesso a ambos os serviços Web. Nesse cenário, a ordem na qual você solicita um token tem um papel importante na experiência do usuário final.

Vamos supor que temos o serviço web A e B e o serviço web B tem nossa política de Acesso Condicional aplicada. Embora a solicitação auth interativa inicial exija consentimento para ambos os serviços, a política de Acesso Condicional não é necessária em todos os casos. Se o aplicativo solicitar um token para o serviço Web B, a política será invocada e as solicitações subsequentes para o serviço Web A também serão bem-sucedidas, como se segue.

![Diagrama do fluxo de aplicativo acessando vários serviços](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternativamente, se o aplicativo solicitar inicialmente um token para o serviço web A, o usuário final não invocará a política de Acesso Condicional. Isso permite que o desenvolvedor do aplicativo controle a experiência do usuário final e não force a política de Acesso Condicional a ser invocada em todos os casos. O caso complicado é se o aplicativo solicitar posteriormente um token para o serviço web B. Neste ponto, o usuário final precisa cumprir a política de Acesso Condicional. Quando o aplicativo tenta `acquireToken`, ele pode gerar o seguinte erro (ilustrado no diagrama a seguir):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplicativo acessando vários serviços que solicitam um novo token](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se o aplicativo estiver usando a biblioteca MSAL, uma falha na aquisição do token é sempre repetida interativamente. Quando essa solicitação interativa ocorre, o usuário final tem a oportunidade de cumprir o Acesso Condicional. Isso é verdade, a `AcquireTokenSilentAsync` `PromptBehavior.Never` menos que a solicitação seja ```AcquireToken``` um ou nesse caso o aplicativo precisa realizar uma solicitação interativa para dar ao usuário final a oportunidade de cumprir a política.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Cenário: Aplicativo de página única (SPA) usando MSAL.js

Neste cenário, nós caminhamos sobre o caso quando temos um aplicativo de uma única página (SPA), usando O MSAL.js para chamar uma API web protegida de Acesso Condicional. Esta é uma arquitetura simples, mas tem algumas nuances que precisam ser levadas em conta ao se desenvolver em torno do Acesso Condicional.

No MSAL.js, existem algumas funções que `loginPopup()`obtêm tokens: , `acquireTokenSilent(...)`, `acquireTokenPopup(…)`e `acquireTokenRedirect(…)`.

* `loginPopup()`obtém um token de ID através de uma solicitação de login interativa, mas não obtém tokens de acesso para qualquer serviço (incluindo uma API web protegida de Acesso Condicional).
* `acquireTokenSilent(…)` pode ser usada para obter silenciosamente um token de acesso, o que significa que ela não mostra a interface do usuário em nenhuma circunstância.
* `acquireTokenPopup(…)`e `acquireTokenRedirect(…)` são usadas para solicitar interativamente um token para um recurso, o que significa que elas sempre mostram a interface do usuário de entrada.

Quando um aplicativo precisa de um token de acesso `acquireTokenSilent(…)`para chamar uma API web, ele tenta um . Se a sessão de token estiver expirada ou precisarmos cumprir uma política de Acesso Condicional, a função *AcquireToken* falhará e o aplicativo usará `acquireTokenPopup()` ou `acquireTokenRedirect()`.

![Aplicativo de página única usando diagrama de fluxo MSAL](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Vamos dar um exemplo com nosso cenário de Acesso Condicional. O usuário final apenas aterrissou no site e não tem uma sessão. Executamos uma chamada `loginPopup()`, obtemos um token de ID sem autenticação multifator. Em seguida, o usuário pressiona um botão que requer que o aplicativo solicite dados de uma API Web. O aplicativo tenta `acquireTokenSilent()` fazer uma chamada, mas falha, já que o usuário ainda não realizou a autenticação multifatorial e precisa cumprir a política de Acesso Condicional.

O Azure AD envia a seguinte resposta HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nosso aplicativo precisa capturar `error=interaction_required`. O aplicativo pode usar `acquireTokenPopup()` ou `acquireTokenRedirect()` no mesmo recurso. O usuário é forçado a fazer uma autenticação multifator. Depois que o usuário conclui a autenticação multifator, o aplicativo recebe um novo token de acesso para o recurso solicitado.

Para testar esse cenário, veja nosso [exemplo de código Em nome de SPA JS](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/Microsoft.Identity.Web/README.md#handle-conditional-access). Esta amostra de código usa a política de acesso condicional e a API web que você registrou anteriormente com um JS SPA para demonstrar esse cenário. Ele mostra como lidar adequadamente com o desafio de sinistros e obter um token de acesso que pode ser usado para sua API web. Como alternativa, verifique o [exemplo de código Angular.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) geral para obter orientação sobre um SPA Angular

## <a name="see-also"></a>Confira também

* Para saber mais sobre os recursos, confira [Acesso condicional no Azure Active Directory](/azure/active-directory/conditional-access/overview).
* Para obter mais amostras de código Azure AD, consulte [amostras](sample-v2-code.md).
* Para obter mais informações sobre os SDKs MSAL e acessar a documentação de referência, consulte a [visão geral da Biblioteca de Autenticação da Microsoft](msal-overview.md).
* Para saber mais sobre cenários de multilocatários, confira [Como conectar usuários usando o padrão de multilocatário](howto-convert-app-to-be-multi-tenant.md).
* Saiba mais sobre [o acesso condicional e a garantia de acesso a aplicativos IoT](/azure/architecture/example-scenario/iot-aad/iot-aad).
