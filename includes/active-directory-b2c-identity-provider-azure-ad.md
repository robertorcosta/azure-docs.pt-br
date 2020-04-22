---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678060"
---
## <a name="register-an-azure-ad-app"></a>Registrar um aplicativo do Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está usando o diretório que contém seu inquilino Azure AD organizacional (por exemplo, contoso.com). Selecione o **filtro de assinatura Diretório +** no menu superior e escolha o diretório que contém o inquilino Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Digite um **nome** para sua inscrição. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção padrão de **Contas neste diretório organizacional apenas** para este aplicativo.
1. Para o **URI redirecionar**, aceite o valor da **Web**e digite a URL a seguir em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registrar**. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Selecione **Certificados & segredos**e selecione **Novo segredo do cliente**.
1. Digite uma **descrição** do segredo, selecione uma expiração e, em seguida, **selecione Adicionar**. Registre o **valor** do segredo para uso em uma etapa posterior.

### <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Se você quiser `family_name` obter `given_name` as e reivindicações do Azure AD, você pode configurar reivindicações opcionais para o seu aplicativo no portal Azure UI ou manifesto de aplicativo. Para obter mais informações, consulte [Como fornecer reclamações opcionais para o seu aplicativo Azure AD](/active-directory/develop/active-directory-optional-claims.md).

1. Entre no [portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar,** selecione **registros do Aplicativo**.
1. Selecione o aplicativo para o aplicativo que deseja configurar reivindicações opcionais na lista.
1. Na seção **Gerenciar,** selecione **Configuração Token**.
1. Selecione **Adicionar reivindicação opcional**.
1. Para o **tipo Token,** **selecione ID**.
1. Selecione as reivindicações `family_name` `given_name`opcionais a serem adicionadas e .
1. Clique em **Adicionar**.