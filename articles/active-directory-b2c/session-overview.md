---
title: Sessões de SSO no Azure Active Directory B2C | Microsoft Docs
description: Uma visão geral da configuração do comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e02323df3a12c4a74de1fb62b36762fc739e9e5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750432"
---
# <a name="azure-ad-b2c-session"></a>Sessão de Azure AD B2C

O SSO (logon único) adiciona segurança e conveniência quando os usuários entram em aplicativos em Azure Active Directory B2C (Azure AD B2C). Este artigo descreve os métodos de logon único usados no Azure AD B2C e ajuda a escolher o método de SSO mais apropriado ao configurar sua política.

Com o logon único, os usuários entram uma vez com uma única conta e obtêm acesso a vários aplicativos. O aplicativo pode ser um aplicativo Web, móvel ou de página única, independentemente da plataforma ou do nome de domínio.

Quando o usuário entra inicialmente em um aplicativo, Azure AD B2C persiste uma sessão baseada em cookie. Após as solicitações de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookies e emite um token de acesso sem solicitar que o usuário entre novamente. Se a sessão baseada em cookies expirar ou se tornar inválida, o usuário será solicitado a entrar novamente.  

## <a name="sso-session-types"></a>Tipos de sessão de SSO

A integração com o Azure AD B2C envolve três tipos de sessões de SSO:

- **Azure ad B2C** -sessão gerenciada por Azure ad B2C
- **Provedor de identidade federada** -sessão gerenciada pelo provedor de identidade, por exemplo, Facebook, Salesforce ou conta Microsoft
- **Aplicativo** -sessão gerenciada pelo aplicativo Web, móvel ou de página única

![Sessão de SSO](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

Quando um usuário é autenticado com êxito com uma conta local ou social, o Azure AD B2C armazena uma sessão baseada em cookie no navegador do usuário. O cookie é armazenado sob o nome de domínio do locatário Azure AD B2C, como `https://contoso.b2clogin.com` .

Se um usuário entrar inicialmente com uma conta federada e, durante a janela de tempo da sessão (vida útil ou TTL) entrar no mesmo aplicativo ou em um aplicativo diferente, Azure AD B2C tentará adquirir um novo token de acesso do provedor de identidade federada. Se a sessão do provedor de identidade federada tiver expirado ou for inválida, o provedor de identidade federada solicitará ao usuário suas credenciais. Se a sessão ainda estiver ativa (ou se o usuário tiver entrado com uma conta local em vez de uma conta federada), o Azure AD B2C autoriza o usuário e elimina solicitações adicionais.

Você pode configurar o comportamento da sessão, incluindo o TTL da sessão e como Azure AD B2C compartilha a sessão entre políticas e aplicativos.

### <a name="federated-identity-provider-sso"></a>SSO do provedor de identidade federada

Um provedor de identidade social ou empresarial gerencia sua própria sessão. O cookie é armazenado sob o nome de domínio do provedor de identidade, como `https://login.salesforce.com` . Azure AD B2C não controla a sessão do provedor de identidade federada. Em vez disso, o comportamento da sessão é determinado pelo provedor de identidade federada. 

Considere o seguinte cenário:

1. Um usuário entra no Facebook para verificar o feed.
2. Posteriormente, o usuário abre seu aplicativo e inicia o processo de entrada. O aplicativo redireciona o usuário para Azure AD B2C para concluir o processo de entrada.
3. Na página Azure AD B2C inscrição ou entrada, o usuário opta por entrar com sua conta do Facebook. O usuário é redirecionado para o Facebook. Se houver uma sessão ativa no Facebook, o usuário não receberá uma solicitação para fornecer suas credenciais e será redirecionado imediatamente para Azure AD B2C com um token do Facebook.

### <a name="application-sso"></a>SSO de aplicativo

Um aplicativo Web, móvel ou de página única pode ser protegido por acesso OAuth, tokens de ID ou tokens SAML. Quando um usuário tenta acessar um recurso protegido no aplicativo, o aplicativo verifica se há uma sessão ativa no lado do aplicativo. Se não houver nenhuma sessão de aplicativo ou a sessão tiver expirado, o aplicativo levará o usuário a Azure AD B2C para a página de entrada.

A sessão de aplicativo pode ser uma sessão baseada em cookie armazenada sob o nome de domínio do aplicativo, como `https://contoso.com` . Os aplicativos móveis podem armazenar a sessão de forma diferente, mas usando uma abordagem semelhante.

## <a name="azure-ad-b2c-session-configuration"></a>Configuração de sessão de Azure AD B2C

### <a name="session-scope"></a>Escopo de sessão

A sessão de Azure AD B2C pode ser configurada com os seguintes escopos:

- **Locatário** -essa configuração é o padrão. O uso dessa configuração permite a vários aplicativos e fluxos de usuário em seu locatário B2C compartilhar a mesma sessão de usuário. Por exemplo, quando um usuário entra em um aplicativo, o usuário também pode entrar diretamente em outro acesso ao acessá-lo.
- **Aplicação** - Esta configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independente de outros aplicativos. Por exemplo, você pode usar essa configuração se quiser que o usuário entre no contoso farmácia, independentemente de o usuário já estar conectado aos mercados da contoso.
- **Política** - Essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que a usam. Por exemplo, se o usuário já tiver entrado e concluído uma etapa de autenticação multifator (MFA), o usuário poderá receber acesso a partes de segurança mais alta de vários aplicativos, desde que a sessão vinculada ao fluxo do usuário não expire.
- **Desabilitado** – essa configuração força o usuário a executar todo o fluxo do usuário em cada execução da política.

### <a name="session-life-time"></a>Tempo de vida da sessão

O **tempo de vida da sessão** é a quantidade de tempo que o cookie de sessão Azure ad B2C é armazenado no navegador do usuário após a autenticação bem-sucedida. Você pode definir o tempo de vida da sessão como um valor de 15 a 720 minutos.

### <a name="keep-me-signed-in"></a>Mantenha-me conectado

O recurso " [manter-me conectado](custom-policy-keep-me-signed-in.md) " estende o tempo de vida da sessão por meio do uso de um cookie persistente. A sessão permanece ativa depois que o usuário fecha e reabre o navegador. A sessão é revogada somente quando um usuário se desconecta. O recurso conectado manter-me só se aplica à entrada com contas locais.

O recurso conectado manter-me tem precedência sobre o tempo de vida da sessão. Se o recurso conectado manter-me estiver habilitado e o usuário o selecionar, esse recurso determinará quando a sessão irá expirar. 

### <a name="session-expiry-type"></a>Tipo de expiração da sessão

O **tipo de expiração da sessão** indica como uma sessão é estendida pela configuração do tempo de vida da sessão ou pela configuração de manter-se conectado.

- **Sem interrupção** -indica que a sessão é estendida toda vez que o usuário executa uma autenticação baseada em cookie (padrão).
- **Absoluta** – indica que o usuário é forçado a autenticar novamente após o período de tempo especificado.

## <a name="sign-out"></a>Sair

Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão com o usuário. Você deve redirecionar o usuário para Azure AD B2C para sair. Caso contrário, o usuário poderá se autenticar novamente em seus aplicativos sem inserir suas credenciais novamente.

Após uma solicitação de saída, Azure AD B2C:

1. Invalida o Azure AD B2C sessão baseada em cookie.
1. Tentativas de sair de provedores de identidade federada:
   - OpenId Connect – se o ponto de extremidade de configuração bem conhecido do provedor de identidade especificar um `end_session_endpoint` local.
   - OAuth2 – se os [metadados do provedor de identidade](oauth2-technical-profile.md#metadata) contiverem o `end_session_endpoint` local.
   - SAML-se os [metadados do provedor de identidade](saml-identity-provider-technical-profile.md#metadata) contiverem o `SingleLogoutService` local.
1. Opcionalmente, desconecta-se de outros aplicativos. Para obter mais informações, consulte a seção de [saída única](#single-sign-out) .

> [!NOTE]
> Usando [políticas personalizadas](custom-policy-overview.md), você pode desabilitar a saída de provedores de identidade federada, definindo os metadados do perfil técnico do provedor de identidade `SingleLogoutEnabled` como `false` .

A saída limpa o estado de logon único do usuário com o Azure AD B2C, mas ele pode não desconectar o usuário da sessão do provedor de identidade social. Se o usuário selecionar o mesmo provedor de identidade durante uma entrada subsequente, ele poderá se autenticar sem inserir suas credenciais. Se um usuário quiser sair do aplicativo, isso não significa necessariamente que deseja sair de sua conta do Facebook. No entanto, se forem usadas contas locais, a sessão do usuário será encerrada corretamente.

### <a name="single-sign-out"></a>Logout único 


> [!NOTE]
> Esse recurso é limitado a [políticas personalizadas](custom-policy-overview.md).

Quando você redireciona o usuário para o ponto de extremidade de saída do Azure AD B2C (para os protocolos OAuth2 e SAML), Azure AD B2C limpa a sessão do usuário do navegador. No entanto, o usuário ainda pode estar conectado a outros aplicativos que usam Azure AD B2C para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, Azure AD B2C envia uma solicitação HTTP GET para o registrado `LogoutUrl` de todos os aplicativos aos quais o usuário está conectado no momento.


Os aplicativos devem responder a essa solicitação, limpando as sessões que identificam o usuário e retornando uma resposta `200`. Se você quiser dar suporte ao logout único em seu aplicativo, deverá implementar um `LogoutUrl` no código do aplicativo. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Configurar o comportamento da sessão no fluxo do usuário](session-behavior.md).
- Saiba como [configurar o comportamento da sessão em políticas personalizadas](session-behavior-custom-policy.md).
