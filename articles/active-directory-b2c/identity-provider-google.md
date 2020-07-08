---
title: Configurar a inscrição e a entrada com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 72e4de1473766d50512453ae38b6033ff0c5b73d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388027"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para usar uma conta do Google como um [provedor de identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo no console do Google Developers. Se você ainda não tiver uma conta do Google, poderá se inscrever em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) .

1. Entre no [Console de Desenvolvedores do Google](https://console.developers.google.com/) com suas credenciais de conta do Google.
1. No canto superior esquerdo da página, selecione a lista projeto e, em seguida, selecione **novo projeto**.
1. Insira um **nome de projeto**, selecione **criar**.
1. Verifique se você está usando o novo projeto selecionando a lista suspensa projeto no canto superior esquerdo da tela, selecione o projeto por nome e, em seguida, selecione **abrir**.
1. Selecione a **tela de consentimento do OAuth** no menu à esquerda, selecione **externo**e, em seguida, selecione **criar**.
Insira um **Nome** para seu aplicativo. Insira *b2clogin.com* na seção **domínios autorizados** e selecione **salvar**.
1. Selecione **Credenciais** no menu à esquerda e, em seguida, selecione **Criar Credenciais** > **ID do cliente Oauth**.
1. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
1. Insira um **Nome** para seu aplicativo, insira `https://your-tenant-name.b2clogin.com` em **Origens de JavaScript autorizadas** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores da **ID do cliente** e do **segredo do cliente**. Você precisará de ambos para configurar o Google como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar a conta do Google como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Google**.
1. Insira um **Nome**. Por exemplo, *Google*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo do Google que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Clique em **Salvar**.
