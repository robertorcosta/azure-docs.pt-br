---
title: Configurar a inscrição e a entrada com uma conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada aos consumidores com contas Microsoft em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387993"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para usar uma conta Microsoft como [provedor de identidade](openid-connect.md) no Azure Active Directory B2C (Azure AD B2C), é preciso criar um aplicativo no locatário do Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C. Se ainda não tiver uma conta Microsoft, obtenha uma em [https://www.live.com/](https://www.live.com/).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** .

   Confira mais informações sobre as diferentes seleções de tipo de conta em [Início Rápido: registrar um aplicativo na plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **URI de redirecionamento (opcional)** , selecione **Web** e insira `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `<tenant-name>` pelo nome de seu locatário do Azure AD B2C.
1. Selecione **Registrar**
1. Registre a **ID do aplicativo (do cliente)** mostrada na página Visão geral do aplicativo. Ela será necessária durante a configuração do provedor de identidade na próxima seção.
1. Selecione **Certificados e segredos**
1. Clique em **Novo segredo do cliente**
1. Insira uma **Descrição** para o segredo, por exemplo *Senha do aplicativo 1* e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **Valor**. Ela será necessária durante a configuração do provedor de identidade na próxima seção.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta da Microsoft como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Provedores de identidade** e depois **Conta Microsoft**.
1. Insira um **Nome**. Por exemplo, *MSA*.
1. Para a **ID do cliente**, insira a ID (do cliente) do aplicativo do Azure AD criado anteriormente.
1. Para o **Segredo do cliente**, insira o segredo que você registrou.
1. Clique em **Salvar**.
