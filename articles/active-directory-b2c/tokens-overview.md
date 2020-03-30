---
title: Visão geral dos tokens - Azure Active Directory B2C
description: Conheça os tokens usados no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186481"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Visão geral dos tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada [fluxo de autenticação](application-types.md). Este documento descreve o formato, as características de segurança e o conteúdo de cada tipo de token.

## <a name="token-types"></a>Tipos de token

O Azure AD B2C suporta os [protocolos OAuth 2.0 e OpenID Connect,](protocols-overview.md)que faz uso de tokens para autenticação e acesso seguro aos recursos. Todos os tokens usados no Azure AD B2C são [tokens web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) que contêm afirmações de informações sobre o portador e o assunto do token.

Os seguintes tokens são usados na comunicação com o Azure AD B2C:

- *Token ID* - Um JWT que contém alegações que você pode usar para identificar usuários em seu aplicativo. Este token é enviado com segurança em solicitações HTTP para comunicação entre dois componentes do mesmo aplicativo ou serviço. Você pode usar as declarações em um token de ID como desejar. Eles são comumente usados para exibir informações da conta ou para tomar decisões de controle de acesso em um aplicativo. Os tokens de ID são assinados, mas não são criptografados. Quando seu aplicativo ou API recebe um token de ID, ele deve validar a assinatura para provar que o token é autêntico. Seu aplicativo ou API também deve validar algumas reivindicações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as reclamações validadas por um aplicativo podem variar, mas sua aplicação deve realizar algumas validações comuns de sinistros em todos os cenários.
- *Token de* acesso - Um JWT que contém reivindicações que você pode usar para identificar as permissões concedidas às suas APIs. Os tokens de acesso são assinados, mas não são criptografados. Os tokens de acesso são usados para fornecer acesso a APIs e servidores de recursos.  Quando sua API recebe um token de acesso, deve validar a assinatura para provar que o token é autêntico. Sua API também deve validar algumas declarações no token para provar que ele é válido. Dependendo dos requisitos do cenário, as reclamações validadas por um aplicativo podem variar, mas sua aplicação deve realizar algumas validações comuns de sinistros em todos os cenários.
- *Token de atualização* - Tokens de atualização são usados para adquirir novos tokens de ID e tokens de acesso em um fluxo OAuth 2.0. Eles fornecem ao seu aplicativo acesso a longo prazo aos recursos em nome dos usuários sem exigir interação com esses usuários. Os tokens de atualização são opacos para o seu aplicativo. Eles são emitidos pelo Azure AD B2C e podem ser inspecionados e interpretados apenas pelo Azure AD B2C. Eles são de longa duração, mas sua aplicação não deve ser escrita com a expectativa de que um token de atualização durará por um período específico de tempo. Os tokens de atualização podem ser invalidados a qualquer momento por vários motivos. A única maneira de seu aplicativo saber se um token de atualização é válido é tentar resgatá-lo fazendo uma solicitação de token ao Azure AD B2C. Quando você resgata um token de atualização para um novo token, você recebe um novo token de atualização na resposta do token. Salve o novo token de atualização. Ele substitui o token de atualização que você usou anteriormente na solicitação. Esta ação ajuda a garantir que seus tokens de atualização permaneçam válidos pelo maior tempo possível.

## <a name="endpoints"></a>Pontos de extremidade

Um [aplicativo registrado](tutorial-register-applications.md) recebe tokens e se comunica com o Azure AD B2C enviando solicitações para esses pontos finais:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Os tokens de segurança que seu aplicativo recebe do Azure AD B2C podem vir dos `/authorize` pontos finais ou `/token` do Azure. Quando os tokens de ID são adquiridos do `/authorize` ponto final, ele é feito usando o fluxo [implícito](implicit-flow-single-page-application.md), que é frequentemente usado para usuários que fazem login em aplicativos web baseados em JavaScript. Quando os tokens de ID são adquiridos do `/token` ponto final, ele é feito usando o fluxo de código de [autorização](openid-connect.md#get-a-token), que mantém o token escondido do navegador.

## <a name="claims"></a>Declarações

Ao usar o Azure AD B2C, você tem um controle refinado sobre o conteúdo de seus tokens. Você pode configurar [fluxos de usuário](user-flow-overview.md) e [políticas personalizadas](custom-policy-overview.md) para enviar certos conjuntos de dados do usuário em reivindicações que são necessárias para o seu aplicativo. Essas reivindicações podem incluir propriedades padrão, como **displayName** e **emailAddress**. Seus aplicativos podem usar essas declarações para autenticar usuários e solicitações com segurança.

As reivindicações em tokens de ID não são retornadas em nenhuma ordem particular. Novas reivindicações podem ser introduzidas em tokens de ID a qualquer momento. Sua aplicação não deve quebrar à medida que novas reivindicações são introduzidas. Você também pode incluir [atributos personalizados do usuário](user-flow-custom-attributes.md) em suas reivindicações.

A tabela a seguir lista as reivindicações que você pode esperar em tokens de ID e tokens de acesso emitidos pelo Azure AD B2C.

| Nome | Declaração | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Público | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica o destinatário pretendido do token. Para o Azure AD B2C, o público é o ID do aplicativo. Seu aplicativo deve validar esse valor e rejeitar o token se ele não corresponder. Público-alvo é sinônimo de recurso. |
| Emissor | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica o STS (serviço de token de segurança) que constrói e retorna o token. Ele também identifica o diretório no qual o usuário foi autenticado. Seu aplicativo deve validar a alegação do emissor para garantir que o token veio do ponto final apropriado. |
| Emitido em | `iat` | `1438535543` | A hora em que o token foi emitido, representada na época. |
| Hora de expiração | `exp` | `1438539443` | A hora em que o token se torna inválido, representada na época. Seu aplicativo deve usar esta reivindicação para verificar a validade da vida útil do token. |
| Não antes de | `nbf` | `1438535543` | O horário em que o token se torna inválido, representado no horário da época. Desta vez é geralmente o mesmo que a hora em que o token foi emitido. Seu aplicativo deve usar esta reivindicação para verificar a validade da vida útil do token. |
| Versão | `ver` | `1.0` | A versão do token ID, conforme definido pelo Azure AD B2C. |
| Hash de código | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de código incluído em um token de ID somente quando o token é emitido juntamente com um código de autorização OAuth 2.0. Um hash de código pode ser usado para validar a autenticidade de um código de autorização. Para obter mais informações sobre como realizar essa validação, consulte a [especificação OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Hash de token de acesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Um hash de token de acesso incluído em um token de ID somente quando o token é emitido juntamente com um token de acesso OAuth 2.0. Um hash de token de acesso pode ser usado para validar a autenticidade de um token de acesso. Para obter mais informações sobre como realizar essa validação, consulte a [especificação do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Um nonce é uma estratégia usada para reduzir ataques de reprodução de token. Seu aplicativo pode especificar um nonce `nonce` em uma solicitação de autorização usando o parâmetro de consulta. O valor fornecido na solicitação é emitido `nonce` sem modificações na reivindicação de um token de ID apenas. Esta reclamação permite que seu aplicativo verifique o valor em relação ao valor especificado na solicitação. Seu aplicativo deve realizar essa validação durante o processo de validação do token ID. |
| Assunto | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | O principal sobre o qual o token afirma informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório. |
| Referência de classe de contexto de autenticação | `acr` | Não aplicável | Usado apenas com políticas mais antigas. |
| Política de estrutura confiável | `tfp` | `b2c_1_signupsignin1` | O nome da apólice que foi usado para adquirir o token id. |
| Hora da autenticação | `auth_time` | `1438535543` | O tempo em que um usuário inseriu credenciais pela última vez, representado em tempo de época. Não há discriminação entre essa autenticação ser um novo login, uma sessão de login único (SSO) ou outro tipo de login. É `auth_time` a última vez que o aplicativo (ou usuário) iniciou uma tentativa de autenticação contra o Azure AD B2C. O método utilizado para autenticar não é diferenciado. |
| Escopo | `scp` | `Read`| As permissões concedidas ao recurso para um token de acesso. Várias permissões concedidas são separadas por um espaço. |
| Entidade Autorizada | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | A **ID do aplicativo** do aplicativo cliente que iniciou a solicitação. |

## <a name="configuration"></a>Configuração

As seguintes propriedades são usadas para [gerenciar tokens de segurança emitidos](configure-tokens.md) pelo Azure AD B2C:

- **Duração dos tokens de acesso e ID (minutos)**: o tempo de vida do token portador do OAuth 2.0 usado para obter acesso a um recurso protegido. O padrão é de 60 minutos. O mínimo (inclusive) é de 5 minutos. O máximo (inclusive) é de 1440 minutos.

- **Atualizar token lifetime (dias)** - O período máximo de tempo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso ou ID. O período de tempo também abrange a aquisição de um `offline_access` novo token de atualização se sua aplicação tiver sido concedida o escopo. O padrão é 14 dias. O mínimo (inclusive) é de um dia. O máximo (inclusive) é de 90 dias.

- **Atualizar token deslizando vida útil da janela (dias)** - Após esse período de tempo, o usuário é forçado a reautenticar, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Ele só poderá ser fornecido se a opção estiver definida como **Bounded**. Ele precisa ser maior ou igual ao valor **Vida útil do token de atualização (dias)** . Se a opção estiver definida como **Unbounded**, você não poderá fornecer um valor específico. O padrão é de 90 dias. O mínimo (inclusive) é de um dia. O máximo (inclusive) é de 365 dias.

Os casos de uso a seguir são ativados usando estas propriedades:

- Permitir que um usuário permaneça conectado a um aplicativo móvel indefinidamente, contanto que o usuário esteja continuamente ativo no aplicativo. Você pode definir **Atualizar duração da janela deslizante do token (dias)** para **Não consolidado** em seu fluxo de usuário.
- Atenda aos requisitos de conformidade e segurança de seu setor definindo a vida de tokens de acesso adequadamente.

Essas configurações não estão disponíveis para os fluxos de usuário de redefinição de senha.

## <a name="compatibility"></a>Compatibilidade

As seguintes propriedades são usadas para [gerenciar a compatibilidade de tokens:](configure-tokens.md)

- **Emissor (iss) reivindicação** - essa propriedade identifica o locatário do Azure AD B2C que emitiu o token. O valor padrão é `https://<domain>/{B2C tenant GUID}/v2.0/`. O valor `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inclui IDs tanto para o inquilino Azure AD B2C quanto para o fluxo de usuário que foi usado na solicitação de token. Se o seu aplicativo ou biblioteca precisar que o Azure AD B2C esteja em conformidade com a [especificação OpenID Connect Discovery 1.0,](https://openid.net/specs/openid-connect-discovery-1_0.html)use este valor.

- **Subject (sub) declaração** - Esta propriedade identifica a entidade para a qual o token afirma informações. O valor padrão é **ObjectID** `sub` , que preenche a reclamação no token com o ID do objeto do usuário. O valor de **Não suportado** é fornecido apenas para retrocompatibilidade. Recomenda-se que você mude para **ObjectID** assim que puder.

- **Reivindicação representando iD de política** - Esta propriedade identifica o tipo de reclamação no qual o nome da política usado na solicitação de token é preenchido. O valor padrão é `tfp`. O valor `acr` do é fornecido apenas para retrocompatibilidade.

## <a name="pass-through"></a>Passagem

Quando uma jornada de usuário é iniciada, o Azure AD B2C recebe um token de acesso de um provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Você [habilita uma reclamação no seu fluxo de usuário](idp-pass-through-user-flow.md) ou define uma reclamação em sua política [personalizada](idp-pass-through-custom.md) para passar o token para os aplicativos que você registra no Azure AD B2C. Seu aplicativo deve estar usando um [fluxo de usuário v2](user-flow-versions.md) para tirar proveito de passar o token como uma reivindicação.

O Azure Active Directory B2C atualmente suporta apenas passar o token de acesso dos provedores de identidade do OAuth 2.0, que incluem Facebook e Google. Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="validation"></a>Validação

Para validar um token, seu aplicativo deve verificar a assinatura e as reivindicações do token. Muitas bibliotecas de código aberto estão disponíveis para validar JWTs, dependendo do seu idioma preferido. Recomenda-se que você explore essas opções em vez de implementar sua própria lógica de validação.

### <a name="validate-signature"></a>Validar a assinatura

Um JWT contém três segmentos, um *cabeçalho,* um *corpo*e uma *assinatura.* O segmento de assinatura pode ser usado para validar a autenticidade do token para que ele possa ser confiável pelo seu aplicativo. Os tokens do Azure AD B2C são assinados usando algoritmos de criptografia assimétrica padrão do setor, como o RSA 256.

O cabeçalho do token contém informações sobre o método de criptografia e a chave usados para assinar o token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

O valor da reivindicação **alg** é o algoritmo que foi usado para assinar o token. O valor da reivindicação do **garoto** é a chave pública que foi usada para assinar o token. A qualquer momento, o Azure AD B2C pode assinar um token usando qualquer um de um conjunto de pares de chaves público-privadas. O Azure AD B2C gira o possível conjunto de chaves periodicamente. Seu aplicativo deve ser escrito para lidar com essas alterações de chave automaticamente. Uma frequência razoável para verificar as atualizações das chaves públicas usadas pelo Azure AD B2C é a cada 24 horas.

O Azure AD B2C tem um ponto de extremidade de metadados OpenID Connect. Usando este ponto final, os aplicativos podem solicitar informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. O inquilino Azure AD B2C contém um documento de metadados JSON para cada política. O documento de metadados é um objeto JSON que contém várias informações úteis. Os metadados contêm **jwks_uri**, o que dá a localização do conjunto de chaves públicas que são usadas para assinar tokens. Esse local é fornecido aqui, mas é melhor buscar a localização dinamicamente usando o documento de metadados e analisando **jwks_uri:**

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
O documento JSON localizado nessa URL contém todas as informações de chave pública em uso em um momento específico. O aplicativo pode usar a declaração `kid` no cabeçalho do JWT para selecionar a chave pública no documento JSON que é usada para assinar um token específico. Assim, ele pode executar a validação da assinatura usando a chave pública correta e o algoritmo indicado.

O documento de `B2C_1_signupsignin1` metadados `contoso.onmicrosoft.com` da política no inquilino está localizado em:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Para determinar qual política foi usada para assinar um token (e para onde solicitar os metadados), você tem duas opções. Primeiro, o nome da política é incluído na declaração `acr` no token. Você pode analisar as declarações fora do corpo do JWT decodificando em base 64 o corpo e desserializando a cadeia de caracteres JSON resultante. A `acr` reivindicação é o nome da política que foi usada para emitir o token. A outra opção é codificar a diretiva `state` no valor do parâmetro quando você emitir a solicitação e, em seguida, decodificá-la para determinar qual política foi usada. Ambos os métodos são válidos.

Uma descrição de como executar a validação de assinatura está fora do escopo deste documento. Muitas bibliotecas de código aberto estão disponíveis para ajudá-lo a validar um token.

### <a name="validate-claims"></a>Validar sinistros

Quando seus aplicativos ou API recebem um token de ID, ele também deve realizar várias verificações contra as reclamações no token de Identificação. As seguintes reivindicações devem ser verificadas:

- **audiência** - Verifica se o token id foi destinado a ser dado à sua aplicação.
- **não antes** e **tempo de expiração** - Verifica se o token de identificação não expirou.
- **emissor** - Verifica se o token foi emitido para o seu aplicativo pelo Azure AD B2C.
- **nonce** - Uma estratégia para mitigação de ataque de replay de token.

Para obter uma lista completa de validações que seu aplicativo deve realizar, consulte a [especificação OpenID Connect](https://openid.net).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar tokens de acesso](access-tokens.md).

