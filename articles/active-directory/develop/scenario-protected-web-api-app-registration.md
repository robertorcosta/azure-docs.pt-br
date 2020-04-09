---
title: Registro de aplicativos de API web protegido | Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web protegida e as informações que você precisa para registrar o aplicativo.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2cdf3ff45a400d4b8d0b2605bf7ddc364aff1fe6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882379"
---
# <a name="protected-web-api-app-registration"></a>API web protegida: registro de aplicativos

Este artigo explica as especificidades do registro do aplicativo para uma API web protegida.

Para obter as etapas comuns para registrar um aplicativo, consulte [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Versão de token aceita

O ponto final da plataforma de identidade da Microsoft pode emitir tokens v1.0 e tokens v2.0. Para obter mais informações sobre esses tokens, consulte [Tokens de acesso](access-tokens.md).

A versão de token aceita depende do valor **dos tipos de conta suportados** que você escolher ao criar seu aplicativo.

- Se o valor dos tipos de **conta suportadas** for **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**, a versão do token aceita é v2.0.
- Caso contrário, a versão do token aceita é v1.0.

Depois de criar o aplicativo, você pode determinar ou alterar a versão de token aceita seguindo estas etapas:

1. No portal Azure, selecione seu aplicativo e **selecione Manifest**.
1. Encontre o acesso à **propriedadeTokenAcceptedVersion** no manifesto. O valor padrão da propriedade é 2.
1. O valor especifica para o Azure Active Directory (Azure AD) que a versão de token que a API web aceita.
    - Se o valor for 2, a API da Web aceita tokens v2.0.
    - Se o valor for **nulo,** a API da Web aceita tokens v1.0.
1. Se você alterou a versão do token, **selecione Salvar**.

> [!NOTE]
> A API web especifica qual versão de token aceita. Quando um cliente solicita um token para sua API web a partir do ponto final da plataforma de identidade Microsoft (v2.0), o cliente recebe um token que indica qual versão de token a API da Web aceita.

## <a name="no-redirect-uri"></a>Sem redirecionar uri

As APIs da Web não precisam registrar um URI de redirecionamento porque nenhum usuário está conectado interativamente.

## <a name="exposed-api"></a>API exposta

Outras configurações específicas para APIs da Web são a API exposta e os escopos expostos.

### <a name="application-id-uri-and-scopes"></a>Uri id de aplicativo e escopos

Os escopos geralmente `resourceURI/scopeName`têm o formulário . Para o Microsoft Graph, os escopos têm atalhos. Por exemplo, `User.Read` é `https://graph.microsoft.com/user.read`um atalho para .

Durante o registro do aplicativo, você precisa definir esses parâmetros:

- O recurso URI
- Um ou mais escopos
- Uma ou mais funções de aplicativo

Por padrão, o portal de registro de `api://{clientId}`aplicativos recomenda que você use o recurso URI . Este URI é único, mas não é legível humano. Se você alterar o URI, certifique-se de que o novo valor é único.

Para aplicativos de clientes, os escopos aparecem como *permissões delegadas* e as funções do aplicativo aparecem como *permissões de aplicativos* para sua API web.

Os escopos também aparecem na janela de consentimento apresentada aos usuários do seu aplicativo. Então você precisa fornecer as strings correspondentes que descrevem o escopo:

- Como visto por um usuário.
- Como visto por um inquilino, que pode conceder consentimento de admin.

### <a name="exposing-delegated-permissions-scopes"></a>Expondo permissões delegadas (escopos)

1. Selecione **Expor uma API** no registro do aplicativo.
1. Selecione **Adicionar um escopo**.
1. Se solicitado, aceite o ID`api://{clientId}`uri de aplicativo proposto , selecionando **Salvar e Continuar**.
1. Especifique esses valores:
    - Selecione **nome do escopo** e insira **access_as_user**.
    - Selecione **Quem pode consentir** e **certifique-se de que os admins e os usuários estão selecionados.**
    - Selecione **o nome de exibição de consentimento do Admin** e digite **Access TodoListService como usuário**.
    - Selecione **a descrição do consentimento do Admin** e digite **AaPI da Web TodoListService como usuário**.
    - Selecione **o nome de exibição de consentimento do usuário** e digite Access **TodoListService como usuário**.
    - Selecione **a descrição do consentimento do usuário** e digite **Acessa a API da Web TodoListService como usuário**.
    - Mantenha o valor **do Estado** definido **como Ativado**.
 1. Selecione **Adicionar escopo**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se sua API web for chamada por um aplicativo daemon

Nesta seção, você aprende como registrar sua API web protegida para que os aplicativos daemon possam chamá-la com segurança.

- Você declara e expõe apenas *permissões de aplicativos* porque os aplicativos daemon não interagem com os usuários. Permissões delegadas não fariam sentido.
- Os administradores de inquilinos podem exigir que o Azure AD emita tokens de API da Web apenas para aplicativos que se registraram para acessar uma das permissões de solicitação da API.

#### <a name="exposing-application-permissions-app-roles"></a>Expondo permissões de aplicativos (funções de aplicativo)

Para expor as permissões de aplicativo, você precisa editar o manifesto.

1. No registro de inscrição para sua inscrição, selecione **Manifest**.
1. Para editar o manifesto, encontre a `appRoles` configuração e adicione as funções do aplicativo. As definições de função são fornecidas no bloco JSON da amostra a seguir.
1. Deixe `allowedMemberTypes` definido `"Application"` para apenas.
1. Certifique-se de que `id` é um GUID único.
1. `displayName` Certifique-se `value` e não contenha espaços.
1. Salve o manifesto.

A amostra a seguir `appRoles`mostra o `id` conteúdo de , onde o valor de pode ser qualquer GUID único.

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Garantir que o Azure AD emita tokens para sua API web para permitir apenas clientes

A API web verifica a função do aplicativo. Essa função é a maneira de um desenvolvedor de software expor permissões de aplicativos. Você também pode configurar o Azure AD para emitir tokens de API apenas para aplicativos que o administrador de inquilinos aprova para acesso à API.

Para adicionar essa maior segurança:

1. Vá para a página **visão geral** do aplicativo para o seu registro do aplicativo.
1. Em **aplicativo gerenciado no diretório local,** selecione o link com o nome do seu aplicativo. O rótulo para esta seleção pode ser truncado. Por exemplo, você pode ver **aplicativo gerenciado em ...**

   > [!NOTE]
   >
   > Quando você seleciona este link, você vai para a página **Visão geral do aplicativo corporativo.** Esta página está associada ao diretor de serviço para sua aplicação no inquilino onde você a criou. Você pode ir para a página de registro do aplicativo usando o botão de volta do seu navegador.

1. Selecione a página **Propriedades** na seção **Gerenciar** as páginas do aplicativo Empresa.
1. Se você quiser que o Azure AD permita o acesso à sua **Yes**API web apenas de certos clientes, defina **a atribuição de usuário necessária?**

   > [!IMPORTANT]
   >
   > Se você definir **a atribuição de usuário necessária?** para **Sim**, o Azure AD verifica as atribuições da função do aplicativo de um cliente quando ele solicita um token de acesso à API da Web. Se o cliente não for atribuído a nenhuma função do aplicativo, o Azure AD retornará a mensagem\> de erro "invalid_client: AADSTS501051: O nome do aplicativo \<\> \<não é atribuído a uma função para a API web".
   >
   > Se você mantiver **a atribuição de usuário necessária?** Definido como **No**, o Azure AD não verificará as atribuições da função do aplicativo quando um cliente solicitar um token de acesso para sua API web. Qualquer cliente daemon, ou seja, qualquer cliente que use o fluxo de credenciais do cliente, pode obter um token de acesso para a API apenas especificando seu público. Qualquer aplicativo pode acessar a API sem ter que solicitar permissões para ela.
   >
   > Mas, como explicado na seção anterior, sua API web sempre pode verificar se o aplicativo tem a função certa, que é autorizada pelo admin inquilino. A API realiza essa verificação validando que o token de acesso tem uma reclamação de funções e que o valor para esta reclamação está correto. Na amostra JSON anterior, `access_as_application`o valor é .

1. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração do código do aplicativo](scenario-protected-web-api-app-configuration.md)
