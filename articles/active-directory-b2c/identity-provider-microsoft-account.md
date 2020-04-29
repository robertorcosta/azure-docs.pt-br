---
title: Configurar a inscrição e a entrada com uma conta da Microsoft
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas da Microsoft em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188011"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar um conta Microsoft como um [provedor de identidade](openid-connect.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo no locatário do Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C. Se você ainda não tiver uma conta Microsoft, poderá obter uma em [https://www.live.com/](https://www.live.com/).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**. Essa opção visa o conjunto mais amplo de identidades da Microsoft.

   Para obter mais informações sobre as diferentes seleções de tipo de conta, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **URI de redirecionamento (opcional)**, selecione `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **Web** e insira na caixa de texto. Substitua `your-tenant-name` pelo nome do locatário do Azure ad B2C.
1. Selecionar **registro**
1. Registre a **ID do aplicativo (cliente)** mostrada na página Visão geral do aplicativo. Você precisará disso quando configurar o provedor de identidade na próxima seção.
1. Selecionar **certificados & segredos**
1. Clique em **novo segredo do cliente**
1. Insira uma **Descrição** para o segredo, por exemplo, *senha de aplicativo 1*e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **valor** . Você precisará disso quando configurar o provedor de identidade na próxima seção.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta da Microsoft como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador global do seu locatário Azure ad B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **conta da Microsoft**.
1. Insira um **nome**. Por exemplo, *MSA*.
1. Para a **ID do cliente**, insira a ID do aplicativo (cliente) do aplicativo do Azure AD que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Salvar**.
