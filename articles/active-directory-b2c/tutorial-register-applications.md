---
title: 'Tutorial: registrar um aplicativo'
titleSuffix: Azure AD B2C
description: Saiba como registrar seus aplicativo Web no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76d38e4b35111cd2c09685653fb4948f98387132
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950775"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: registrar um aplicativo no Azure Active Directory B2C

Antes que seus [aplicativos](active-directory-b2c-apps.md) possam interagir com Azure Active Directory B2C (Azure ad B2C), eles devem ser registrados em um locatário que você gerencia. Este tutorial mostra como registrar um aplicativo Web usando o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](tutorial-create-tenant.md), crie um agora. É possível usar um locatário existente do Azure AD B2C.

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

Para registrar um aplicativo no locatário do Azure AD B2C, você pode usar a experiência **Aplicativos** atual ou nossa nova experiência **Registros de aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *webapp1*.
1. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
1. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode defini-lo para escutar localmente em `https://localhost:44316`. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente.

    Para fins de teste como este tutorial, você pode defini-lo como `https://jwt.ms` que exibe o conteúdo de um token para inspeção. Para este tutorial, defina a **URL de resposta** como `https://jwt.ms`.

    As seguintes restrições se aplicam a URLs de resposta:

    * A URL de resposta deve começar com o esquema `https`.
    * A URL de resposta diferencia maiúsculas de minúsculas. Seu caso deve corresponder ao caso do caminho da URL do seu aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos como diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` incompatível com maiúsculas e minúsculas.

1. Clique em **criar** para concluir o registro do aplicativo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *webapp1*.
1. Selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade**.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira `https://jwt.ms` na caixa de texto URL.

    O URI de redirecionamento é o ponto de extremidade para o qual o usuário é enviado pelo servidor de autorização (Azure AD B2C, nesse caso) depois de concluir sua interação com o usuário e para o qual um token de acesso ou código de autorização é enviado após a autorização bem-sucedida. Em um aplicativo de produção, normalmente é um ponto de extremidade publicamente acessível em que seu aplicativo está em execução, como `https://contoso.com/auth-response`. Para fins de teste como este tutorial, você pode defini-lo como `https://jwt.ms`, um aplicativo da Web de propriedade da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca deixa o navegador). Durante o desenvolvimento do aplicativo, você pode adicionar o ponto de extremidade onde seu aplicativo escuta localmente, como `https://localhost:5000`. Você pode adicionar e modificar URIs de redirecionamento em seus aplicativos registrados a qualquer momento.

    As seguintes restrições se aplicam a URIs de redirecionamento:

    * A URL de resposta deve começar com o esquema `https`.
    * A URL de resposta diferencia maiúsculas de minúsculas. Seu caso deve corresponder ao caso do caminho da URL do seu aplicativo em execução. Por exemplo, se seu aplicativo incluir como parte de seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` na URL de resposta. Como o navegador da Web trata os caminhos como diferenciando maiúsculas de minúsculas, os cookies associados a `.../abc/response-oidc` podem ser excluídos se forem redirecionados para a URL de `.../ABC/response-oidc` incompatível com maiúsculas e minúsculas.

1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.

Depois que o registro do aplicativo for concluído, habilite o fluxo de concessão implícita:

1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **Concessão implícita**, selecione ambas as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Clique em **Salvar**.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se seu aplicativo trocar um código para um token, você precisará criar um segredo do aplicativo.

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Na página **Azure ad B2C-aplicativos** , selecione o aplicativo que você criou, por exemplo *webapp1*.
1. Selecione **chaves** e, em seguida, selecione **gerar chave**.
1. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Você usa esse valor como o segredo do aplicativo no código do aplicativo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Na página **Azure AD B2C registros de aplicativo (versão prévia)** , selecione o aplicativo que você criou, por exemplo *webapp1*.
1. Em **Gerenciar**, selecione **Certificados e Segredos**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição**. Por exemplo, *clientsecret1*.
1. Em **Expirar**, selecione um período durante o qual o segredo será válido e clique em **Adicionar**.
1. Registre o **Valor** do segredo. Você usa esse valor como o segredo do aplicativo no código do aplicativo.

* * *

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Em seguida, saiba como criar fluxos de usuário para permitir que os usuários se inscrevam, entrem e gerenciem seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de usuário no Azure Active Directory B2C >](tutorial-create-user-flows.md)
