---
title: Configure o login e faça login com uma conta do LinkedIn
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do LinkedIn em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188093"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do LinkedIn usando o Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn

Para usar uma conta do LinkedIn como provedor de [identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu inquilino que o represente. Se você ainda não tem uma conta no LinkedIn, você pode se inscrever em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Entre no [site de Desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) com suas credencias de conta do LinkedIn.
1. Escolha **Meus Aplicativos** e clique em **Criar Aplicativo**.
1. Insira **Nome da Empresa**, **Nome do Aplicativo**, **Descrição do Aplicativo**, **Logotipo do Aplicativo**, **Uso do Aplicativo**, **URL do Site**, **Email Comercial** e **Telefone Comercial**.
1. Aceite os **Termos de Uso da API do LinkedIn** e clique em **Enviar**.
1. Copie os valores de **Customer ID** e **Client Secret**. Você pode encontrá-los em **Chaves de Autenticação**. Você precisará de ambos para configurar o LinkedIn como um provedor de identidade no seu locatário. O **segredo do cliente** é uma credencial de segurança importante.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de Redirecionamento Autorizadas**. Substitua `your-tenant-name` pelo nome do seu locatário. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C. Selecione **Adicionar** e depois clique em **Atualizar**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurar uma conta do LinkedIn como um provedor de identidade

1. Faça login no [portal Azure](https://portal.azure.com/) como o administrador global do seu inquilino Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **LinkedIn**.
1. Digite um **nome**. Por exemplo, *LinkedIn*.
1. Para o **ID do cliente,** digite o ID do cliente do aplicativo LinkedIn que você criou anteriormente.
1. Para o segredo do **Cliente,** digite o Segredo do Cliente que você gravou.
1. Selecione **Salvar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [suas APIs de v1.0 para v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, o Azure AD B2C só pode obter o nome completo do usuário do LinkedIn durante a inscrição. Se um endereço de e-mail for um dos atributos coletados durante a inscrição, o usuário deve digitar manualmente o endereço de e-mail e validá-lo.
