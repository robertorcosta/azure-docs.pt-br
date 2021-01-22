---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: f94076f06fb13bae2a26e8ab6003d7574a2dacfd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98674212"
---
## <a name="configure-the-relying-party-policy"></a>Configurar a política de terceira parte confiável

A política de terceira parte confiável, por exemplo [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), especifica a jornada do usuário que Azure ad B2C será executado. Localize o elemento **DefaultUserJourney** na terceira parte [confiável](../articles/active-directory-b2c/relyingparty.md). Atualize o  **referenceid** para corresponder à ID do percurso do usuário, na qual você adicionou o provedor de identidade. 

No exemplo a seguir, para o percurso do `CustomSignUpOrSignIn` usuário, o **referenceid** é definido como `CustomSignUpOrSignIn` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Carregar a política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **carregar política personalizada** e, em seguida, carregue os dois arquivos de política que você alterou, na seguinte ordem: a política de extensão, por exemplo `TrustFrameworkExtensions.xml` , a política de terceira parte confiável, como `SignUpSignIn.xml` .

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin`
1. Para **aplicativo**, selecione um aplicativo Web que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

