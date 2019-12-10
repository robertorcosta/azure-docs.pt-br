---
title: Configurar a inscrição e a entrada com uma conta do Weibo
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Weibo em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f84d1d33874ac70a21c9d596c6fa5a9e608bb84
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950299"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Weibo usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Criar um aplicativo Weibo

Para usar uma conta do Weibo como um provedor de identidade no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Weibo, poderá se inscrever em [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Acesse o [portal do desenvolvedor do Weibo](https://open.weibo.com/) com suas credenciais de conta do Weibo.
1. Depois de entrar, selecione seu nome para exibição no canto superior direito.
1. No menu suspenso, selecione **编辑开发者信息** (editar informações do desenvolvedor).
1. Insira as informações necessárias e selecione **提交** (enviar).
1. Conclua o processo de verificação de email.
1. Acesse a [página de verificação de identidade](https://open.weibo.com/developers/identity/edit).
1. Insira as informações necessárias e selecione **提交** (enviar).

### <a name="register-a-weibo-application"></a>Registrar um aplicativo Weibo

1. Acesse a [nova página de registro de aplicativo Weibo](https://open.weibo.com/apps/new).
1. Insira as informações necessárias de aplicativo.
1. Selecione **创建** (criar).
1. Copie os valores de **Chave do Aplicativo** e **Segredo do Aplicativo**. Você precisa de ambos para adicionar o provedor de identidade para seu locatário.
1. Carregue as fotos necessárias e insira as informações necessárias.
1. Selecione **保存以上信息** (salvar).
1. Selecione **高级信息** (informações avançadas).
1. Selecione **编辑** (editar) ao lado do campo referente a OAuth2.0 **授权设置** (URL de redirecionamento).
1. Digite `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` para OAuth2.0 **授权设置**(URL de redirecionamento). Por exemplo, se o nome do locatário é contoso, defina a URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecione **提交** (enviar).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurar a conta do Weibo como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Weibo (versão prévia)** .
1. Insira um **Nome**. Por exemplo, *Weibo*.
1. Para a **ID do cliente**, insira a chave de aplicativo do aplicativo Weibo que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do aplicativo que você registrou.
1. Clique em **Salvar**.
