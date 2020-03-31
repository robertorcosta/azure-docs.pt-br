---
title: Configure o login e faça login com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188133"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para usar uma conta do Google como provedor de [identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no seu Console de Desenvolvedores do Google. Se você ainda não tem uma conta no [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)Google, você pode se inscrever em .

1. Entre no [Console de Desenvolvedores do Google](https://console.developers.google.com/) com suas credenciais de conta do Google.
1. No canto superior esquerdo da página, selecione a lista de projetos e selecione **Novo projeto**.
1. Digite um **nome de projeto,** selecione **Criar**.
1. Certifique-se de que está usando o novo projeto selecionando o projeto em baixa no canto superior esquerdo da tela, selecione seu projeto por nome e selecione **Abrir**.
1. Selecione **a tela de consentimento OAuth** no menu esquerdo, selecione **Externo**e selecione **Criar**.
Digite um **nome** para sua inscrição. Digite *b2clogin.com* na seção **domínios autorizados** e **selecione Salvar**.
1. Selecione **Credenciais** no menu à esquerda e, em seguida, selecione **Criar Credenciais** > **ID do cliente Oauth**.
1. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
1. Insira um **Nome** para seu aplicativo, insira `https://your-tenant-name.b2clogin.com` em **Origens de JavaScript autorizadas** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores do **Customer ID** e **do Client secret**. Você precisará de ambos para configurar o Google como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar a conta do Google como um provedor de identidade

1. Faça login no [portal Azure](https://portal.azure.com/) como o administrador global do seu inquilino Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **o Google**.
1. Digite um **nome**. Por exemplo, *o Google*.
1. Para o **ID do cliente,** digite o ID do cliente do aplicativo do Google que você criou anteriormente.
1. Para o segredo do **Cliente,** digite o Segredo do Cliente que você gravou.
1. Selecione **Salvar**.
