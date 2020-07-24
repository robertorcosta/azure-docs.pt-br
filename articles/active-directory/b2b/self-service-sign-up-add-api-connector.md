---
title: Adicionar conectores de API a fluxos de inscrição de autoatendimento-Azure AD
description: Configure uma API Web a ser usada em um fluxo de usuário.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6238e89b3941668f831f3128bb0e723a4097e48
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027505"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Adicionar um conector de API a um fluxo de usuário

Para usar um [conector de API](api-connectors-overview.md), primeiro crie o conector de API e habilite-o em um fluxo de usuário.

## <a name="create-an-api-connector"></a>Criar um conector de API

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **todos os conectores de API (versão prévia)** e, em seguida, selecione **novo conector de API**.

   ![Adicionar um novo conector de API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Forneça um nome de exibição para a chamada. Por exemplo, **Verifique o status de aprovação**.
6. Forneça a **URL do ponto de extremidade** para a chamada à API.
7. Forneça as informações de autenticação para a API.

   - Somente a autenticação básica tem suporte no momento. Se você quiser usar uma API sem autenticação básica para fins de desenvolvimento, basta inserir um **nome de usuário** e **senha** fictícios que sua API pode ignorar. Para usar com uma função do Azure com uma chave de API, você pode incluir o código como um parâmetro de consulta na **URL do ponto de extremidade** (por exemplo, https []() ://contoso.azurewebsites.NET/API/Endpoint<b>? Code = 0123456789</b>).

   ![Adicionar um novo conector de API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Selecione as declarações que você deseja enviar para a API.
9. Selecione as declarações que você planeja receber da API.

   ![Definir declarações do conector de API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Clique em **Salvar**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Habilitar o conector de API em um fluxo de usuário

Siga estas etapas para adicionar um conector de API a um fluxo de usuário de inscrição de autoatendimento.

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **fluxos de usuário (versão prévia)** e, em seguida, selecione o fluxo de usuário ao qual você deseja adicionar o conector de API.
5. Selecione **conectores de API**e, em seguida, selecione os pontos de extremidade de API que você deseja invocar nas etapas a seguir no fluxo do usuário:

   - **Depois de entrar com um provedor de identidade**
   - **Antes de criar o usuário**

   ![Adicionar APIs ao fluxo do usuário](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Clique em **Salvar**.

Saiba mais sobre [onde você pode habilitar um conector de API em um fluxo de usuário](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Solicitação enviada para a API

Um conector de API se materializa como uma solicitação HTTP POST, enviando declarações selecionadas como pares chave-valor em um corpo JSON. A resposta também deve ter o cabeçalho HTTP `Content-Type: application/json` . Os atributos são serializados da mesma forma para Microsoft Graph atributos de usuário. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Solicitação de exemplo

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

A declaração de **localidades da interface do usuário (' ui_locales ')** é enviada por padrão em todas as solicitações. Ele fornece a localidade de um usuário e pode ser usado pela API para retornar respostas internacionalizadas. Ele não aparece no painel de configuração de API.

Se uma declaração a ser enviada não tiver um valor no momento em que o ponto de extremidade de API for chamado, a declaração não será enviada para a API.

Atributos personalizados podem ser criados para o usuário usando o formato de ** \<extensions-app-id> _AttributeName extension_** . Sua API deve esperar receber declarações nesse mesmo formato serializado. Sua API pode retornar declarações com ou sem o `<extensions-app-id>` . Para obter mais informações sobre atributos personalizados, consulte [definir atributos personalizados para fluxos de inscrição de autoatendimento](user-flow-add-custom-attributes.md).

> [!TIP] 
> as declarações de [**identidades (' identidades ')**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) e de **endereço de email (' email ')** podem ser usadas para identificar um usuário antes de terem uma conta em seu locatário. A declaração ' Identities ' é enviada quando um usuário é autenticado com um Google ou Facebook e ' email ' é sempre enviado.

## <a name="expected-response-types-from-the-web-api"></a>Tipos de resposta esperados da API Web

Quando a API Web recebe uma solicitação HTTP do Azure AD durante um fluxo de usuário, ela pode retornar essas respostas:

- [Resposta de continuação](#continuation-response)
- [Resposta de bloqueio](#blocking-response)
- [Validação-resposta de erro](#validation-error-response)

### <a name="continuation-response"></a>Resposta de continuação

Uma resposta de continuação indica que o fluxo do usuário deve continuar para a próxima etapa. Em uma resposta de continuação, a API pode retornar declarações.

Se uma declaração for retornada pela API e for selecionada como uma **declaração para receber**, a declaração fará o seguinte:

- Preenche previamente os campos de entrada na página de coleção de atributos se o conector da API for invocado antes da página ser apresentada.
- Substitui qualquer valor que já tenha sido atribuído à declaração.
- Atribui um valor à declaração se ela era nula anteriormente.

#### <a name="example-of-a-continuation-response"></a>Exemplo de uma resposta de continuação

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parâmetro                                          | Type              | Obrigatório | Descrição                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | Sim      | A versão da API.                                                                                                                                                                                                                                                                |
| ação                                             | String            | Sim      | O valor precisa ser `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Não       | Os valores podem ser armazenados no diretório se forem selecionados como uma **declaração para receber** na configuração do conector de API e nos **atributos de usuário** para um fluxo de usuário. Os valores podem ser retornados no token, se selecionado como uma **declaração de aplicativo**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Não       | A declaração retornada não precisa conter `_<extensions-app-id>_` . Os valores serão armazenados no diretório se forem selecionados como uma **declaração para receber** na configuração do conector de API e no **atributo de usuário** para um fluxo de usuário. Atributos personalizados não podem ser enviados de volta no token. |

### <a name="blocking-response"></a>Resposta de bloqueio

Uma resposta de bloqueio sai do fluxo do usuário. Ele pode ser emitido intencionalmente pela API para interromper a continuação do fluxo do usuário, exibindo uma página de bloco para o usuário. A página bloco exibe o `userMessage` fornecido pela API.

Exemplo de resposta de bloqueio:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parâmetro   | Type   | Obrigatório | Descrição                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Sim      | A versão da API.                                                    |
| ação      | String | Sim      | O valor deve ser`ShowBlockPage`                                              |
| userMessage | String | Sim      | Mensagem a ser exibida ao usuário.                                            |
| code        | Cadeia de caracteres | Não       | Código do erro. Pode ser usado para fins de depuração. Não são exibidos para o usuário. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Experiência do usuário final com uma resposta de bloqueio

![Exemplo de página de bloco](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Validação-resposta de erro

Uma chamada à API chamada após uma página de coleção de atributos pode retornar uma resposta de erro de validação. Ao fazer isso, o fluxo do usuário permanece na página coleção de atributos e o `userMessage` é exibido para o usuário. O usuário pode editar e reenviar o formulário. Esse tipo de resposta pode ser usado para validação de entrada.

#### <a name="example-of-a-validation-error-response"></a>Exemplo de uma resposta de erro de validação

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parâmetro   | Type    | Obrigatório | Descrição                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Sim      | A versão da API.                                                    |
| ação      | String  | Sim      | O valor precisa ser `ValidationError`.                                           |
| status      | Inteiro | Sim      | Deve ser `400` um valor para uma resposta do ValidationError.                        |
| userMessage | String  | Sim      | Mensagem a ser exibida ao usuário.                                            |
| code        | Cadeia de caracteres  | Não       | Código do erro. Pode ser usado para fins de depuração. Não são exibidos para o usuário. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Experiência do usuário final com uma resposta de erro de validação

![Página de validação de exemplo](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integração com o Azure Functions
Você pode usar um gatilho HTTP em Azure Functions como uma maneira simples de criar uma API para usar com o conector de API. Você usa a função do Azure para, [por exemplo](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), executar a lógica de validação e limitar as entradas a domínios específicos. Você também pode chamar e invocar outras APIs da Web, lojas de usuários e outros serviços de nuvem.

## <a name="next-steps"></a>Próximas etapas

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Saiba como [Adicionar um fluxo de trabalho de aprovação personalizado à inscrição de autoatendimento](self-service-sign-up-add-approvals.md)
- Introdução aos nossos [exemplos de início rápido do Azure function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
