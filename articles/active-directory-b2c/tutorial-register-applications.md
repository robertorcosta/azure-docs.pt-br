---
title: 'Tutorial: Registre um aplicativo'
titleSuffix: Azure AD B2C
description: Saiba como registrar seus aplicativo Web no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183084"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registre um aplicativo no Azure Active Directory B2C

Antes que seus [aplicativos](application-types.md) possam interagir com o Azure Active Directory B2C (Azure AD B2C), eles devem ser registrados em um inquilino que você gerencia. Este tutorial mostra como registrar um aplicativo Web usando o portal do Azure.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [Azure AD B2C Tenant,](tutorial-create-tenant.md)crie um agora. É possível usar um locatário existente do Azure AD B2C.

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

Para registrar um aplicativo no locatário do Azure Active Directory B2C, você pode usar a experiência **Aplicativos** atual ou a nossa nova experiência **Registros de Aplicativo (versão prévia)** unificada. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicativos**e selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *webapp1*.
1. Para **Incluir aplicativo da Web / API da Web** e **Permitir fluxo implícito**, selecione **Sim**.
1. Para a **URL de resposta**, insira um ponto de extremidade em que o Azure AD B2C deve retornar os tokens solicitados pelo seu aplicativo. Por exemplo, você pode configurá-lo para ouvir localmente em `https://localhost:44316`. Se você ainda não souber o número da porta, poderá inserir um valor de espaço reservado e alterá-lo posteriormente.

    Para fins de teste como este `https://jwt.ms` tutorial, você pode defini-lo para o qual exibe o conteúdo de um token para inspeção. Para este tutorial, defina `https://jwt.ms`a URL de **resposta** para .

    As seguintes restrições se aplicam às URLs de resposta:

    * A URL de resposta `https`deve começar com o esquema .
    * A URL de resposta é sensível a maiúsculas e minúsculas. Seu estojo deve coincidir com o caso do caminho de URL do seu aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc`como parte `.../ABC/response-oidc` de seu caminho, não especifique na URL de resposta. Como o navegador da Web trata os `.../abc/response-oidc` caminhos como sensíveis a maiúsculas `.../ABC/response-oidc` e minúsculas, os cookies associados podem ser excluídos se redirecionados para a URL incompatível com o caso.

1. Selecione **Criar** para concluir o registro do aplicativo.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Insira um **Nome** para o aplicativo. Por exemplo, *webapp1*.
1. Selecione **Contas em qualquer diretório organizacional ou em qualquer provedor de identidade**.
1. Em **Redirecionar URI,** selecione `https://jwt.ms` **Web**e digite na caixa de texto URL.

    O URI de redirecionamento é o ponto final para o qual o usuário é enviado pelo servidor de autorização (Azure AD B2C, neste caso) após concluir sua interação com o usuário, e para o qual um token de acesso ou código de autorização é enviado mediante autorização bem sucedida. Em um aplicativo de produção, é tipicamente um ponto final acessível ao público onde seu aplicativo está sendo executado, como `https://contoso.com/auth-response`. Para fins de teste como este tutorial, você pode defini-lo para `https://jwt.ms`, um aplicativo web de propriedade da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca sair do seu navegador). Durante o desenvolvimento do aplicativo, você pode adicionar o `https://localhost:5000`ponto final onde seu aplicativo ouve localmente, como . Você pode adicionar e modificar URIs de redirecionamento em seus aplicativos registrados a qualquer momento.

    As seguintes restrições se aplicam ao redirecionamento de URIs:

    * A URL de resposta `https`deve começar com o esquema .
    * A URL de resposta é sensível a maiúsculas e minúsculas. Seu estojo deve coincidir com o caso do caminho de URL do seu aplicativo em execução. Por exemplo, se o aplicativo incluir `.../abc/response-oidc`como parte `.../ABC/response-oidc` de seu caminho, não especifique na URL de resposta. Como o navegador da Web trata os `.../abc/response-oidc` caminhos como sensíveis a maiúsculas `.../ABC/response-oidc` e minúsculas, os cookies associados podem ser excluídos se redirecionados para a URL incompatível com o caso.

1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.

Uma vez que o registro do aplicativo esteja concluído, habilite o fluxo implícito de subvenção:

1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **Concessão implícita**, selecione ambas as caixas de seleção **Tokens de acesso** e **Tokens de ID**.
1. Selecione **Salvar**.

* * *

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

Se o seu aplicativo trocar um código de autorização por um token de acesso, você precisa criar um segredo de aplicativo.

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Na página **Azure AD B2C - Aplicativos,** selecione o aplicativo que você criou, por *exemplo, webapp1*.
1. Selecione **Chaves** e selecione **Gerar tecla**.
1. Selecione **salvar** para exibir a chave. Anote o valor da **Chave do Aplicativo**. Você usa esse valor como o segredo do aplicativo no código do seu aplicativo.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Na página **Azure AD B2C - Registros de aplicativos (Preview),** selecione o aplicativo que você criou, por *exemplo, webapp1*.
1. Em **Gerenciar**, selecione **Certificados e Segredos**.
1. Selecione **Novo segredo do cliente**.
1. Insira uma descrição para o segredo do cliente na caixa **Descrição**. Por exemplo, *clientsecret1*.
1. Em **Expirar**, selecione um período durante o qual o segredo será válido e clique em **Adicionar**.
1. Registre o valor do **segredo.** Você usa esse valor como o segredo do aplicativo no código do seu aplicativo.

* * *

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Registrar um aplicativo Web
> * Criar um segredo do cliente

Em seguida, saiba como criar fluxos de usuário para permitir que seus usuários se inscrevam, entrem e gerenciem seus perfis.

> [!div class="nextstepaction"]
> [Crie fluxos de usuário no Azure Active Directory B2C >](tutorial-create-user-flows.md)
