---
title: Configure o login e faça login com uma conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça login e login aos clientes com contas Microsoft em seus aplicativos usando o Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188011"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar uma conta microsoft como provedor de [identidade](openid-connect.md) no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no inquilino Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C. Se você ainda não tem uma conta microsoft, [https://www.live.com/](https://www.live.com/)você pode obter uma em .

1. Faça login no [portal Azure](https://portal.azure.com).
1. Certifique-se de que está usando o diretório que contém o inquilino Azure AD selecionando o filtro **de assinatura Diretório +** no menu superior e escolhendo o diretório que contém o inquilino Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Digite um **nome** para sua inscrição. Por exemplo, *MSAapp1*.
1. Em **tipos de conta suportados,** selecione Contas em qualquer diretório organizacional e contas pessoais da Microsoft **(por exemplo, Skype, Xbox, Outlook.com)**. Essa opção tem como alvo o conjunto mais amplo de identidades da Microsoft.

   Para obter mais informações sobre as diferentes seleções de tipo de conta, consulte [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **Redirecionar URI (opcional)**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` selecione **Web** e digite na caixa de texto. Substitua pelo `your-tenant-name` nome de inquilino Azure AD B2C.
1. Selecionar **Registro**
1. Registre o **ID do aplicativo (cliente)** mostrado na página Visão Geral do aplicativo. Você precisa disso quando configurar o provedor de identidade na próxima seção.
1. Selecione **Certificados & segredos**
1. Clique em **Novo cliente secreto**
1. Digite uma **descrição** para o segredo, por exemplo *Senha de aplicativo 1*e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **Valor.** Você precisa disso quando configurar o provedor de identidade na próxima seção.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta da Microsoft como um provedor de identidade

1. Faça login no [portal Azure](https://portal.azure.com/) como o administrador global do seu inquilino Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **A Conta Microsoft**.
1. Digite um **nome**. Por exemplo, *MSA.*
1. Para o **ID do cliente,** digite o ID de aplicativo (cliente) do aplicativo Azure AD que você criou anteriormente.
1. Para o segredo do **Cliente,** digite o segredo do cliente que você gravou.
1. Selecione **Salvar**.
