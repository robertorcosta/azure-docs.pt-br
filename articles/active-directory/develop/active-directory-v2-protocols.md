---
title: Protocolos OAuth 2.0 e OpenID Connect - plataforma de identidade Microsoft | Azure
description: Um guia para os protocolos OAuth 2.0 e OpenID Connect que são suportados pelo ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4847bddcbcfbc27502965efa221a3707fa453457
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885660"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft

O ponto final da plataforma de identidade da Microsoft para identidade como serviço com protocolos padrão do setor, OpenID Connect e OAuth 2.0. Embora o serviço esteja em conformidade com o padrão, pode haver diferenças sutis entre duas implementações diferentes desses protocolos. As informações descritas aqui serão úteis se você optar por criar seu código enviando e tratando solicitações HTTP diretamente, ou usar uma biblioteca de software livre de terceiros ao invés de usar uma de nossas [bibliotecas de software livre](reference-v2-libraries.md).

> [!NOTE]
> Nem todos os cenários e recursos do Azure AD são suportados pelo ponto final da plataforma de identidade da Microsoft. Para determinar se você deve usar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos do OAuth 2.0 e do OpenID Connect, há quatro partes envolvidas na troca:

![Diagrama mostrando as funções OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* O **Servidor de Autorização** é o ponto final da plataforma de identidade da Microsoft e responsável por garantir a identidade do usuário, conceder e revogar o acesso aos recursos e emitir tokens. O servidor de autorização também é conhecido como provedor de identidade; ele trata com segurança tudo que estiver relacionado às informações do usuário, seu acesso e as relações de confiança entre as partes de um fluxo.
* O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados ou recurso.
* O **Cliente OAuth** é o seu aplicativo, identificado pela respectiva ID de Aplicativo. Geralmente é a parte com a qual usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão do proprietário do recurso para acessar o recurso.
* O **Servidor de Recurso** é o local no qual o recurso ou os dados residem. Ele confia no Servidor de Autorização para autenticar e autorizar com segurança o Cliente OAuth e usa tokens de acesso ao Portador para garantir que o acesso a um recurso possa ser concedido.

## <a name="app-registration"></a>Registro do aplicativo

Todo aplicativo que deseja aceitar contas pessoais e de trabalho ou escola deve ser registrado através da experiência **de inscrições** do App no [portal Azure](https://aka.ms/appregistrations) antes de poder assinar esses usuários usando OAuth 2.0 ou OpenID Connect. O processo de registro de aplicativo coleta e atribui alguns valores a seu aplicativo:

* Um **ID de aplicativo** que identifica exclusivamente seu aplicativo
* Um **URI redirecionado** (opcional) que pode ser usado para direcionar respostas de volta ao seu aplicativo
* Alguns outros valores específicos de cenário.

Para obter mais detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

## <a name="endpoints"></a>Pontos de extremidade

Uma vez registrado, o aplicativo se comunica com a plataforma de identidade da Microsoft enviando solicitações para o ponto final:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Em que o `{tenant}` pode ter um de quatro valores diferente:

| Valor | Descrição |
| --- | --- |
| `common` | Permite que os usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `organizations` | Permite que somente usuários com contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `consumers` | Permite que somente os usuários com MSA (Contas Pessoais da Microsoft) para entrem no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite que somente os usuários com contas corporativas/de estudante de um locatário específico do Azure AD entrem no aplicativo. É possível usar o nome de domínio amigável do locatário do Azure AD ou o identificador GUID de locatário. |

Para saber mais sobre como interagir com esses pontos de extremidade, escolha um tipo específico de aplicativo na seção [Protocolos](#protocols) e siga os links.

> [!TIP]
> Qualquer aplicativo registrado no Azure AD pode usar o ponto final da plataforma de identidade da Microsoft, mesmo que eles não façam login em contas pessoais.  Dessa forma, você pode migrar aplicativos existentes para a plataforma de identidade Microsoft e [mSAL](reference-v2-libraries.md) sem recriar seu aplicativo.  

## <a name="tokens"></a>Tokens

A implementação da plataforma de identidade Microsoft do OAuth 2.0 e do OpenID Connect faz uso extensivo de tokens portadores, incluindo tokens de portador representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deve primeiro autenticar com a plataforma de identidade da Microsoft para receber o token do portador, se as medidas necessárias não forem tomadas para proteger o token na transmissão e armazenamento, ele pode ser interceptado e usado por uma parte não intencional. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, uma parte mal-intencionada pode usar um ataque "man-in-the-middle" para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](https://tools.ietf.org/html/rfc6750).

Mais detalhes sobre diferentes tipos de tokens usados no ponto final da plataforma de identidade da Microsoft estão disponíveis na [referência de token de ponto final da plataforma de identidade Microsoft](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Se você estiver pronto para ver alguns exemplos de solicitação, inicie com um dos tutoriais a seguir. Cada um corresponde a um cenário de autenticação específico. Se você precisar de ajuda para determinar qual é o fluxo certo para você, confira [os tipos de aplicativos que você pode construir com a plataforma de identidade Microsoft](v2-app-types.md).

* [Construa aplicativo móvel e nativo com OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Criar aplicativos Web com o OpenID Connect](v2-protocols-oidc.md)
* [Criar aplicativos de página única com o fluxo implícito do OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Criar daemons ou processos do servidor com o fluxo de credenciais do cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obter tokens em uma API Web com o OAuth 2.0 em nome do fluxo](v2-oauth2-on-behalf-of-flow.md)
