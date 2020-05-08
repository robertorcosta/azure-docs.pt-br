---
title: Configurar a inscrição e a entrada com uma conta da Amazon
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas da Amazon em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3149d45f50c53209e3be6be6688c9c2ce8fb0555
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900342"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com a conta da Amazon usando o Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Criar um aplicativo no console do desenvolvedor do Amazon

Para usar uma conta da Amazon como um provedor de identidade federada no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seus [serviços de desenvolvedor e tecnologias do Amazon](https://developer.amazon.com). Se você ainda não tiver uma conta do Amazon, poderá se inscrever [https://www.amazon.com/](https://www.amazon.com/)em.

> [!NOTE]  
> Use as URLs a seguir na **etapa 8** abaixo, `your-tenant-name` substituindo pelo nome do seu locatário. Ao inserir o nome do locatário, use todas as letras minúsculas, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C.
> - Para **origens permitidas**, insira`https://your-tenant-name.b2clogin.com` 
> - Para **URLs de retorno permitidas**, insira`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurar uma conta da Amazon como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador global do seu locatário Azure ad B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Amazon**.
1. Insira um **nome**. Por exemplo, *Amazon*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo Amazon que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Clique em **Salvar**.
