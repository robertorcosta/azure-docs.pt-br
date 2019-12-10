---
title: Configurar a inscrição e a entrada com uma conta do Twitter
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Twitter em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b316fdb185c8a9c78e1a0c9be5f7d57b809141c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950435"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Twitter usando o Azure Active Directory B2C

## <a name="create-an-application"></a>Criar um aplicativo

Para usar o Twitter como provedor de identidade no Azure AD B2C, você precisará criar um aplicativo do Twitter. Se você ainda não tiver uma conta do Twitter, poderá se inscrever em [https://twitter.com/signup](https://twitter.com/signup).

1. Entre no site [Desenvolvedores do Twitter](https://developer.twitter.com/en/apps) com suas credencias de conta do Twitter.
1. Selecione **Criar um aplicativo**.
1. Insira um **Nome do aplicativo** e uma **Descrição do aplicativo**.
1. Na **URL do site**, insira `https://your-tenant.b2clogin.com`. Substitua `your-tenant` pelo nome do seu locatário. Por exemplo, https://contosob2c.b2clogin.com.
1. Insira `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` como o valor da **URL de Retorno de Chamada**. Substitua `your-tenant` pelo nome do locatário e `your-user-flow-Id` pelo identificador do fluxo de usuário. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário e a ID do fluxo do usuário, mesmo que elas estejam definidas com letras maiúsculas no Azure AD B2C.
1. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **Criar**.
1. Na página **Detalhes do aplicativo**, selecione **Editar > Editar detalhes**, marque a caixa de **Habilitar entrada com o Twitter** e, em seguida, selecione **Salvar**.
1. Selecione **Chaves e tokens** e registre os valores da **Chave da API do consumidor** e da **Chave secreta da API do consumidor** que serão usados mais tarde.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Twitter**.
1. Insira um **Nome**. Por exemplo, *Twitter*.
1. Para a **ID do cliente**, insira a chave de API do consumidor do aplicativo do Twitter que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave secreta da API do consumidor que você registrou.
1. Clique em **Salvar**.
