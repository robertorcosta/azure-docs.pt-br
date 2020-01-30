---
title: Configurar a inscrição e a entrada com uma conta do Facebook
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Facebook em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: db6421167442e8aa3f0e7994dda35a8a5e475eb7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847570"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Facebook usando o Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como um [provedor de identidade](authorization-code-flow.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Facebook, poderá se inscrever em [https://www.facebook.com/](https://www.facebook.com/).

1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
1. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para fazer isso **, selecione introdução** no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
1. Selecione **meus aplicativos** e **criar aplicativo**.
1. Insira um **Nome de Exibição** e um **Email de Contato** válido.
1. Selecione **criar ID do aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
1. Escolha **Configurações** > **Básicas**.
1. Escolha uma **Categoria**, por exemplo, `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado para o Azure AD B2C.
1. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
1. Na **URL do Site**, insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu locatário. Insira uma URL para a **URL da Política de Privacidade**, por exemplo `http://www.contoso.com`. A URL da política é uma página que você mantém para fornecer informações de privacidade para o seu aplicativo.
1. Selecione **Salvar alterações**.
1. Na parte superior da página, copie o valor de **ID do Aplicativo**.
1. Selecione **Mostrar** e copie o valor do **segredo do aplicativo**. Você usará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
1. Selecione o sinal de adição ao lado de **produtos**e, em seguida, selecione **Configurar** em **logon do Facebook**.
1. Em **logon do Facebook**, selecione **configurações**.
1. Em **URIs de Redirecionamento do OAuth Válidos**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu locatário. Selecione **salvar alterações** na parte inferior da página.
1. Para disponibilizar seu aplicativo do Facebook para Azure AD B2C, selecione o seletor de status na parte superior direita da página e **ative-o** para tornar o aplicativo público e selecione **modo de comutação**.  Neste ponto, o Status deverá mudar de **Desenvolvimento** para **Ativo**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar o Facebook como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Facebook**.
1. Insira um **Nome**. Por exemplo, *Facebook*.
1. Para a **ID do cliente**, insira a ID do aplicativo do Facebook que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do aplicativo que você registrou.
1. Clique em **Salvar**.
