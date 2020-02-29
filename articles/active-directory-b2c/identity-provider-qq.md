---
title: Configurar a inscrição e entrada com a conta do QQ usando o Azure Active Directory B2C
description: Forneça a inscrição e entrada aos consumidores com contas do QQ em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187980"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do QQ usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Criar um aplicativo QQ

Para usar uma conta do QQ como um provedor de identidade no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do QQ, poderá se inscrever em [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrar-se no programa de desenvolvedores do QQ

1. Entre no [portal do desenvolvedor do QQ](http://open.qq.com) com suas credenciais de conta do QQ.
1. Após a autenticação, acesse [https://open.qq.com/reg](https://open.qq.com/reg) para registrar-se como desenvolvedor.
1. Selecione **个人** (desenvolvedor individual).
1. Insira as informações necessárias e selecione **下一步** (próxima etapa).
1. Conclua o processo de verificação de email. Você precisará aguardar alguns dias para ser aprovado depois de registrar-se como desenvolvedor.

### <a name="register-a-qq-application"></a>Registrar um aplicativo do QQ

1. Ir para [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selecione **应用管理**(gerenciamento de aplicativos).
1. Selecione **创建应用** (criar aplicativo) e insira as informações necessárias.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` em **授权回调域** (URL de retorno de chamada). Por exemplo, se `tenant_name` for contoso, defina a URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecione **创建应用** (criar aplicativo).
1. Na página de confirmação, selecione **应用管理**(gerenciamento de aplicativos) para retornar à página de gerenciamento de aplicativos.
1. Selecione **查看** (exibir) ao lado do aplicativo que você criou.
1. Selecione **修改** (editar).
1. Copie a **ID DO APLICATIVO** e a **CHAVE DO APLICATIVO**. Você precisa de ambos os valores para adicionar o provedor de identidade para seu locatário.

## <a name="configure-qq-as-an-identity-provider"></a>Configurar o QQ como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **QQ (versão prévia)** .
1. Insira um **Nome**. Por exemplo, *QQ*.
1. Para a **ID do cliente**, insira a ID do aplicativo QQ que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave do aplicativo que você registrou.
1. Clique em **Salvar**.
