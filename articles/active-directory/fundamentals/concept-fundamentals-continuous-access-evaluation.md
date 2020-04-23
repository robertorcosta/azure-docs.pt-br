---
title: Avaliação de acesso contínuo no Azure AD
description: Respondendo a mudanças no estado do usuário mais rapidamente com avaliação de acesso contínuo no Azure AD
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
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873297"
---
# <a name="continuous-access-evaluation"></a>Avaliação de acesso contínuo

Os serviços da Microsoft, como o Azure Active Directory (Azure AD) e o Office 365, usam padrões e protocolos abertos para maximizar a interoperabilidade. Um dos mais críticos é o Open ID Connect (OIDC). Quando um aplicativo cliente como o Outlook se conecta a um serviço como o Exchange Online, as solicitações de API são autorizadas usando tokens de acesso OAuth 2.0. Por padrão, esses tokens de acesso são válidos por uma hora. Quando expirar, o cliente é redirecionado de volta ao Azure AD para atualizá-los. Isso também oferece uma oportunidade de reavaliar políticas de acesso ao usuário – podemos optar por não atualizar o token por causa de uma política de Acesso Condicional ou porque o usuário foi desativado no diretório. 

Ouvimos o feedback avassalador de nossos clientes: um atraso de uma hora devido ao acesso ao token vitalício para reaplicar políticas de Acesso Condicional e mudanças no estado do usuário (por exemplo: desativado devido à licença) não é bom o suficiente.

A Microsoft foi uma das primeiras participantes da iniciativa Do Protocolo de Avaliação de Acesso Contínuo (CAEP) como parte do grupo de trabalho [de Sinais Compartilhados e Eventos](https://openid.net/wg/sse/) da Fundação OpenID. Os provedores de identidade e as partes que dependem poderão aproveitar os eventos e sinais de segurança definidos pelo grupo de trabalho para reautorizar ou encerrar o acesso. É um trabalho emocionante e melhorará a segurança em muitas plataformas e aplicativos.

Como os benefícios de segurança são tão grandes, estamos implementando uma implementação inicial específica da Microsoft em paralelo ao nosso trabalho contínuo dentro dos órgãos de padrões. À medida que trabalhamos para implantar esses recursos de avaliação de acesso contínuo (CAE) em serviços da Microsoft, aprendemos muito e estamos compartilhando essas informações com a comunidade de padrões. Esperamos que nossa experiência em implantação possa ajudar a informar um padrão ainda melhor do setor e estamos comprometidos em implementar esse padrão uma vez ratificado, permitindo que todos os serviços participantes se beneficiem.

## <a name="how-does-cae-work-in-microsoft-services"></a>Como funciona o CAE nos serviços da Microsoft?

Estamos focando nossa implementação inicial de avaliação de acesso contínuo ao Exchange e Às Equipes. Esperamos expandir o suporte a outros serviços da Microsoft no futuro. Começaremos a permitir a avaliação de acesso contínuo apenas para inquilinos sem políticas de acesso condicional. Usaremos nossos aprendizados desta fase da CAE para informar nossa implantação contínua do CAE.

## <a name="service-side-requirements"></a>Requisitos do lado do serviço

A avaliação de acesso contínuo é implementada permitindo que os serviços (provedores de recursos) se inscrevam em eventos críticos no Azure AD para que esses eventos possam ser avaliados e aplicados quase em tempo real. Os seguintes eventos serão aplicados neste lançamento inicial do CAE:

- Conta de usuário é excluída ou desativada
- A senha para um usuário é alterada ou redefinida
- O admin revoga explicitamente todos os tokens de atualização para um usuário
- Risco elevado do usuário detectado pelo Azure AD Identity Protection

No futuro, esperamos adicionar mais eventos, incluindo eventos como mudanças de localização e estado do dispositivo. **Embora nosso objetivo seja que a aplicação seja instantânea, em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento**. 

## <a name="client-side-claim-challenge"></a>Desafio de reivindicação do lado do cliente

Antes da avaliação de acesso contínuo, os clientes sempre tentavam reproduzir o token de acesso de seu cache, desde que não estivesse expirado. Com o CAE, estamos introduzindo um novo caso de que um provedor de recursos pode rejeitar um token mesmo quando ele não está expirado. A fim de informar os clientes para contornar seu cache mesmo que os tokens armazenados em cache não tenham expirado, introduzimos um mecanismo chamado **desafio de sinistro**. A CAE requer uma atualização do cliente para entender o desafio da reclamação. A versão mais recente dos seguintes aplicativos abaixo suporta o desafio de reivindicação:

- Perspectivas para Windows 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Equipes para Windows
- Equipes iOS 
- Equipes Android 
- Equipes Mac 

## <a name="token-lifetime"></a>Tempo de vida do token

Como o risco e a política são avaliados em tempo real, os clientes que negociam sessões de avaliação de acesso contínuo dependerão da CAE em vez das políticas de vida de token de acesso estático existentes, o que significa que a política de vida útil do token configurável não será mais honrada para clientes capazes de CAE que negociam sessões com reconhecimento cae.

Aumentaremos a vida útil do token de acesso para 24 horas nas sessões do CAE. A revogação é impulsionada por eventos críticos e avaliação de políticas, não por um período de tempo arbitrário. Essa mudança aumenta a estabilidade de seus aplicativos sem afetar sua postura de segurança. 

## <a name="example-flows"></a>Fluxos de exemplo

### <a name="user-revocation-event-flow"></a>Fluxo de evento de revogação do usuário:

![Fluxo de evento de revogação do usuário](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. Um cliente capaz de CAE apresenta credenciais ou um token de atualização para a AAD pedindo um token de acesso para algum recurso.
1. Um token de acesso é devolvido junto com outros artefatos para o cliente.
1. Um administrador revoga explicitamente [todos os tokens de atualização para o usuário](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Um evento de revogação será enviado ao provedor de recursos do Azure AD.
1. Um token de acesso é apresentado ao provedor de recursos. O provedor de recursos avalia a validade do token e verifica se há algum evento de revogação para o usuário. O provedor de recursos usa essas informações para decidir conceder acesso ao recurso ou não.
1. Neste caso, o provedor de recursos nega o acesso e envia um desafio de reclamação de 401+ para o cliente
1. O cliente capaz de CAE entende o desafio de reivindicação 401+. Ele ignora os caches e volta para a etapa 1, enviando seu token de atualização junto com o desafio de reivindicação de volta ao Azure AD. O Azure AD, então, reavaliará todas as condições e solicitará ao usuário que reautenticar neste caso.
 
## <a name="faqs"></a>Perguntas frequentes

### <a name="what-is-the-lifetime-of-my-access-token"></a>Qual é a vida do meu Access Token?

Se você não estiver usando clientes capazes de CAE, sua vida útil padrão do Access Token ainda será de 1 hora, a menos que você tenha configurado sua vida útil do Access Token com o recurso de visualização [CTL (Configurável Token Lifetime).](../develop/active-directory-configurable-token-lifetimes.md)

Se você estiver usando clientes capazes de CAE que negociam sessões com reconhecimento cae, suas configurações ctl para a vida útil do Access Token serão substituídas e a vida útil do Access Token será de 24 horas.

### <a name="how-quick-is-enforcement"></a>Quão rápido é a aplicação?

Embora nosso objetivo seja que a aplicação seja instantânea, em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Como o CAE funcionará com a frequência de login?

A Freqüência de login será honrada com ou sem CAE.

## <a name="next-steps"></a>Próximas etapas

[Anunciando avaliação de acesso contínuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
