---
title: Garanta uma API de gerenciamento de API do Azure usando o Azure Active Directory B2C
description: Saiba como usar tokens de acesso emitidos pelo Azure Active Directory B2C para garantir um ponto final de API de gerenciamento de API do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186923"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Garanta uma API de gerenciamento de API do Azure com a Azure AD B2C

Saiba como restringir o acesso à Sua API Azure API (API) para clientes que autenticaram com o Azure Active Directory B2C (Azure AD B2C). Siga as etapas deste artigo para criar e testar uma política de entrada no APIM que restringe o acesso apenas às solicitações que incluem um token de acesso aD B2C válido.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos em vigor antes de continuar com as etapas deste artigo:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário
* [Publicada API](../api-management/import-and-publish.md) na Api Management da Azure
* [Carteiro](https://www.getpostman.com/) para testar acesso seguro (opcional)

## <a name="get-azure-ad-b2c-application-id"></a>Obter ID de aplicativo Ad AD AD

Quando você segura uma API no Azure API Management com o Azure AD B2C, você precisa de vários valores para a [política de entrada](../api-management/api-management-howto-policies.md) que você cria no APIM. Primeiro, registre o ID de aplicativo de um aplicativo que você criou anteriormente no seu inquilino Azure AD B2C. Se você estiver usando o aplicativo que criou nos pré-requisitos, use o ID do aplicativo para *webbapp1*.

Você pode usar a experiência atual **do Applications** ou a nossa nova experiência de registro unificado **de aplicativos (Preview)** para obter o ID do aplicativo. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Em **Gerenciar,** selecione **Aplicativos**.
1. Registre o valor na coluna **ID do aplicativo** para *webapp1* ou outro aplicativo que você criou anteriormente.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Registros do aplicativo (Visualização)** e selecione a guia **Aplicativos De propriedade.**
1. Registre o valor na coluna **ID do aplicativo (cliente)** para *webapp1* ou outro aplicativo que você criou anteriormente.

* * *

## <a name="get-token-issuer-endpoint"></a>Obter ponto final do emissor de token

Em seguida, obtenha a URL de configuração bem conhecida para um dos fluxos de usuário AD B2C do Azure. Você também precisa do uri de ponto final emissor de token que deseja suportar no Gerenciamento de API do Azure.

1. Navegue até o seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com).
1. Em **Políticas,** selecione **Fluxos de usuário (políticas)**.
1. Selecione uma diretiva existente, por *exemplo, B2C_1_signupsignin1,* e **selecione Executar fluxo de usuário**.
1. Registre a URL no hiperlink exibido a direção de fluxo de **usuário Executar** perto do topo da página. Esta URL é o ponto final de descoberta bem conhecido do OpenID Connect para o fluxo do usuário e você o usa na próxima seção quando configura a diretiva de entrada no Gerenciamento de API do Azure.

    ![Hiperlink URI bem conhecido na página Executar agora do portal Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecione o hiperlink para navegar na página de configuração conhecida Do OpenID Connect.
1. Na página que abre no seu `issuer` navegador, registre o valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Você usa esse valor na próxima seção quando configurar sua API no Azure API Management.

Agora você deve ter duas URLs gravadas para uso na próxima seção: o URL de ponto final de configuração bem conhecido Do OpenID Connect e o URI do emissor. Por exemplo: 

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configure a política de entrada no Gerenciamento de API do Azure

Agora você está pronto para adicionar a política de entrada no Gerenciamento de API do Azure que valida chamadas de API. Ao adicionar uma política [de validação JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) que verifica o público e o emissor em um token de acesso, você pode garantir que apenas chamadas de API com um token válido sejam aceitas.

1. Navegue até sua instância de Gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **APIs**.
1. Selecione a API que deseja proteger com o Azure AD B2C.
1. Selecione a guia **Design**.
1. Em **Processamento de entrada,** selecione ** \< / ** para abrir o editor de código de diretiva.
1. Coloque a `<validate-jwt>` seguinte `<inbound>` tag dentro da diretiva.

    1. Atualize `url` o `<openid-config>` valor no elemento com a URL de configuração bem conhecida da sua política.
    1. Atualize `<audience>` o elemento com id de aplicativo do aplicativo que você criou anteriormente no seu inquilino B2C (por exemplo, *webapp1*).
    1. Atualize `<issuer>` o elemento com o ponto final do emissor de token que você gravou anteriormente.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Valide o acesso seguro à API

Para garantir que apenas os chamadores autenticados possam acessar sua API, você pode validar sua configuração de gerenciamento de API do Azure ligando para a API com [o Carteiro](https://www.getpostman.com/).

Para chamar a API, você precisa de um token de acesso emitido pelo Azure AD B2C e uma chave de assinatura APIM.

### <a name="get-an-access-token"></a>Obter um token de acesso

Primeiro você precisa de um token emitido pelo Azure `Authorization` AD B2C para usar no cabeçalho no Carteiro. Você pode obter um usando o recurso **Executar agora** do seu fluxo de usuário de inscrição/login que você deveria ter criado como um dos pré-requisitos.

1. Navegue até o seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com).
1. Em **Políticas,** selecione **Fluxos de usuário (políticas)**.
1. Selecione um fluxo de usuário de inscrição/login existente, por exemplo, *B2C_1_signupsignin1*.
1. Para **aplicativo,** selecione *webapp1*.
1. Para url de `https://jwt.ms` **resposta,** escolha .
1. Selecione **Executar fluxo de usuário**.

    ![Executar página de fluxo de usuário para fazer login no fluxo de usuário no portal Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Conclua o processo de conexão. Você deve ser `https://jwt.ms`redirecionado para.
1. Registre o valor do token codificado exibido no seu navegador. Você usa este valor de token para o cabeçalho de autorização no Carteiro.

    ![Valor de token codificado exibido em jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Obtenha a chave de assinatura da API

Um aplicativo cliente (neste caso, Carteiro) que chama uma API publicada deve incluir uma chave de assinatura de Gerenciamento de API válida em suas solicitações HTTP à API. Para obter uma chave de assinatura para incluir em sua solicitação HTTP do Carteiro:

1. Navegue até a instância de serviço de gerenciamento da API do Azure no [portal Azure](https://portal.azure.com).
1. Selecione **Assinaturas**.
1. Selecione a elipse para **Produto: Ilimitada,** em seguida, selecione **'Mostrar/ocultar chaves .**
1. Registre **a CHAVE PRIMÁRIA** do produto. Você usa esta `Ocp-Apim-Subscription-Key` chave para o cabeçalho em sua solicitação HTTP no Carteiro.

![Página-chave de assinatura com chaves show/ocultação selecionadas no portal Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Teste uma chamada de API segura

Com a chave de assinatura do token de acesso e a apim registrada, agora você está pronto para testar se configurou corretamente o acesso seguro à API.

1. Crie uma `GET` nova solicitação no [Carteiro](https://www.getpostman.com/). Para a URL de solicitação, especifique o ponto final da lista de oradores da API que você publicou como um dos pré-requisitos. Por exemplo: 

    `https://contosoapim.azure-api.net/conference/speakers`

1. Em seguida, adicione os seguintes cabeçalhos:

    | Chave | Valor |
    | --- | ----- |
    | `Authorization` | Valor de token codificado que você `Bearer ` gravou anteriormente, prefixado com (inclua o espaço após "Portador") |
    | `Ocp-Apim-Subscription-Key` | Chave de assinatura APIM que você gravou anteriormente |

    O URL e **os cabeçalhos de** solicitação **GET** devem parecer semelhantes a:

    ![UI carteiro mostrando a URL de solicitação GET e cabeçalhos](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecione o botão **Enviar** no Carteiro para executar a solicitação. Se você configurou tudo corretamente, você deve ser presenteado com uma resposta JSON com uma coleção de palestrantes de conferência (mostrado aqui truncado):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Teste uma chamada de API insegura

Agora que você fez uma solicitação bem-sucedida, teste o caso de falha para garantir que as chamadas para sua API com um token *inválido* sejam rejeitadas como esperado. Uma maneira de executar o teste é adicionar ou alterar alguns caracteres no valor do token e, em seguida, executar a mesma `GET` solicitação de antes.

1. Adicione vários caracteres ao valor do token para simular um token inválido. Por exemplo, adicione "INVALID" ao valor do token:

    ![Seção de cabeçalhos da UI do Carteiro mostrando INVALID adicionado ao token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecione o botão **Enviar** para executar a solicitação. Com um token inválido, o `401` resultado esperado é um código de status não autorizado:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se você `401` ver o código de status, você verificou que somente os chamadores com um token de acesso válido emitido pelo Azure AD B2C podem fazer solicitações bem-sucedidas à sua API de gerenciamento de API do Azure.

## <a name="support-multiple-applications-and-issuers"></a>Suporte a vários aplicativos e emissores

Vários aplicativos normalmente interagem com uma única API REST. Para permitir que sua API aceite tokens destinados a vários aplicativos, adicione seus IDs de aplicativos ao `<audiences>` elemento na política de entrada apim.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Da mesma forma, para suportar vários emissores de tokens, adicione suas URIs de ponto final ao `<issuers>` elemento na política de entrada do APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrar para b2clogin.com

Se você tiver uma API APIM que valida `login.microsoftonline.com` tokens emitidos pelo ponto final legado, você deve migrar a API e os aplicativos que a chamam para usar tokens emitidos por [b2clogin.com](b2clogin.md).

Você pode seguir este processo geral para realizar uma migração em etapas:

1. Adicione suporte em sua política de entrada APIM para tokens emitidos por b2clogin.com e login.microsoftonline.com.
1. Atualize seus aplicativos um de cada vez para obter tokens do ponto final b2clogin.com.
1. Uma vez que todos os seus aplicativos estejam obtendo corretamente tokens de b2clogin.com, remova o suporte para tokens emitidos por login.microsoftonline.com da API.

O exemplo a seguir, a política de entrada APIM ilustra como aceitar tokens emitidos por b2clogin.com e login.microsoftonline.com. Além disso, ele suporta solicitações de API de dois aplicativos.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre as políticas de gerenciamento de API do Azure, consulte o [índice de referência de políticas APIM](../api-management/api-management-policies.md).

Você pode encontrar informações sobre a migração de APIs web baseadas em OWIN e seus aplicativos para b2clogin.com em [Migrar uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).
