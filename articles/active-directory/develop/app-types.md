---
title: Tipos de aplicativos na v1.0 | Azure
description: Descreve os tipos de aplicativos e cenários com suporte no ponto de extremidade do Azure Active Directory v2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72fab8a51f1ea1b9c7bda14d825dad538f96cde6
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374056"
---
# <a name="application-types-in-v10"></a>Tipos de aplicativos na v1.0

O Azure AD (Azure Active Directory) oferece suporte à autenticação para diversas arquiteturas de aplicativos modernos, que se baseiam nos protocolos padrão da indústria, OAuth 2.0 ou OpenID Connect.

O diagrama a seguir ilustra os cenários e os tipos de aplicativos e como os diferentes componentes podem ser adicionados:

![Cenários e tipos de aplicativos](./media/authentication-scenarios/application_types_and_scenarios.png)

Estes são os cinco cenários de aplicativo principais suportados pelo Azure AD:

- **[SPA (aplicativo de página única)](single-page-application.md)** : um usuário precisa entrar em um aplicativo de página única protegida pelo Azure AD.
- **[Navegador da Web para aplicativo Web](web-app.md)** : um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD.
- **[Aplicativo nativo para API Web](native-app.md)** : um aplicativo nativo executado em um telefone, tablet ou PC precisa autenticar um usuário para obter recursos de uma API Web protegida pelo Azure AD.
- **[Aplicativo Web para API Web](web-api.md)** : um aplicativo Web precisa obter recursos de uma API Web protegida pelo Azure AD.
- **[Servidor ou aplicativo daemon para API Web](service-to-service.md)** : um aplicativo daemon ou um aplicativo de servidor sem interface do usuário da Web precisa obter recursos de uma API Web protegida pelo Azure AD.

Siga os links para saber mais sobre cada tipo de aplicativo e compreender os cenários de alto nível antes de começar a trabalhar com o código. Você também pode aprender sobre as diferenças que precisa saber ao escrever um aplicativo específico que funciona com o ponto de extremidade v1.0 ou ponto de extremidade v2.0.

> [!NOTE]
> O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure AD. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).

Você pode desenvolver qualquer um dos aplicativos e cenários descritos aqui, usando várias linguagens e plataformas. Eles são apoiados por exemplos de código completos disponíveis no guia de exemplos de código: [exemplos de código v1.0 por cenário](sample-v1-code.md) e [exemplos de código v2.0 por cenário](sample-v2-code.md). Você também pode baixar os exemplos de código diretamente dos [repositórios de exemplo do GitHub](https://github.com/Azure-Samples?q=active-directory) correspondentes.

Além disso, se seu aplicativo precisar de uma parte ou segmento específico de um cenário de ponta a ponta, na maioria dos casos, essa funcionalidade pode ser adicionada independentemente. Por exemplo, se você tiver um aplicativo nativo que chama uma API da Web, você pode adicionar facilmente um aplicativo Web que também chama a API da Web.

## <a name="app-registration"></a>Registro do aplicativo

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Como registrar um aplicativo que usa o ponto de extremidade do Azure AD v 1.0

Qualquer aplicativo que terceirize a autenticação ao Azure AD deve ser registrado em um diretório. Esta etapa envolve comunicar o Azure AD sobre seu aplicativo, incluindo a URL da sua localização, a URL para enviar respostas após a autenticação, o URI para identificar seu aplicativo e mais. Essas informações são necessárias por alguns motivos:

* O Azure AD precisa se comunicar com o aplicativo ao lidar com tokens de logon ou troca. As informações transmitidas entre o Azure AD e o aplicativo incluem o seguinte:
  
  * **URI de ID do aplicativo**: o identificador de um aplicativo. Esse valor é enviado ao Azure AD durante a autenticação para indicar para qual aplicativo o chamador deseja ter um token. Além disso, esse valor é incluído no token para que o aplicativo saiba que ele era o destino pretendido.
  * **URL de resposta** e **URI de redirecionamento**: no caso de uma API da Web ou aplicativo Web, a URL de resposta é o local ao qual o Azure AD enviará a resposta de autenticação, incluindo um token caso a autenticação seja bem-sucedida. No caso de um aplicativo nativo, o URI de redirecionamento é um identificador exclusivo para o qual o Azure AD redirecionará o usuário-agente em uma solicitação do OAuth 2.0.
  * **ID do Aplicativo**: a ID de um aplicativo, que é gerada pelo Azure AD quando o aplicativo é registrado. Ao solicitar um código de autorização ou token, a ID do Aplicativo e a chave são enviadas ao Azure AD durante a autenticação.
  * **Chave**: a chave enviada com uma ID de Aplicativo durante a autenticação no Azure AD para chamar uma API da Web.
* O Azure AD precisa garantir que o aplicativo tem as permissões necessárias para acessar os dados do diretório, outros aplicativos em sua organização e assim por diante.

Para obter detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplicativos multilocatário e de único locatário

O provisionamento fica mais claro quando você entende que há duas categorias de aplicativos que podem ser desenvolvidos e integrados no Azure AD:

* **Aplicativo de locatário único**: um aplicativo de locatário único para uso em uma organização. Eles normalmente são aplicativos de linha de negócios (LoB) escritos por um desenvolvedor empresarial. Um aplicativo de locatário único só precisa ser acessado por usuários em um diretório e, como resultado, ele só precisa ser provisionado em um diretório. Esses aplicativos geralmente são registrados por um desenvolvedor na organização.
* **Aplicativos multilocatários**: um aplicativo multilocatário é destinado para uso em muitas organizações, não apenas em uma. Esses são normalmente aplicativos de software como serviço (SaaS) escritos por um fornecedor de software independente (ISV). Aplicativos multilocatários precisam ser provisionados em cada diretório em que serão usados, o que requer o consentimento do usuário ou administrador para registrá-los. Esse processo de consentimento é iniciado quando um aplicativo tiver sido registrado no diretório e receber acesso à API do Graph ou talvez a outra API da Web. Quando um usuário ou administrador de uma organização diferente se inscreve para usar o aplicativo, é apresentada uma caixa de diálogo que exibe as permissões exigidas pelo aplicativo. O usuário ou administrador pode então dar consentimento para o aplicativo, que fornece ao aplicativo acesso aos dados declarados e, por fim, registra o aplicativo em seu diretório. Para obter mais informações, consulte [Visão geral da estrutura de consentimento](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Considerações adicionais ao desenvolver aplicativos de locatário único ou multilocatário

Surgem algumas considerações adicionais ao desenvolver um aplicativo multilocatário em vez de um aplicativo de locatário único. Por exemplo, se você estiver disponibilizando seu aplicativo a usuários em vários diretórios, você precisa de um mecanismo para determinar em qual locatário eles estão. Um aplicativo de locatário único só precisa procurar em seu próprio diretório por um usuário, enquanto que um aplicativo multilocatário precisa identificar um usuário específico em todos os diretórios no Azure AD. Para realizar essa tarefa, o Azure AD fornece um ponto de extremidade de autenticação comum em que qualquer aplicativo multilocatário pode direcionar solicitações de entrada, em vez de um ponto de extremidade específico de locatário. Esse ponto de extremidade é https://login.microsoftonline.com/common para todos os diretórios no Azure AD, enquanto um ponto de extremidade específico do locatário pode ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto de extremidade comum deve ser considerado com especial importância ao desenvolver seu aplicativo, pois você precisará da lógica necessária para lidar com vários locatários durante o login, logout e validação de token.

Se você está desenvolvendo atualmente um aplicativo de locatário único, mas deseja disponibilizá-lo para muitas organizações, você pode facilmente fazer alterações no aplicativo e em sua configuração no Azure AD para habilitá-lo a multilocatários. Além disso, o Azure AD usa a mesma chave de assinatura para todos os tokens em todos os diretórios, independentemente de você estar fornecendo autenticação em um aplicativo de locatário único ou multilocatário.

Cada cenário listado neste documento inclui uma subseção que descreve seus requisitos de provisionamento. Para obter informações mais detalhadas sobre o provisionamento de um aplicativo no Azure AD e sobre as diferenças entre aplicativos de locatário único e multilocatário, confira [Integrar aplicativos com o Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md). Continue lendo para entender os cenários comuns de aplicativos no Azure AD.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre outras [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
