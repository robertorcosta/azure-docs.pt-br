---
title: Trocas de declarações da API REST na política personalizada do B2C
titleSuffix: Azure AD B2C
description: Uma introdução à criação de uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337409"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrar as trocas de declarações da API REST em sua política personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A estrutura de experiência de identidade, que se baseia Azure Active Directory B2C (Azure AD B2C), pode se integrar a APIs RESTful dentro de um percurso do usuário. Este artigo mostra como criar uma jornada do usuário que interage com um serviço RESTful usando um [perfil técnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Usando Azure AD B2C, você pode adicionar sua própria lógica de negócios a uma jornada do usuário chamando seu próprio serviço RESTful. A estrutura de experiência de identidade pode enviar e receber dados de seu serviço RESTful para trocar declarações. Por exemplo, você pode:

- **Validar dados de entrada do usuário**. Por exemplo, você pode verificar se o endereço de email fornecido pelo usuário existe no banco de dados do cliente e, se não estiver, apresentar um erro.
- **Processar declarações**. Se um usuário inserir seu nome em letras minúsculas ou maiúsculas, sua API REST poderá formatar o nome apenas com a primeira letra em maiúscula e retorná-lo para Azure AD B2C.
- **Enriquecer os dados do usuário integrando-os com aplicativos de linha de negócios corporativos**. Seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário ao Azure AD B2C. Em seguida, as declarações de retorno podem ser armazenadas na conta do Azure AD do usuário, avaliadas nas próximas etapas de orquestração ou incluídas no token de acesso.
- **Execute a lógica de negócios personalizada**. Você pode enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões, auditar bancos de dados e executar qualquer outro fluxo de trabalho.

![Diagrama de uma troca de declarações do serviço RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Chamando um serviço RESTful

A interação inclui uma troca de declarações de informações entre as declarações da API REST e a Azure AD B2C. É possível criar a integração com os serviços RESTful das seguintes maneiras:

- **Perfil técnico de validação**. A chamada para o serviço RESTful ocorre dentro de um [perfil técnico de validação](validation-technical-profile.md) do [perfil técnico autodeclarado](self-asserted-technical-profile.md)especificado ou um controle de [exibição de verificação](display-control-verification.md) de um [controle de exibição](display-controls.md). O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário possa avançar. Com o perfil técnico de validação, é possível:

  - Envie declarações para sua API REST.
  - Valide as declarações e gere mensagens de erro personalizadas que são exibidas para o usuário.
  - Envie declarações de retorno da API REST para as etapas de orquestração subsequentes.

- **Troca de declarações**. Uma troca de declarações direta pode ser configurada chamando um perfil técnico da API REST diretamente de uma etapa de orquestração de um percurso do [usuário](userjourneys.md). Essa definição é limitada a:

  - Envie declarações para sua API REST.
  - Valide as declarações e gere mensagens de erro personalizadas que são retornadas para o aplicativo.
  - Envie declarações de retorno da API REST para as etapas de orquestração subsequentes.

Você pode adicionar uma chamada à API REST em qualquer etapa no percurso do usuário definido por uma política personalizada. Por exemplo, você pode chamar uma API REST:

- Durante a entrada, logo antes de Azure AD B2C valida as credenciais.
- Imediatamente após a entrada.
- Antes de Azure AD B2C cria uma nova conta no diretório.
- Depois que Azure AD B2C cria uma nova conta no diretório.
- Antes de Azure AD B2C emite um token de acesso.

![Coleção de perfis técnicos de validação](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Enviar dados

No [perfil técnico RESTful](restful-technical-profile.md), o `InputClaims` elemento contém uma lista de declarações a serem enviadas ao serviço RESTful. Você pode mapear o nome da sua declaração para o nome definido no serviço RESTful, definir um valor padrão e usar [resolvedores de declarações](claim-resolver-overview.md).

Você pode configurar como as declarações de entrada são enviadas para o provedor de declarações RESTful usando o atributo SendClaimsIn. Os valores possíveis são:

- **Corpo**, enviado no corpo da solicitação HTTP post no formato JSON.
- **Formulário**, enviado no corpo da solicitação HTTP post em um formato de valor de chave separado "&" e comercial.
- **Cabeçalho**, enviado no cabeçalho de solicitação HTTP Get.
- **QueryString**, enviado na cadeia de caracteres de consulta de solicitação HTTP Get.

Quando a opção de **corpo** é configurada, o perfil técnico da API REST permite que você envie uma carga JSON complexa para um ponto de extremidade. Para obter mais informações, consulte [enviar um conteúdo JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Recebendo dados

O `OutputClaims` elemento do [perfil técnico RESTful](restful-technical-profile.md) contém uma lista de declarações retornadas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Você também pode incluir declarações que não são retornadas pelo provedor de identidade da API REST, desde que você defina o atributo DefaultValue.

As declarações de saída analisadas pelo provedor de declarações RESTful sempre esperam analisar uma resposta de corpo JSON simples, como:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

As declarações de saída devem ser semelhantes às seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Para analisar uma resposta de corpo JSON aninhada, defina os metadados de ResolveJsonPathsInJsonTokens como true. Na declaração de saída, defina PartnerClaimType como o elemento de caminho JSON que você deseja gerar.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


As declarações de saída devem ser semelhantes às seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerações de segurança

Você deve proteger seu ponto de extremidade de API REST para que somente clientes autenticados possam se comunicar com ele. A API REST deve usar um ponto de extremidade HTTPS. Defina os metadados de AuthenticationType para um dos seguintes métodos de autenticação:

- O **certificado do cliente** restringe o acesso usando a autenticação de certificado do cliente. Somente os serviços que têm os certificados apropriados podem acessar sua API. Você armazena o certificado de cliente em uma chave de política de Azure AD B2C. Saiba mais sobre como [proteger seu serviço RESTful usando certificados de cliente](secure-rest-api.md#https-client-certificate-authentication).
- **Básico** protege a API REST com autenticação básica http. Somente usuários verificados, incluindo Azure AD B2C, podem acessar a API. O nome de usuário e a senha são armazenados em chaves de política de Azure AD B2C. Saiba como [proteger seus serviços RESTful usando a autenticação básica http](secure-rest-api.md#http-basic-authentication).
- O **portador** restringe o acesso usando um token de acesso OAuth2 cliente. O token de acesso é armazenado em uma chave de política de Azure AD B2C. Saiba mais sobre como [proteger seu serviço RESTful usando o token de portador](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Plataforma da API REST
Sua API REST pode ser baseada em qualquer plataforma e escrita em qualquer linguagem de programação, desde que seja segura e possa enviar e receber declarações conforme especificado no [perfil técnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localize a API REST
Em um perfil técnico RESTful, talvez você queira enviar o idioma/a localidade da sessão atual e, se necessário, gerar uma mensagem de erro localizada. Usando o [resolvedor de declarações](claim-resolver-overview.md), você pode enviar uma declaração contextual, como o idioma do usuário. O exemplo a seguir mostra um perfil técnico RESTful que demonstra esse cenário.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Manipulando mensagens de erro

Sua API REST pode precisar retornar uma mensagem de erro, como "o usuário não foi encontrado no sistema de CRM". Se ocorrer um erro, a API REST deverá retornar uma mensagem de erro HTTP 409 (código de status de resposta de conflito). Para obter mais informações, consulte o [perfil técnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Isso só pode ser obtido com a chamada de um perfil técnico da API REST de um perfil técnico de validação. Isso permite que o usuário corrija os dados na página e execute a validação novamente no envio da página.

Uma resposta HTTP 409 é necessária para impedir o processamento de quaisquer perfis técnicos de validação subsequentes nesta etapa de orquestração.

Se você fizer referência a um perfil técnico da API REST diretamente de um percurso do usuário, o usuário será Redirecionado de volta para o aplicativo de terceira parte confiável com a mensagem de erro relevante.

## <a name="publishing-your-rest-api"></a>Publicando sua API REST

A solicitação para seu serviço de API REST vem de servidores Azure AD B2C. O serviço de API REST deve ser publicado em um ponto de extremidade HTTPS publicamente acessível. As chamadas à API REST chegarão de um endereço IP do Azure data center.

Projete seu serviço de API REST e seus componentes subjacentes (como o banco de dados e o sistema de arquivos) para serem altamente disponíveis.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de como usar um perfil técnico RESTful:

- [Walkthrough: integrar as trocas de declarações da API REST no percurso do usuário Azure AD B2C como validação da entrada do usuário](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Adicionar trocas de declarações da API REST a políticas personalizadas no Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja seus serviços de API REST](secure-rest-api.md)
- [Referência: perfil técnico RESTful](restful-technical-profile.md)