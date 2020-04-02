---
title: Configurar o Microsoft authentication
description: Saiba como configurar a autenticação da Conta Microsoft como provedor de identidade para o aplicativo App Service ou Azure Functions.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: a78208ba592f86400e9b06b15d8a76923dda736f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519891"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Configure seu aplicativo App Service ou Azure Functions para usar o login da Conta Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Azure App Service ou funções do Azure para usar o AAD para suportar logins pessoais de conta da Microsoft.

> [!NOTE]
> Tanto contas pessoais da Microsoft quanto contas organizacionais usam o provedor de identidade AAD. Neste momento, não é possível configurar esse provedor de identidade para suportar ambos os tipos de logins.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrar seu aplicativo na conta da Microsoft

1. Acesse [**as inscrições do App**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) no portal Azure. Se necessário, faça login com sua conta microsoft.
1. Selecione **Novo registro**e digite um nome de aplicativo.
1. Em **tipos de conta suportados,** selecione Contas em qualquer diretório organizacional **(qualquer diretório Azure AD - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**
1. Em **Redirecionar URIs,** selecione `https://<app-domain-name>/.auth/login/aad/callback` **Web**e, em seguida, digite . Substitua o * \<nome de domínio do aplicativo>* pelo nome de domínio do seu aplicativo.  Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de usar o esquema HTTPS na URL.

1. Selecione **Registrar**.
1. Copie o **ID do aplicativo (Cliente).** Você precisará dela mais tarde.
1. No painel esquerdo, selecione **Certificados & segredos** > **Novo segredo do cliente**. Digite uma descrição, selecione a duração da validade e **selecione Adicionar**.
1. Copie o valor que aparece na página **Certificados & segredos.** Depois de sair da página, ela não será exibida novamente.

    > [!IMPORTANT]
    > O valor secreto do cliente (senha) é uma importante credencial de segurança. Não compartilhe a senha com ninguém nem distribua-a em um aplicativo cliente.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Adicionar informações da conta da Microsoft ao seu aplicativo de Serviço de Aplicativo

1. Vá para sua aplicação no [portal Azure].
1. Selecione **Configurações** > **Autenticação / Autorização**e certifique-se de que a **autenticação do serviço** do aplicativo está em **ação**.
1. Em **Provedores de Autenticação,** selecione **O Diretório Ativo do Azure**. Selecione **o modo Avançado** em **Gestão**. Cole no ID de aplicação (cliente) e no sigilo do cliente que você obteve anteriormente. Use **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** para o campo Url do **emissor.**
1. Selecione **OK**.

   O App Service fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e às APIs. Você deve autorizar os usuários no código do aplicativo.

1. (Opcional) Para restringir o acesso aos usuários da conta Microsoft, defina ação a ser tomada quando a **solicitação não for autenticada** para **fazer login com o Azure Active Directory**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para usar AAD para autenticação. Observe que, como você configurou sua **Url do Emissor** para usar o inquilino da Conta Microsoft, apenas acccounts pessoais serão autenticados com sucesso.

   > [!CAUTION]
   > Restringir o acesso desta forma se aplica a todas as chamadas para o seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial disponível publicamente, como em muitos aplicativos de página única. Para tais aplicativos, **permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
