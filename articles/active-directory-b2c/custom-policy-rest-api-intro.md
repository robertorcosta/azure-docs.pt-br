---
title: AAREST reivindica trocas na política personalizada B2C
titleSuffix: Azure AD B2C
description: Uma introdução à criação de uma jornada de usuário Azure AD B2C que interage com serviços RESTful.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337409"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integre as trocas de reclamações da API REST em sua política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Identity Experience Framework, que está por trás do Azure Active Directory B2C (Azure AD B2C), pode se integrar com APIs RESTful dentro de uma jornada de usuário. Este artigo mostra como criar uma jornada de usuário que interaja com um serviço RESTful usando um [perfil técnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Usando o Azure AD B2C, você pode adicionar sua própria lógica de negócios a uma jornada de usuário, chamando seu próprio serviço RESTful. O Identity Experience Framework pode enviar e receber dados do seu serviço RESTful para trocar reclamações. Por exemplo, você pode:

- **Validar os dados de entrada do usuário**. Por exemplo, você pode verificar se o endereço de e-mail fornecido pelo usuário existe no banco de dados do seu cliente e, se não, apresentar um erro.
- **Reivindicações de processo**. Se um usuário inserir seu primeiro nome em todas as letras minúsculas ou em todas as letras maiúsculas, sua API REST poderá formatar o nome apenas com a primeira letra capitalizada e devolvê-lo ao Azure AD B2C.
- **Enriqueça os dados do usuário, integrando-se ainda mais com aplicativos corporativos de linha de negócios.** Seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário ao Azure AD B2C. Em seguida, as reivindicações de devolução podem ser armazenadas na conta Azure AD do usuário, avaliadas nas próximas etapas de orquestração ou incluídas no token de acesso.
- **Execute a lógica de negócios personalizada.** Você pode enviar notificações push, atualizar bancos de dados corporativos, executar um processo de migração de usuários, gerenciar permissões, auditar bancos de dados e executar quaisquer outros fluxos de trabalho.

![Diagrama de uma troca de sinistros de serviço RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Chamando um serviço RESTful

A interação inclui uma troca de informações entre as reivindicações da API REST e o Azure AD B2C. É possível criar a integração com os serviços RESTful das seguintes maneiras:

- **Perfil técnico de validação**. A chamada para o serviço RESTful acontece dentro de um [perfil técnico](validation-technical-profile.md) de validação do perfil [técnico auto-afirmado especificado,](self-asserted-technical-profile.md)ou um controle de exibição de [verificação](display-control-verification.md) de um controle de [exibição](display-controls.md). O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário possa avançar. Com o perfil técnico de validação, é possível:

  - Envie reivindicações para sua API REST.
  - Valide as reivindicações e jogue mensagens de erro personalizadas exibidas ao usuário.
  - Envie de volta as reivindicações da API REST para etapas subsequentes de orquestração.

- **Troca de sinistros**. Uma troca direta de reclamações pode ser configurada chamando um perfil técnico da API REST diretamente de uma etapa de orquestração de uma [jornada do usuário](userjourneys.md). Essa definição é limitada a:

  - Envie reivindicações para sua API REST.
  - Valide as reivindicações e jogue mensagens de erro personalizadas que são devolvidas ao aplicativo.
  - Envie de volta as reivindicações da API REST para etapas subsequentes de orquestração.

Você pode adicionar uma chamada à API REST em qualquer etapa no percurso do usuário definido por uma política personalizada. Por exemplo, você pode chamar uma API REST:

- Durante o login, pouco antes do Azure AD B2C validar as credenciais.
- Imediatamente após o login.
- Antes do Azure AD B2C criar uma nova conta no diretório.
- Depois do Azure AD B2C cria uma nova conta no diretório.
- Antes do Azure AD B2C emitir um token de acesso.

![Coleta de perfil técnico de validação](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Enviar dados

No [perfil técnico RESTful,](restful-technical-profile.md)o `InputClaims` elemento contém uma lista de reivindicações a serem enviadas ao seu serviço RESTful. Você pode mapear o nome da sua reivindicação para o nome definido no serviço RESTful, definir um valor padrão e usar [resolver sinistros](claim-resolver-overview.md).

Você pode configurar como as reclamações de entrada são enviadas ao provedor de sinistros RESTful usando o atributo SendClaimsIn. Os valores possíveis são:

- **Corpo**, enviado no órgão de solicitação HTTP POST em formato JSON.
- **Formulário**, enviado no órgão de solicitação HTTP POST em um formato de valor-chave separado por ampersand '&'.
- **Cabeçalho**, enviado no cabeçalho de solicitação HTTP GET.
- **QueryString**, enviado na seqüência de consulta de solicitação HTTP GET.

Quando a opção **Corpo** é configurada, o perfil técnico da API REST permite que você envie uma carga de carga JSON complexa para um ponto final. Para obter mais informações, consulte [Enviar uma carga JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Recebendo dados

O `OutputClaims` elemento do [perfil técnico RESTful](restful-technical-profile.md) contém uma lista de reclamações devolvidas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Você também pode incluir reivindicações que não são retornadas pelo provedor de identidade REST API, desde que você defina o atributo DefaultValue.

As reivindicações de saída analisados pelo provedor de sinistros RESTful sempre esperam analisar uma resposta plana do Corpo JSON, tais como:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

As reivindicações de saída devem ser as seguintes:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Para analisar uma resposta do Corpo JSON aninhada, defina os metadados ResolveJsonPathsInJsonTokens como verdadeiros. Na reivindicação de saída, defina o PartnerClaimType para o elemento de caminho JSON que deseja produzir.

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


As reivindicações de saída devem parecer a seguir:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerações sobre segurança

Você deve proteger seu ponto final da API REST para que apenas clientes autenticados possam se comunicar com ele. A API REST deve usar um ponto final HTTPS. Defina os metadados AuthenticationType como um dos seguintes métodos de autenticação:

- **O certificado do cliente** restringe o acesso usando a autenticação do certificado do cliente. Somente os serviços que possuem os certificados apropriados podem acessar sua API. Você armazena o certificado do cliente em uma chave de política AD B2C do Azure. Saiba mais sobre como [proteger seu serviço RESTful usando certificados de cliente](secure-rest-api.md#https-client-certificate-authentication).
- **O Basic** protege a API REST com autenticação básica HTTP. Somente usuários verificados, incluindo Azure AD B2C, podem acessar a API. O nome de usuário e a senha são armazenados nas chaves de diretiva Ad B2C do Azure. Saiba como [proteger seus serviços RESTful usando autenticação básica HTTP](secure-rest-api.md#http-basic-authentication).
- **O Portador** restringe o acesso usando um token de acesso oAuth2 do cliente. O token de acesso é armazenado em uma chave de diretiva Azure AD B2C. Saiba mais sobre como [proteger seu serviço RESTful usando o token Bearer](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Plataforma de API REST
Sua API REST pode ser baseada em qualquer plataforma e escrita em qualquer idioma de programação, desde que seja segura e possa enviar e receber reclamações conforme especificado no [perfil técnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localize a API REST
Em um perfil técnico RESTful, você pode querer enviar o idioma/local e, se necessário, levantar uma mensagem de erro localizada. Usando o [resolver sinistros,](claim-resolver-overview.md)você pode enviar uma reclamação contextual, como o idioma do usuário. O exemplo a seguir mostra um perfil técnico RESTful demonstrando esse cenário.

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

## <a name="handling-error-messages"></a>Manipulação de mensagens de erro

Sua API REST pode precisar retornar uma mensagem de erro, como "O usuário não foi encontrado no sistema de CRM". Se ocorrer um erro, a API REST deve retornar uma mensagem de erro HTTP 409 (código de status de resposta a conflitos). Para obter mais informações, consulte o [perfil técnico RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Isso só pode ser conseguido ligando para um perfil técnico da API REST a partir de um perfil técnico de validação. Isso permite que o usuário corrija os dados na página e execute a validação novamente após o envio da página.

Uma resposta HTTP 409 é necessária para impedir o processamento de quaisquer perfis técnicos de validação subseqüentes dentro desta etapa de orquestração.

Se você referenciar um perfil técnico da API REST diretamente de uma jornada de usuário, o usuário será redirecionado de volta para o aplicativo de parte que confia com a mensagem de erro relevante.

## <a name="publishing-your-rest-api"></a>Publicando sua API REST

A solicitação ao seu serviço de API REST vem de servidores Azure AD B2C. O serviço de API REST deve ser publicado em um ponto final HTTPS acessível ao público. As chamadas da API REST chegarão a partir de um endereço IP do data center do Azure.

Projete seu serviço de API REST e seus componentes subjacentes (como o banco de dados e o sistema de arquivos) para estar altamente disponível.

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos para exemplos de uso de um perfil técnico RESTful:

- [Passo a passo: Integre as trocas de reclamações da API REST em sua jornada de usuário Azure AD B2C como validação da entrada do usuário](custom-policy-rest-api-claims-validation.md)
- [Passo a passo: Adicionar trocas de reclamações da API REST a políticas personalizadas no Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteja seus serviços de API REST](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)