---
title: Configure o login e faça login com uma conta do Facebook
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Facebook em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188266"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Facebook usando o Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como provedor de [identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em seu inquilino que o represente. Se você ainda não tem uma conta no [https://www.facebook.com/](https://www.facebook.com/)Facebook, você pode se inscrever em .

1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
1. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para isso, **selecione 'Iniciar'** no canto superior direito da página, aceite as políticas do Facebook e complete as etapas de registro.
1. Selecione **Meus aplicativos** e, em seguida, **crie aplicativo**.
1. Insira um **Nome de Exibição** e um **Email de Contato** válido.
1. Selecione **Criar ID de aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
1. Selecione **Configurações** > **Básicas**.
1. Escolha uma **Categoria**, por exemplo, `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado para o Azure AD B2C.
1. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
1. Na **URL do Site**, insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu locatário. Insira uma URL para a **URL da Política de Privacidade**, por exemplo `http://www.contoso.com`. A URL da política é uma página que você mantém para fornecer informações de privacidade para o seu aplicativo.
1. Selecione ** Salvar alterações **.
1. Na parte superior da página, copie o valor de **ID do Aplicativo**.
1. Selecione **Mostrar** e copie o valor do **App Secret**. Você usará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
1. Selecione o sinal de acrescido ao lado **de PRODUTOS**e selecione **Configurar** em Login **do Facebook**.
1. Em **Login do Facebook,** selecione **Configurações**.
1. Em **URIs de Redirecionamento do OAuth Válidos**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu locatário. Selecione **Salvar alterações** na parte inferior da página.
1. Para disponibilizar seu aplicativo do Facebook ao Azure AD B2C, selecione o seletor de status no canto superior direito da página e ligue-o para tornar o aplicativo público e, **em** seguida, selecione **O Modo de Comutação**.  Neste ponto, o Status deve mudar de **Desenvolvimento** para **Viver**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar o Facebook como um provedor de identidade

1. Faça login no [portal Azure](https://portal.azure.com/) como o administrador global do seu inquilino Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **o Facebook**.
1. Digite um **nome**. Por exemplo, *facebook*.
1. Para o **ID do cliente,** digite o ID do aplicativo do Facebook que você criou anteriormente.
1. Para o **segredo do Cliente,** digite o App Secret que você gravou.
1. Selecione **Salvar**.
