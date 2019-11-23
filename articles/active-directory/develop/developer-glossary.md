---
title: Microsoft identity platform developer glossary | Azure
description: A list of terms for commonly used Microsoft identity platform developer concepts and features.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221a491abad6c11ee12c75b1d69f1263f4abddc4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322606"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform developer glossary

This article contains definitions for some of the core developer concepts and terminology, which are helpful when learning about application development using Microsoft identity platform.

## <a name="access-token"></a>o token de acesso

Um tipo de [token de segurança](#security-token) emitido por um [servidor de autorização](#authorization-server) e usado por um [aplicativo cliente](#client-application) para acessar um [servidor de recursos protegido](#resource-server). Typically in the form of a [JSON Web Token (JWT)][JWT], the token embodies the authorization granted to the client by the [resource owner](#resource-owner), for a requested level of access. O token contém todas as [declarações](#claim) aplicáveis sobre a entidade, habilitando o aplicativo cliente a usá-lo como uma forma de credenciais ao acessar um recurso específico. Isso também elimina a necessidade do proprietário do recurso de expor as credenciais para o cliente.

Os tokens de acesso às vezes são chamados de "Usuário+Aplicativo" ou "Somente Aplicativo", dependendo das credenciais que estão sendo representadas. Por exemplo, quando um aplicativo cliente usa:

* [Concessão de autorização de "código de autorização"](#authorization-grant), o usuário final é autenticado primeiro como o proprietário do recurso, delegando a autorização ao cliente para acessar o recurso. O cliente é autenticado depois, ao obter o token de acesso. Às vezes, o token pode ser chamado mais especificamente de token de "Usuário+Aplicativo", que representa o usuário que autorizou o aplicativo cliente e o aplicativo.
* [Concessão de autorização de "credenciais de cliente"](#authorization-grant), o cliente fornece a única autenticação, funcionando sem a autenticação/autorização do proprietário do recurso. Portanto, às vezes o token pode ser chamado de token "Somente de Aplicativo".

See [Microsoft identity platform Token Reference][AAD-Tokens-Claims] for more details.

## <a name="application-id-client-id"></a>application ID (client ID)

Os problemas de identificador exclusivo do Azure AD para um registro de aplicativo que identifica um aplicativo específico e as configurações associadas. This application ID ([client ID](https://tools.ietf.org/html/rfc6749#page-15)) is used when performing authentication requests and is provided to the authentication libraries in development time. The application ID (client ID) is not a secret.

## <a name="application-manifest"></a>manifesto do aplicativo

A feature provided by the [Azure portal][AZURE-portal], which produces a JSON representation of the application's identity configuration, used as a mechanism for updating its associated [Application][AAD-Graph-App-Entity] and [ServicePrincipal][AAD-Graph-Sp-Entity] entities. See [Understanding the Azure Active Directory application manifest][AAD-App-Manifest] for more details.

## <a name="application-object"></a>objeto de aplicativo

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an application object and a corresponding [service principal object](#service-principal-object) for that tenant. O objeto de aplicativo *define* a configuração de identidade do aplicativo globalmente (em todos os locatários aos quais ele tem acesso), fornecendo um modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso localmente em tempo de execução (em um locatário específico).

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>registro de aplicativo

Para permitir que um aplicativo se integre e delegue funções de Gerenciamento de Acesso e Identidade ao Azure AD, ele deve ser registrado em um [locatário](#tenant)do Azure AD. Ao registrar seu aplicativo no Azure AD, você fornece uma configuração de identidade para o aplicativo, permitindo que ele se integre ao Azure AD e use recursos como:

* Robust management of Single Sign-On using Azure AD Identity Management and [OpenID Connect][OpenIDConnect] protocol implementation
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 [authorization server](#authorization-server)
* [Estrutura de consentimento](#consent) para gerenciar o acesso do cliente a recursos protegidos, com base na autorização do proprietário do recurso.

See [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] for more details.

## <a name="authentication"></a>autenticação

O ato de desafiar uma parte para o fornecimento de credenciais legítimas, fornecendo a base para a criação de uma entidade de segurança a ser usada para controle de identidade e acesso. Durante uma [concessão de autorização OAuth2](#authorization-grant), por exemplo, a parte que está realizando a autenticação está desempenhando a função de [proprietário do recurso](#resource-owner) ou [aplicativo cliente](#client-application), dependendo da concessão usada.

## <a name="authorization"></a>authorization

O ato de conceder a uma entidade de segurança autenticada permissão para fazer algo. Há dois casos de uso principais no modelo de programação do Azure AD:

* Durante um fluxo de [concessão de autorização OAuth2](#authorization-grant): quando o [proprietário do recurso](#resource-owner) concede autorização para o [aplicativo cliente](#client-application), permitindo que o cliente acesse recursos do proprietário do recurso.
* Durante o acesso a recursos pelo cliente: conforme implementado pelo [servidor de recursos](#resource-server), usando os valores de [declaração](#claim) presentes no [token de acesso](#access-token) para tomar decisões de controle de acesso com base neles.

## <a name="authorization-code"></a>código de autorização

Um "token" de curta duração fornecido a um [aplicativo cliente](#client-application) pelo [ponto de extremidade de autorização](#authorization-endpoint), como parte do fluxo de "código de autorização", uma das quatro [concessões de autorização](#authorization-grant) OAuth2. O código é retornado para o aplicativo cliente em resposta à autenticação de um [proprietário do recurso](#resource-owner), indicando que o proprietário do recurso delegou autorização para acessar os recursos solicitados. Como parte do fluxo, o código é resgatado posteriormente para um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto de extremidade de autorização

Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server), usado para interagir com o [proprietário do recurso](#resource-owner) para fornecer uma [concessão de autorização](#authorization-grant) durante um fluxo de concessão de autorização OAuth2. Dependendo do fluxo de concessão de autorização usado, a concessão real fornecida pode variar, incluindo um [código de autorização](#authorization-code) ou um [token de segurança](#security-token).

See the OAuth2 specification's [authorization grant types][OAuth2-AuthZ-Grant-Types] and [authorization endpoint][OAuth2-AuthZ-Endpoint] sections, and the [OpenIDConnect specification][OpenIDConnect-AuthZ-Endpoint] for more details.

## <a name="authorization-grant"></a>concessão de autorização

Uma credencial que representa a [autorização](#resource-owner) [do proprietário do recurso](#authorization) para acessar seus recursos protegidos, concedida a um [aplicativo cliente](#client-application). A client application can use one of the [four grant types defined by the OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] to obtain a grant, depending on client type/requirements: "authorization code grant", "client credentials grant", "implicit grant", and "resource owner password credentials grant". A credencial retornada ao cliente é um [token de acesso](#access-token) ou um [código de autorização](#authorization-code) (posteriormente trocado por um token de acesso), dependendo do tipo de concessão de autorização usado.

## <a name="authorization-server"></a>servidor de autorização

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], the server responsible for issuing access tokens to the [client](#client-application) after successfully authenticating the [resource owner](#resource-owner) and obtaining its authorization. Um [aplicativo cliente](#client-application) interage com o servidor de autorização em runtime por meio de seus pontos de extremidade de [autorização](#authorization-endpoint) e [token](#token-endpoint), de acordo com as [concessões de autorização](#authorization-grant) definidas pelo OAuth2.

In the case of Microsoft identity platform application integration, Microsoft identity platform implements the authorization server role for Azure AD applications and Microsoft service APIs, for example [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>declaração

Um [token de segurança](#security-token) contêm declarações, que fornecem asserções sobre uma entidade (como um [aplicativo cliente](#client-application) ou um [proprietário de recursos](#resource-owner)) para outra entidade (como o [servidor de recursos](#resource-server)). As declarações são pares de nome/valor que transmitem fatos sobre a entidade do token (por exemplo, a entidade de segurança que foi autenticada pelo [servidor de autorização](#authorization-server)). As declarações presentes em um token específico dependem de diversas variáveis, incluindo o tipo de token, o tipo de credencial usado para autenticar a entidade, a configuração de aplicativo etc.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="client-application"></a>aplicativo cliente

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an application that makes protected resource requests on behalf of the [resource owner](#resource-owner). O termo "cliente" não implica características de implementação de hardware específicas (por exemplo, se o aplicativo é executado em um servidor, na área de trabalho ou em outros dispositivos).

Um aplicativo cliente solicita [autorização](#authorization) de um proprietário de recurso para participar de um fluxo de [concessão de autorização OAuth2](#authorization-grant) e pode acessar APIs/dados em nome do proprietário do recurso. The OAuth2 Authorization Framework [defines two types of clients][OAuth2-Client-Types], "confidential" and "public", based on the client's ability to maintain the confidentiality of its credentials. Os aplicativos podem implementar um [cliente Web (confidencial)](#web-client) que é executado em um servidor Web, um [cliente nativo (público)](#native-client) instalado em um dispositivo ou um [cliente baseado em agente de usuário (público)](#user-agent-based-client) que é executado no navegador do dispositivo.

## <a name="consent"></a>consentimento

O processo para que um [proprietário do recurso](#resource-owner) conceda autorização a um [aplicativo cliente](#client-application), para acessar recursos protegidos em [permissões](#permissions) específicas, em nome do proprietário do recurso. Dependendo das permissões solicitadas pelo cliente, um administrador ou usuário deverá consentir o acesso a seus dados da empresa/individuais, respectivamente. Observe que, em um cenário de [multilocatário](#multi-tenant-application), a [entidade de serviço](#service-principal-object) do aplicativo também é registrada no locatário do usuário isso que fornece o consentimento.

Veja [estrutura de consentimento](consent-framework.md) para obter mais informações.

## <a name="id-token"></a>token de ID

An [OpenID Connect][OpenIDConnect-ID-Token] [security token](#security-token) provided by an [authorization server's](#authorization-server) [authorization endpoint](#authorization-endpoint), which contains [claims](#claim) pertaining to the authentication of an end user [resource owner](#resource-owner). Like an access token, ID tokens are also represented as a digitally signed [JSON Web Token (JWT)][JWT]. Diferentemente de um token de acesso, as declarações de um token de ID não são usadas para fins relacionados ao acesso a recursos e ao controle de acesso especificamente.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="microsoft-identity-platform"></a>Plataforma de identidade da Microsoft

A plataforma de identidade da Microsoft é uma evolução da plataforma de desenvolvedor e serviço de identidade do Azure AD (Azure Active Directory). Ela permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtêm tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs que os desenvolvedores criaram. Trata-se de uma plataforma completa que consiste em um serviço de autenticação, bibliotecas, registro de aplicativo e configuração, documentação completa do desenvolvedor, exemplos de código e outros conteúdos do desenvolvedor. A plataforma de identidade da Microsoft dá suporte a protocolos padrão do setor, como OAuth 2.0 e OpenID Connect. Veja [Sobre a plataforma de identidade da Microsoft](about-microsoft-identity-platform.md) para obter mais detalhes.

## <a name="multi-tenant-application"></a>Aplicativos multilocatários

Uma classe de aplicativo que permite se conectar e [consentir](#consent) por usuários provisionados em qualquer [locatário](#tenant) do Azure AD, incluindo locatários diferentes daquele em que o cliente está registrado. Aplicativos de [cliente nativo](#native-client) são multilocatários por padrão, enquanto aplicativos de [cliente Web](#web-client) e [recurso da Web/API](#resource-server) têm a capacidade de selecionar locatário único ou multilocatário. Por outro lado, um aplicativo Web registrado como locatário único só permitirá conexões de contas de usuário provisionadas no mesmo locatário que aquele em que o aplicativo está registrado.

See [How to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] for more details.

## <a name="native-client"></a>cliente nativo

Um tipo de [aplicativo cliente](#client-application) que é instalado de forma nativa em um dispositivo. Como todo o código é executado em um dispositivo, ele é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma particular/confidencial. See [OAuth2 client types and profiles][OAuth2-Client-Types] for more details.

## <a name="permissions"></a>permissões

Um [aplicativo cliente](#client-application) obtém acesso a um [servidor de recursos](#resource-server) declarando solicitações de permissão. Dois tipos estão disponíveis:

* Permissões “delegadas”, que especificam o acesso [com base no escopo](#scopes) usando a autorização delegada do [proprietário do recurso](#resource-owner) conectado, são apresentadas para o recurso em tempo de execução como [declarações “scp”](#claim) no [token de acesso](#access-token) do cliente.
* Permissões de “aplicativo”, que especificam o acesso [baseado em função](#roles) usando as credenciais/identidade do aplicativo cliente, são apresentadas para o recurso em tempo de execução como [declarações de “funções”](#claim) no token de acesso do cliente.

Também surgem durante o processo de [consentimento](#consent) , oferecendo ao administrador ou ao proprietário do recurso a oportunidade de conceder/negar ao cliente o acesso aos recursos em seu locatário.

Permission requests are configured on the **API permissions** page for an application in the [Azure portal][AZURE-portal], by selecting the desired "Delegated Permissions" and "Application Permissions" (the latter requires membership in the Global Admin role). Como um [cliente público](#client-application) não pode manter credenciais com segurança, ele só pode solicitar permissões delegadas, enquanto um [cliente confidencial](#client-application) tem a capacidade de solicitar permissões delegadas e de aplicativo. The client's [application object](#application-object) stores the declared permissions in its [requiredResourceAccess property][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietário do recurso

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an entity capable of granting access to a protected resource. Quando o proprietário do recurso é uma pessoa, é chamado de usuário final. For example, when a [client application](#client-application) wants to access a user's mailbox through the [Microsoft Graph API][Microsoft-Graph], it requires permission from the resource owner of the mailbox.

## <a name="resource-server"></a>servidor de recursos

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], a server that hosts protected resources, capable of accepting and responding to protected resource requests by [client applications](#client-application) that present an [access token](#access-token). Também conhecido como um servidor de recursos protegidos ou aplicativo de recurso.

Um servidor de recursos expõe APIs e impõe o acesso a seus recursos protegidos por meio de [escopos](#scopes) e [funções](#roles), usando a Estrutura de Autorização OAuth 2.0. Alguns exemplos são a API do Graph do Azure AD, que fornece acesso aos dados de locatário do Azure AD, e APIs do Office 365, que fornecem acesso a dados como email e calendário. Both of these are also accessible via the [Microsoft Graph API][Microsoft-Graph].

Assim como um aplicativo cliente, a configuração de identidade do aplicativo de recurso é estabelecida via [registro](#application-registration) em um locatário do Azure AD, fornecendo o objeto de entidade de serviço e de aplicativo. Algumas APIs fornecidas pela Microsoft, como a API do Graph do Azure AD, têm entidades de serviço previamente registradas disponíveis em todos os locatários durante o provisionamento.

## <a name="roles"></a>roles

Assim como os [escopos](#scopes), as funções fornecem uma maneira para que um [servidor de recursos](#resource-server) governe o acesso a seus recursos protegidos. Há dois tipos: uma função de "usuário" implementa o controle de acesso baseado em função para usuários/grupos que exigem acesso ao recurso, enquanto uma função de "aplicativo" implementa o mesmo para [aplicativos cliente](#client-application) que requerem acesso.

Roles are resource-defined strings (for example "Expense approver", "Read-only", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [appRoles property][AAD-Graph-Sp-Entity]. O Portal do Azure também é usado para atribuir usuários às funções de "usuário" e configurar [permissões de aplicativo](#permissions) do cliente para acessar uma função de "aplicativo".

For a detailed discussion of the application roles exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For a step-by-step implementation example, see [Manage access using RBAC and the Azure portal][AAD-RBAC].

## <a name="scopes"></a>escopos

Assim como as [funções](#roles), os escopos fornecem uma maneira para que um [servidor de recursos](#resource-server) governe o acesso a seus recursos protegidos. Scopes are used to implement [scope-based][OAuth2-Access-Token-Scopes] access control, for a [client application](#client-application) that has been given delegated access to the resource by its owner.

Scopes are resource-defined strings (for example "Mail.Read", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [oauth2Permissions property][AAD-Graph-Sp-Entity]. O Portal do Azure também é usado para configurar [permissões delegadas](#permissions) do aplicativo cliente para acessar um escopo.

Uma prática recomendada para a convenção de nomenclatura é usar um formato "resource.operation.constraint". For a detailed discussion of the scopes exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For scopes exposed by Office 365 services, see [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>token de segurança

Um documento assinado que contém declarações, como um token OAuth2 ou uma asserção SAML 2.0. For an OAuth2 [authorization grant](#authorization-grant), an [access token](#access-token) (OAuth2) and an [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) are types of security tokens, both of which are implemented as a [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objeto de entidade de serviço

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an [application object](#application-object) and a corresponding service principal object for that tenant. O objeto de aplicativo *define* a configuração de identidade do aplicativo globalmente (em todos os locatários em que o aplicativo associado recebeu acesso) e é o modelo do qual seus objetos de entidade de serviço correspondentes são *derivados* para uso localmente em tempo de execução (em um locatário específico).

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>entrada

O processo para que um [aplicativo cliente](#client-application) inicie a autenticação do usuário final e capture o estado relacionado, para adquirir um [token de segurança](#security-token) e obter o escopo da sessão do aplicativo para esse estado. O estado pode incluir artefatos, como informações de perfil de usuário, e informações derivadas de declarações de token.

A função de entrada de um aplicativo normalmente é usada para implementar o SSO (logon único). Ela também pode ser precedida por uma função de "inscrição", como o ponto de entrada para um usuário final obter acesso a um aplicativo (após a primeira entrada). A função de inscrição é usada para coletar e persistir o estado adicional específico do usuário e pode exigir o [consentimento do usuário](#consent).

## <a name="sign-out"></a>saída

O processo de cancelamento de autenticação de um usuário final, desanexando o estado do usuário associado à sessão do [aplicativo cliente](#client-application) durante a [entrada](#sign-in)

## <a name="tenant"></a>locatário

Uma instância de um diretório do Azure AD é chamada de locatário do Azure AD. Ele fornece vários recursos, incluindo:

* um serviço de registro para aplicativos integrados
* autenticação de contas de usuário e aplicativos registrados
* Pontos de extremidade REST necessários para dar suporte a vários protocolos, incluindo OAuth2 e SAML, incluindo [ponto de extremidade de autorização](#authorization-endpoint), [ponto de extremidade de token](#token-endpoint) e ponto de extremidade "comum" usado por [aplicativos de multilocação](#multi-tenant-application).

Locatários do Azure AD são criados/associados com assinaturas do Azure e o Office 365 durante a inscrição, fornecendo recursos de Gerenciamento de Identidades e Acesso para a assinatura. Os administradores de assinatura do Azure também podem criar locatários do Azure AD adicionais por meio do Portal do Azure. See [How to get an Azure Active Directory tenant][AAD-How-To-Tenant] for details on the various ways you can get access to a tenant. See [How Azure subscriptions are associated with Azure Active Directory][AAD-How-Subscriptions-Assoc] for details on the relationship between subscriptions and an Azure AD tenant.

## <a name="token-endpoint"></a>ponto de extremidade de token

Um dos pontos de extremidade implementados pelo [servidor de autorização](#authorization-server) para dar suporte a [concessões de autorização](#authorization-grant) OAuth2. Depending on the grant, it can be used to acquire an [access token](#access-token) (and related "refresh" token) to a [client](#client-application), or [ID token](#id-token) when used with the [OpenID Connect][OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>Cliente com base em agente de usuário

Um tipo de [aplicativo cliente](#client-application) que baixa código de um servidor Web e é executado em um agente de usuário (por exemplo, um navegador da Web), como um SPA (aplicativo de página única). Como todo o código é executado em um dispositivo, ele é considerado um cliente "público" devido à sua incapacidade de armazenar credenciais de forma particular/confidencial. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="user-principal"></a>entidade de usuário

Da mesma forma como um objeto de entidade de serviço é usado para representar uma instância de aplicativo, um objeto de entidade de usuário é outro tipo de entidade de segurança, que representa um usuário. The Azure AD Graph [User entity][AAD-Graph-User-Entity] defines the schema for a user object, including user-related properties such as first and last name, user principal name, directory role membership, etc. This provides the user identity configuration for Azure AD to establish a user principal at run-time. A entidade de usuário é usada para representar um usuário autenticado para Logon Único, gravando a delegação de [consentimento](#consent), tomando decisões de controle de acesso etc.

## <a name="web-client"></a>cliente Web

Um tipo de [aplicativo cliente](#client-application) que executa todo o código em um servidor Web e pode funcionar como um cliente "confidencial" armazenando com segurança suas credenciais no servidor. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="next-steps"></a>Próximos passos

The [Microsoft identity platform Developer's Guide][AAD-Dev-Guide] is the landing page to use for all Microsoft identity platform development-related topics, including an overview of [application integration][AAD-How-To-Integrate] and the basics of [Microsoft identity platform authentication and supported authentication scenarios][AAD-Auth-Scenarios]. Você também pode encontrar exemplos de código e tutoriais sobre como iniciar rapidamente o [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Use a seção de comentários a seguir para fornecer comentários e ajudar a refinar e moldar esse conteúdo, incluindo solicitações de novas definições ou atualizando as existentes!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
