---
title: Trocas de declarações da API REST como validação
titleSuffix: Azure AD B2C
description: Uma explicação sobre como criar uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 209cddcc8648f7d396f048057bb4367e2ae9f66c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846608"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação na entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A IEF (Estrutura de Experiência de Identidade) subjacente ao Azure AD B2C (Azure Active Directory B2C) permite que o desenvolvedor de identidade integre uma interação com uma API RESTful em um percurso do usuário.

Ao final deste passo a passo, você estará apto a criar um percurso do usuário do Azure AD B2C que interage com serviços RESTful.

A IEF envia dados em declarações e recebe dados de volta em declarações. A interação com a API:

- Pode ser criada como uma troca de declarações da API REST ou como um perfil de validação, que ocorre em uma etapa de orquestração.
- Normalmente valida a entrada do usuário. Se o valor do usuário for rejeitado, o usuário poderá tentar inserir um valor válido novamente com a oportunidade de retornar uma mensagem de erro.

Você também pode criar a interação como uma etapa de orquestração. Para obter mais informações, consulte [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md).

No exemplo de perfil de validação, usaremos o percurso do usuário de edição de perfil no arquivo de starter pack ProfileEdit.xml.

Podemos verificar que o nome fornecido pelo usuário na edição de perfil não faz parte de uma lista de exclusões.

## <a name="prerequisites"></a>Pré-requisitos

- Um locatário do Azure AD B2C configurado para concluir uma inscrição/entrada de conta local, conforme descrito em [Introdução](custom-policy-get-started.md).
- Um ponto de extremidade de API REST com o qual se irá interagir. Para este passo a passo, criamos um site de demonstração chamado [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) com um serviço de API REST.

## <a name="step-1-prepare-the-rest-api-function"></a>Etapa 1: preparar a função da API REST

> [!NOTE]
> A configuração das funções da API REST está fora do escopo deste artigo. O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um kit de ferramentas excelente para criar serviços RESTful na nuvem.

Criamos uma função do Azure que recebe uma declaração esperada como `playerTag`. A função valida se essa declaração existe. Você pode acessar o código completo de função do Azure no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

O IEF espera a declaração `userMessage` retornada pela função do Azure. Essa declaração será apresentada como uma cadeia de caracteres para o usuário se a validação falhar, como quando um status de conflito 409 é retornado no exemplo anterior.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Etapa 2: Configurar a troca de declarações da API RESTful como um perfil técnico no arquivo TrustFrameworkExtensions.xml

Um perfil técnico é a configuração completa da troca desejada com o serviço RESTful. Abra o arquivo TrustFrameworkExtensions.xml e adicione o seguinte snippet de código XML dentro do elemento `<ClaimsProviders>`.

> [!NOTE]
> No XML a seguir, o provedor RESTful `Version=1.0.0.0` é descrito como o protocolo. Considere-o como a função que interagirá com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

O elemento `InputClaims` define as declarações que serão enviadas pela IEF para o serviço REST. Neste exemplo, o conteúdo da declaração `givenName` será enviado para o serviço REST como `playerTag`. Neste exemplo, o IEF não espera declarações novamente. Em vez disso, ele aguarda uma resposta do serviço REST e age de acordo com os códigos de status recebidos.

Os comentários acima `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, consulte [proteger APIs RESTful com autenticação básica](secure-rest-api-dotnet-basic-auth.md) e [proteger APIs RESTful com autenticação de certificado](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Etapa 3: Incluir a troca de declarações do serviço RESTful no perfil técnico autodeclarado no qual você deseja validar a entrada do usuário

O uso mais comum da etapa de validação é na interação com um usuário. Todas as interações nas quais o usuário deve fornecer uma entrada são *perfis técnicos autodeclarados*. Para este exemplo, adicionaremos essa validação ao perfil técnico Self-Asserted-ProfileUpdate. Esse é o perfil técnico utilizado pelo arquivo de política de RP (terceira parte confiável) `Profile Edit`.

Para adicionar a troca de declarações ao perfil técnico autodeclarado:

1. Abra o arquivo TrustFrameworkBase.xml e pesquise `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Examine a configuração desse perfil técnico. Observe como a troca com o usuário é definida como declarações que serão solicitadas ao usuário (declarações de entrada) e declarações esperadas novamente do provedor autodeclarado (declarações de saída).
3. Pesquise `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`e observe que esse perfil é invocado como a etapa de orquestração 5 do `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Etapa 4: Carregar e testar o arquivo de política de RP de edição de perfil

1. Carregue a nova versão do arquivo TrustFrameworkExtensions.xml.
2. Use **Executar agora** para testar o arquivo de política de edição do perfil RP.
3. Teste a validação fornecendo um dos nomes existentes (por exemplo, mcvinny) no campo **Nome Fornecido**. Se tudo estiver configurado corretamente, você deverá receber uma mensagem que notifica o usuário de que a marcação de player já está sendo usada.

## <a name="next-steps"></a>Próximos passos

[Modificar a edição de perfil e o registro de usuário para coletar informações adicionais dos usuários](custom-policy-custom-attributes.md)

[Passo a passo: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md)

[Referência: perfil técnico RESTful](restful-technical-profile.md)

Para saber como proteger suas APIs, consulte os seguintes artigos:

* [Proteja sua API RESTful com a autenticação básica (nome de usuário e senha)](secure-rest-api-dotnet-basic-auth.md)
* [Proteja sua API RESTful com certificados de cliente](secure-rest-api-dotnet-certificate-auth.md)
