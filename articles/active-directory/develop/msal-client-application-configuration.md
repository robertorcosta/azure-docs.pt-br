---
title: Configuração do aplicativo cliente (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Conheça as opções de configuração para aplicativos clientes públicos e confidenciais usando a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0262d22ae00456ce06cb8efbf995f1a093b20043
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262887"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicativos

Em seu código, você inicializa um novo aplicativo cliente público ou confidencial (ou usuário-agente do MSAL.js) para autenticar e adquirir tokens. Você pode definir uma série de opções de configuração ao inicializar o aplicativo cliente na Microsoft Authentication Library (MSAL). Essas opções se enquadram em dois grupos:

- Opções de registro, incluindo:
    - [Autoridade](#authority) (composta pela [instância](#cloud-instance) do provedor de identidade e [audiência](#application-audience) de login para o aplicativo, e possivelmente o ID do inquilino).
    - [ID do cliente](#client-id).
    - [Redirecionar uri](#redirect-uri).
    - [Segredo do cliente](#client-secret) (para aplicações confidenciais do cliente).
- [Opções de registro,](#logging)incluindo nível de registro, controle de dados pessoais e o nome do componente usando a biblioteca.

## <a name="authority"></a>Authority

A autoridade é uma URL que indica um diretório do que o MSAL pode solicitar tokens. As autoridades comuns são:

- https\:\<//login.microsoftonline.com/\>inquilino &lt;/, onde o inquilino&gt; é o ID inquilino do inquilino do Azure Active Directory (Azure AD) ou um domínio associado a este inquilino Azure AD. Usado apenas para fazer login em usuários de uma organização específica.
- https//login.microsoftonline.com/common/.\: Costumava fazer login em usuários com contas de trabalho e escola ou contas pessoais da Microsoft.
- https/login.microsoftonline.com/organizations/.\: Costumava fazer login em usuários com contas de trabalho e escola.
- https//login.microsoftonline.com/consumers/.\: Costumava fazer login em usuários com apenas contas pessoais da Microsoft (anteriormente conhecidas como contas Windows Live ID).

A configuração de autoridade precisa ser consistente com o que é declarado no portal de registro de aplicativos.

A URL da autoridade é composta pela instância e pelo público.

A autoridade pode ser:
- Uma autoridade em nuvem azure.
- Uma autoridade Azure AD B2C. Veja [as especificidades do B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Uma autoridade da Active Directory Federation Services (AD FS). Consulte [o suporte a AD FS](https://aka.ms/msal-net-adfs-support).

As autoridades de nuvem do Azure AD têm duas partes:
- A *instância* do provedor de identidade
- A *audiência* de login para o aplicativo

A instância e o público podem ser concatenados e fornecidos como url de autoridade. Em versões de MSAL.NET antes do MSAL 3. *x*, você tinha que compor a autoridade você mesmo, com base na nuvem que você queria segmentar e na audiência de login.  Este diagrama mostra como a URL de autoridade é composta:

![Como a URL de autoridade é composta](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Exemplo de nuvem

A *instância* é usada para especificar se seu aplicativo está assinando usuários da nuvem pública do Azure ou de nuvens nacionais. Usando o MSAL em seu código, você pode definir a instância de nuvem do Azure `Instance` usando uma enumeração ou passando a URL para a instância nacional da [nuvem](authentication-national-cloud.md#azure-ad-authentication-endpoints) como membro (se você souber).

MSAL.NET lançará uma exceção explícita se ambos `Instance` forem `AzureCloudInstance` especificados.

Se você não especificar uma instância, seu aplicativo terá como alvo a `https://login.onmicrosoftonline.com`instância de nuvem pública do Azure (a instância da URL ).

## <a name="application-audience"></a>Audiência de aplicativos

O público-alvo depende das necessidades de negócios do seu aplicativo:
- Se você é um desenvolvedor de linha de negócios (LOB), provavelmente produzirá um aplicativo de um único inquilino que será usado apenas em sua organização. Nesse caso, você precisa especificar a organização, seja pelo seu ID de inquilino (o ID da sua instância Azure AD) ou por um nome de domínio associado à instância Azure AD.
- Se você é um ISV, você pode querer fazer login com usuários com suas contas de trabalho e escola em qualquer organização ou em algumas organizações (aplicativo multilocatário). Mas você também pode querer que os usuários entrem com suas contas pessoais da Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público em seu código/configuração

Usando msal em seu código, você especifica o público usando um dos seguintes valores:
- A enumeração da audiência da autoridade Azure AD
- A id do inquilino, que pode ser:
  - Um GUID (o ID da sua instância Azure AD), para aplicativos de um único inquilino
  - Um nome de domínio associado à sua instância Azure AD (também para aplicativos de inquilino único)
- Um desses espaços reservados como um id inquilino no lugar da enumeração da audiência de autoridade azure AD:
    - `organizations`para uma aplicação multilocatário
    - `consumers`para fazer login usuários apenas com suas contas pessoais
    - `common`para fazer login usuários com suas contas de trabalho e escola ou suas contas pessoais da Microsoft

A MSAL lançará uma exceção significativa se você especificar tanto a audiência de autoridade azure AD quanto o ID do inquilino.

Se você não especificar um público, seu aplicativo terá como alvo o Azure AD e contas pessoais da Microsoft como público. (Ou seja, ele vai `common` se comportar como se fosse especificado.)

### <a name="effective-audience"></a>Público efetivo

O público efetivo para sua aplicação será o mínimo (se houver um cruzamento) do público que você definir em seu aplicativo e do público especificado no registro do aplicativo. Na verdade, a experiência [de registros](https://aka.ms/appregistrations) do App permite especificar o público (os tipos de conta suportados) para o aplicativo. Para obter mais informações, consulte [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md).

Atualmente, a única maneira de obter um aplicativo para fazer login em usuários com apenas contas pessoais da Microsoft é configurar ambas as configurações:
- Defina o público `Work and school accounts and personal accounts`de registro do aplicativo para .
- Defina o público em `AadAuthorityAudience.PersonalMicrosoftAccount` seu `TenantID` código/configuração para (ou ="consumidores").

## <a name="client-id"></a>ID do Cliente

O ID do cliente é o ID de aplicativo (cliente) exclusivo atribuído ao seu aplicativo pelo Azure AD quando o aplicativo foi registrado.

## <a name="redirect-uri"></a>URI de redirecionamento

O URI de redirecionamento é o URI para o que o provedor de identidade enviará os tokens de segurança de volta.

### <a name="redirect-uri-for-public-client-apps"></a>Redirecionar uri para aplicativos de clientes públicos

Se você é um desenvolvedor de aplicativos cliente público que está usando o MSAL:
- Você gostaria de `.WithDefaultRedirectUri()` usar em aplicativos desktop ou UWP (MSAL.NET 4.1+). Esse método definirá a propriedade uri de redirecionamento do aplicativo cliente público para o uri de redirecionamento recomendado padrão para aplicações de clientes públicos. 

  Plataforma  | URI de redirecionamento  
  ---------  | --------------
  Aplicativo para desktop (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | valor `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`de . Isso permite o SSO com o navegador definindo o valor para o resultado do WebAuthenticationBroker.GetCurrentApplicationCallbackUri() que você precisa registrar
  .NET Core | `https://localhost`. Isso permite que o usuário use o navegador do sistema para autenticação interativa, já que o .NET Core não tem uma interface do usuário para a visualização da Web incorporada no momento.

- Você não precisa adicionar um URI de redirecionamento se estiver construindo um aplicativo Xamarin Android e iOS que `msal{ClientId}://auth` não suporta broker (o URI redirecionamento é automaticamente definido para Xamarin Android e iOS

- Você precisa configurar o URI de redirecionamento nos [registros do Aplicativo](https://aka.ms/appregistrations):

   ![Redirecionar uri em registros de aplicativos](media/msal-client-application-configuration/redirect-uri.png)

Você pode substituir o URI de `RedirectUri` redirecionamento usando a propriedade (por exemplo, se você usar corretores). Aqui estão alguns exemplos de URIs de redirecionamento para esse cenário:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $"msauth. {Bundle.ID}://auth";

Para obter detalhes adicionais do iOS, consulte [os aplicativos Migrar para iOS que usam o Microsoft Authenticator de ADAL.NET a MSAL.NET](msal-net-migration-ios-broker.md) e aproveitar o corretor no [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Para obter mais detalhes do Android, consulte [Auth Intermediado no Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Redirecionar uri para aplicativos clientes confidenciais

Para aplicativos web, o URI redirecionar (ou responder URI) é o URI que o Azure AD usará para enviar o token de volta ao aplicativo. Este URI pode ser a URL do aplicativo web/API da Web se o aplicativo confidencial for um desses. O URI de redirecionamento precisa ser registrado no registro do aplicativo. Este registro é especialmente importante quando você implanta um aplicativo que você testou inicialmente localmente. Em seguida, você precisa adicionar a URL de resposta do aplicativo implantado no portal de registro de aplicativos.

Para aplicativos daemon, você não precisa especificar um URI de redirecionamento.

## <a name="client-secret"></a>Segredo do cliente

Esta opção especifica o segredo do cliente para o aplicativo cliente confidencial. Esse segredo (senha do aplicativo) é fornecido pelo portal de registro do aplicativo ou fornecido ao Azure AD durante o registro do aplicativo com o PowerShell AzureAD, PowerShell AzureRM ou Azure CLI.

## <a name="logging"></a>Registrando em log

As outras opções de configuração permitem o registro e a solução de problemas. Consulte o artigo [Logging](msal-logging.md) para obter detalhes sobre como usá-los.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [instanciar aplicativos clientes usando MSAL.NET](msal-net-initializing-client-applications.md).
Aprenda a [instanciar aplicativos clientes usando MSAL.js](msal-js-initializing-client-applications.md).
