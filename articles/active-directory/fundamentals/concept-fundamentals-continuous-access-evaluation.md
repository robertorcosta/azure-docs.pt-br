---
title: Avaliação de acesso contínuo no Azure AD
description: Respondendo a alterações no estado do usuário mais rapidamente com avaliação de acesso contínuo no Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3713901dd3dd5d17c4e1ddcef529c663b68f5b43
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112568"
---
# <a name="continuous-access-evaluation"></a>Avaliação de acesso contínuo

Os serviços da Microsoft, como o Azure Active Directory (Azure AD) e o Office 365, usam padrões abertos e protocolos para maximizar a interoperabilidade. Um dos mais críticos é o Open ID Connect (OIDC). Quando um aplicativo cliente como o Outlook se conecta a um serviço como o Exchange Online, as solicitações de API são autorizadas usando tokens de acesso OAuth 2,0. Por padrão, esses tokens de acesso são válidos por uma hora. Quando eles expirarem, o cliente será Redirecionado de volta para o Azure AD para atualizá-los. Isso também fornece uma oportunidade para reavaliar as políticas de acesso do usuário – podemos optar por não atualizar o token devido a uma política de acesso condicional ou porque o usuário foi desabilitado no diretório. 

A expiração do token e a atualização são um mecanismo padrão do setor. Dito isso, os clientes expressou preocupações sobre o atraso entre o momento em que as condições de risco mudam para o usuário (por exemplo: mudando do escritório corporativo para o café local ou as credenciais de usuário descobertas no mercado preto) e quando as políticas podem ser impostas relacionadas a essa alteração. Experimentamos a abordagem "objeto de moderação" de tempos de vida reduzidos de token, mas descobrimos que eles podem prejudicar as experiências e a confiabilidade do usuário sem eliminar os riscos.

A resposta oportuna a violações de política ou problemas de segurança realmente requer uma "conversa" entre o emissor do token, como o Azure AD e a terceira parte confiável, como o Exchange Online. Essa conversa bidirecional nos dá dois recursos importantes. A terceira parte confiável pode observar quando as coisas foram alteradas, como um cliente proveniente de um novo local e informam ao emissor do token. Ele também fornece ao emissor do token uma maneira de dizer à terceira parte confiável para parar de respeitar os tokens de um determinado usuário devido a comprometimento da conta, à desabilitação ou a outras preocupações. O mecanismo para essa conversa é a Continuous Access Evaluation (CAE).

A Microsoft foi um participante inicial da iniciativa CAEP (Continuous Access Evaluation Protocol) como parte do grupo de trabalho de [sinais e eventos compartilhados](https://openid.net/wg/sse/) no OpenID Foundation. Provedores de identidade e terceiras partes confiáveis poderão aproveitar os eventos de segurança e os sinais definidos pelo grupo de trabalho para autorizar ou encerrar o acesso. Ele é um trabalho empolgante e melhorará a segurança em várias plataformas e aplicativos.

Como os benefícios de segurança são tão ótimos, estamos distribuindo uma implementação inicial específica da Microsoft em paralelo ao nosso trabalho contínuo dentro dos órgãos de padrões. À medida que trabalhamos para implantar esses recursos de CAE (avaliação de acesso contínuo) nos serviços da Microsoft, aprendemos muito e estamos compartilhando essas informações com a comunidade de padrões. Esperamos que nossa experiência na implantação possa ajudar a informar um padrão de mercado ainda melhor e esteja comprometida em implementar esse padrão depois de ratificar, permitindo que todos os serviços participantes se beneficiem.

## <a name="how-does-cae-work-in-microsoft-services"></a>Como o CAE funciona nos serviços da Microsoft?

Estamos concentrando nossa implementação inicial de avaliação de acesso contínuo para o Exchange e as equipes. Esperamos expandir o suporte a outros serviços da Microsoft no futuro. Começaremos a habilitar a avaliação de acesso contínuo somente para locatários sem políticas de acesso condicional. Usaremos nossas aprendeções desta fase do CAE para informar nossa distribuição contínua do CAE.

## <a name="service-side-requirements"></a>Requisitos do lado do serviço

A avaliação de acesso contínuo é implementada habilitando serviços (provedores de recursos) a assinar eventos críticos no Azure AD para que esses eventos possam ser avaliados e aplicados quase em tempo real. Os seguintes eventos serão aplicados nesta distribuição inicial CAE:

- A conta de usuário foi excluída ou desabilitada
- A senha de um usuário é alterada ou redefinida
- O administrador revoga explicitamente todos os tokens de atualização para um usuário
- Risco de usuário elevado detectado por Azure AD Identity Protection

No futuro, esperamos adicionar mais eventos, incluindo eventos como localização e alterações de estado do dispositivo. **Embora nossa meta seja a imposição para ser instantânea, em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento**. 

## <a name="client-side-claim-challenge"></a>Desafio de declaração do lado do cliente

Antes da avaliação de acesso contínuo, os clientes sempre tentarão repetir o token de acesso de seu cache, desde que ele não tenha expirado. Com o CAE, estamos introduzindo um novo caso que um provedor de recursos possa rejeitar um token, mesmo quando ele não tiver expirado. Para informar os clientes para ignorarem seu cache, mesmo que os tokens em cache não tenham expirado, apresentamos um mecanismo chamado **desafio de declaração**. O CAE requer uma atualização do cliente para entender o desafio da declaração. A versão mais recente dos seguintes aplicativos abaixo dá suporte ao desafio de declaração:

- Outlook para Windows 
- IOS do Outlook 
- Android do Outlook 
- Mac do Outlook 
- Teams para Windows
- Equipes iOS 
- Equipes Android 
- Mac de equipes 

## <a name="token-lifetime"></a>Tempo de vida do token

Como o risco e a política são avaliados em tempo real, os clientes que negociam sessões de reconhecimento de avaliação de acesso contínuo dependerão de CAE em vez de políticas de tempo de vida de token de acesso estático existentes, o que significa que a política de tempo de vida de token configurável não será mais respeitada para clientes compatíveis com CAE que negociem sessões com reconhecimento de

Aumentaremos o tempo de vida do token de acesso para 24 horas em sessões CAE. A revogação é orientada por eventos críticos e avaliação de política, não um período de tempo arbitrário. Essa alteração aumenta a estabilidade de seus aplicativos sem afetar sua postura de segurança. 

## <a name="example-flows"></a>Fluxos de exemplo

### <a name="user-revocation-event-flow"></a>Fluxo de eventos de revogação do usuário:

![Fluxo de eventos de revogação do usuário](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Um cliente com capacidade de CAE apresenta credenciais ou um token de atualização para o AAD solicitando um token de acesso para algum recurso.
1. Um token de acesso é retornado junto com outros artefatos para o cliente.
1. Um administrador [revoga explicitamente todos os tokens de atualização para o usuário](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Um evento de revogação será enviado para o provedor de recursos do Azure AD.
1. Um token de acesso é apresentado ao provedor de recursos. O provedor de recursos avalia a validade do token e verifica se há qualquer evento de revogação para o usuário. O provedor de recursos usa essas informações para decidir conceder acesso ao recurso ou não.
1. Nesse caso, o provedor de recursos nega o acesso e envia um desafio de declaração 401 + de volta para o cliente
1. O cliente com capacidade de CAE compreende o desafio de declaração 401 +. Ele ignora os caches e volta para a etapa 1, enviando seu token de atualização junto com o desafio de declaração de volta para o Azure AD. O Azure AD reavaliará todas as condições e solicitará que o usuário se autentique novamente nesse caso.
 
## <a name="faqs"></a>Perguntas frequentes

### <a name="what-is-the-lifetime-of-my-access-token"></a>Qual é o tempo de vida do meu token de acesso?

Se você não estiver usando clientes compatíveis com CAE, o tempo de vida do token de acesso padrão ainda será de 1 hora, a menos que você tenha configurado o tempo de vida do token de acesso com o recurso de visualização de [tempo de vida do token configurável (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

Se você estiver usando clientes compatíveis com CAE que negociam sessões com reconhecimento de CAE, suas configurações de CTL para o tempo de vida do token de acesso serão substituídas e o tempo de vida do token de acesso será de 24 horas.

### <a name="how-quick-is-enforcement"></a>Quão rápido é a imposição?

Embora nossa meta seja a imposição para ser instantânea, em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Como o CAE funcionará com a frequência de entrada?

A frequência de entrada será respeitada com ou sem CAE.

## <a name="next-steps"></a>Próximas etapas

[Anunciando avaliação de acesso contínuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
