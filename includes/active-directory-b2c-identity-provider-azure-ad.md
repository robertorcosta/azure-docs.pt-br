---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82925448"
---
## <a name="register-an-azure-ad-app"></a>Registrar um aplicativo do Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário organizacional do Azure AD (por exemplo, contoso.com). Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **nome** para seu aplicativo. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção padrão de **contas neste diretório organizacional somente** para este aplicativo.
1. Para o **URI de redirecionamento**, aceite o valor de **Web**e insira a URL a seguir em todas as letras `your-B2C-tenant-name` minúsculas, onde é substituído pelo nome do seu locatário de Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registrar**. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Selecione **certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo, selecione uma expiração e, em seguida, selecione **Adicionar**. Registre o **valor** do segredo para uso em uma etapa posterior.

### <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Se você quiser obter as `family_name` declarações e `given_name` do Azure AD, poderá configurar declarações opcionais para seu aplicativo na interface do usuário do portal do Azure ou no manifesto do aplicativo. Para obter mais informações, consulte [como fornecer declarações opcionais para seu aplicativo do Azure ad](/azure/active-directory/develop/active-directory-optional-claims).

1. Entre no [portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **gerenciar** , selecione **registros de aplicativo**.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.
1. Na seção **gerenciar** , selecione **configuração de token**.
1. Selecione **Adicionar declaração opcional**.
1. Para o **tipo de token**, selecione **ID**.
1. Selecione as declarações opcionais a serem `family_name` adicionadas e `given_name`.
1. Clique em **Adicionar**.