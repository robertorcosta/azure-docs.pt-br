---
title: Configurar a inscrição e a entrada com uma conta do GitHub
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do GitHub em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188198"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do GitHub usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Crie um aplicativo GitHub OAuth

Para usar uma conta do GitHub como um [provedor de identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do GitHub, poderá se inscrever em [https://www.github.com/](https://www.github.com/).

1. Entrar no site de [Desenvolvedor do GitHub](https://github.com/settings/developers) com suas credenciais do GitHub.
1. Selecione **Aplicativos do OAuth** e, em seguida, selecione **Novo Aplicativo do OAuth**.
1. Insira um **nome do Aplicativo** e **URL da home page**.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URL de retorno de chamada de autorização**. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Use todas as letras minúsculas, ao inserir o nome do locatário, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Registrar aplicativo**.
1. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará delas para adicionar o provedor de identidade ao locatário.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurar a conta do GitHub como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **GitHub (versão prévia)** .
1. Insira um **Nome**. Por exemplo, *GitHub*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo GitHub que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Clique em **Salvar**.
