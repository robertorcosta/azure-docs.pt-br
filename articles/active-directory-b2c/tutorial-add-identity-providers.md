---
title: 'Tutorial: adicionar provedores de identidade a seus aplicativos'
titleSuffix: Azure AD B2C
description: Saiba como adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183509"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C

Em seus aplicativos, você talvez queira permitir que os usuários entrem com diferentes provedores de identidade. Um *provedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicativos. Você pode adicionar provedores de identidade que têm suporte do Azure Active Directory B2C (Azure AD B2C) para seus [fluxos de usuário](user-flow-overview.md) usando o portal do Azure.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar aplicativos do provedor de identidade
> * Adicionar os provedores de identidade ao seu locatário
> * Adicione os provedores de identidade ao seu fluxo de usuário

Você normalmente usa apena um provedor de identidade em seus aplicativos, mas tem a opção de adicionar mais. Este tutorial mostra como adicionar um provedor de identidade do Azure AD e um provedor de identidade do Facebook ao seu aplicativo. Adicionar ambos esses provedores de identidade para seu aplicativo é opcional. Você também pode adicionar outros provedores de identidade, como [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)ou [Twitter](identity-provider-twitter.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.

## <a name="create-applications"></a>Criar aplicativos

Aplicativos do provedor de identidade fornecem o identificador e a chave para habilitar a comunicação com seu locatário do Azure AD B2C. Nesta seção do tutorial, você pode criar um aplicativo do Azure AD e um aplicativo do Facebook do qual você obtém identificadores e chaves para adicionar os provedores de identidade para seu locatário. Se você estiver adicionando apenas um dos provedores de identidade, precisará criar o aplicativo para o provedor.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Para habilitar a entrada para usuários do Azure AD, você precisará registrar um aplicativo no locatário do Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um nome para seu aplicativo. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção de **contas neste diretório organizacional somente** para este aplicativo.
1. Para o **URI de redirecionamento**, aceite o valor de **Web** e insira a URL a seguir em todas as letras minúsculas, substituindo `your-B2C-tenant-name` pelo nome do seu locatário Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).

1. Selecione **registrar**e registre a **ID do aplicativo (cliente)** que você usa em uma etapa posterior.
1. Em **gerenciar** no menu aplicativo, selecione **certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
1. Selecione o período de validade. Para este aplicativo, aceite a seleção de **em 1 ano**.
1. Selecione **Adicionar**e registre o valor do novo segredo do cliente que você usa em uma etapa posterior.

### <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como provedor de identidade no Azure AD B2C, você precisará criar um aplicativo no Facebook. Se ainda não tiver uma conta do Facebook, consiga uma conta em [https://www.facebook.com/](https://www.facebook.com/).

1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
1. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para fazer isso **, selecione introdução** no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
1. Selecione **meus aplicativos** e **criar aplicativo**.
1. Insira um **Nome de Exibição** e um **Email de Contato** válido.
1. Clique em **Criar ID de Aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
1. Escolha **Configurações** > **Básicas**.
1. Escolha uma **Categoria**, por exemplo, `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado pelo Azure AD B2C.
1. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
1. Na **URL do Site**, insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu locatário.
1. Insira uma URL para a **URL da Política de Privacidade**, por exemplo `http://www.contoso.com/`. A URL da política de privacidade é uma página que você mantém para fornecer informações de privacidade para seu aplicativo.
1. Selecione **Salvar alterações**.
1. Na parte superior da página, registre o valor da **ID do aplicativo**.
1. Ao lado de **segredo do aplicativo**, selecione **Mostrar** e Registre seu valor. Você usa a ID do aplicativo e o segredo do aplicativo para configurar o Facebook como um provedor de identidade em seu locatário. O **segredo do aplicativo** é uma credencial de segurança importante que você deve armazenar com segurança.
1. Selecione o sinal de adição ao lado de **produtos**e, em seguida, em **logon do Facebook**, selecione **Configurar**.
1. Em **logon do Facebook** no menu à esquerda, selecione **configurações**.
1. Em **URIs de Redirecionamento do OAuth Válidos**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu locatário. Selecione **salvar alterações** na parte inferior da página.
1. Para disponibilizar seu aplicativo do Facebook para Azure AD B2C, clique no seletor de **status** na parte superior direita da página e **ative-o** para tornar o aplicativo público e, em seguida, clique em **confirmar**. Neste ponto, o Status deverá mudar de **Desenvolvimento** para **Ativo**.

## <a name="add-the-identity-providers"></a>Adicionar os provedores de identidade

Depois de criar o aplicativo para o provedor de identidade que você deseja adicionar, adicione o provedor de identidade ao seu locatário.

### <a name="add-the-azure-active-directory-identity-provider"></a>Adicionar o provedor de identidade do Azure Active Directory

1. Verifique se você está usando o diretório que contém Azure AD B2C locatário. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **provedores de identidade**e, em seguida, selecione **novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, insira *Contoso Azure AD*.
1. Para **URL de metadados**, insira a URL a seguir substituindo `your-AD-tenant-domain` pelo nome de domínio do seu locatário do Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. Deixe os valores padrão para **escopo**, **tipo de resposta**e **modo de resposta**.
1. Adicional Insira um valor para **Domain_hint**. Por exemplo, *ContosoAD*. [Dicas de domínio](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) são diretivas que são incluídas na solicitação de autenticação de um aplicativo. Elas podem ser usadas para agilizar o usuário até a página de entrada IdP. Ou elas podem ser usadas por um aplicativo multilocatário para agilizar o usuário diretamente para a página de entrada do Azure AD da marca do locatário.
1. Em **mapeamento de declarações do provedor de identidade**, insira os seguintes valores de mapeamento de declarações:

    * **ID de usuário**: *OID*
    * **Nome de exibição**: *nome*
    * **Nome fornecido**: *given_name*
    * **Sobrenome**: *family_name*
    * **Email**: *unique_name*

1. Clique em **Salvar**.

### <a name="add-the-facebook-identity-provider"></a>Adicionar o provedor de identidade do Facebook

1. Selecione **provedores de identidade**e, em seguida, selecione **Facebook**.
1. Insira um **Nome**. Por exemplo, *Facebook*.
1. Para a **ID do cliente**, insira a ID do aplicativo do Facebook que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do aplicativo que você registrou.
1. Clique em **Salvar**.

## <a name="update-the-user-flow"></a>Atualizar o fluxo de usuário

No tutorial que você concluiu como parte dos pré-requisitos, você criou um fluxo de usuário para inscrição e entrada chamado *B2C_1_signupsignin1*. Nesta seção, você adiciona provedores de identidade ao fluxo de usuários *B2C_1_signupsignin1*.

1. Selecione **Fluxos dos usuários (políticas)** e, em seguida, selecione o fluxo de usuário *B2C_1_signupsignin1*.
2. Selecione **Provedores de identidade**, selecione os provedores de identidade do **Facebook** e **Azure AD da contoso** que você adicionou.
3. Clique em **Salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Na página Visão geral do fluxo do usuário que você criou, selecione **Executar o fluxo do usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **executar fluxo de usuário**e entre com um provedor de identidade que você adicionou anteriormente.
1. Repita as etapas 1 a 3 para outros provedores de identidade que você adicionou.

Se a operação de entrada for bem-sucedida, você será redirecionado para `https://jwt.ms` que exibe o token decodificado, semelhante a:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar aplicativos do provedor de identidade
> * Adicionar os provedores de identidade ao seu locatário
> * Adicione os provedores de identidade ao seu fluxo de usuário

Em seguida, saiba como personalizar a interface do usuário das páginas mostradas aos usuários como parte de sua experiência de identidade em seus aplicativos:

> [!div class="nextstepaction"]
> [Personalize a interface do usuário dos aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md)
