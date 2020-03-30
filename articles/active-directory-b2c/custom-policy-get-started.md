---
title: Introdução às políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como começar com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc87628d8b47435012c3d20ec2e72ac186983555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189320"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introdução às políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Políticas personalizadas](custom-policy-overview.md) são arquivos de configuração que definem o comportamento do seu inquilino Azure Active Directory B2C (Azure AD B2C). Neste artigo, você criará uma política personalizada que dá suporte para inscrição ou entrada de conta local usando um endereço de email e uma senha. Você também pode preparar o ambiente para adicionar provedores de identidade.

## <a name="prerequisites"></a>Pré-requisitos

- Se você ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.
- [Registre seu aplicativo](tutorial-register-applications.md) no inquilino que você criou para que ele possa se comunicar com o Azure AD B2C.
- Complete as etapas em [Configurar o login e fazer login com uma conta do Facebook](identity-provider-facebook.md) para configurar um aplicativo do Facebook. Embora um aplicativo do Facebook não seja necessário para usar políticas personalizadas, ele é usado neste passo a passo para demonstrar a habilitação do login social em uma política personalizada.

## <a name="add-signing-and-encryption-keys"></a>Adicionar chaves de criptografia e de assinatura

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o inquilino Ad B2C do Azure.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Na página de visão geral, em **Políticas,** selecione **Identity Experience Framework**.

### <a name="create-the-signing-key"></a>Criar a chave de assinatura

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Generate`.
1. Em **Nome**, insira `TokenSigningKeyContainer`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Para **Tipo de chave**, selecione **RSA**.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

### <a name="create-the-encryption-key"></a>Criar a chave de criptografia

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Generate`.
1. Em **Nome**, insira `TokenEncryptionKeyContainer`. O prefixo `B2C_1A`_ pode ser adicionado automaticamente.
1. Para **Tipo de chave**, selecione **RSA**.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.

### <a name="create-the-facebook-key"></a>Criar a chave do Facebook

Adicione o App [Secret](identity-provider-facebook.md) do seu aplicativo do Facebook como uma chave de política. Você pode usar o App Secret do aplicativo que você criou como parte dos pré-requisitos deste artigo.

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Para **Nome**, insira `FacebookSecret`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Em **Segredo,** insira o *App Secret* do seu aplicativo do Facebook de developers.facebook.com. Este valor é o segredo, não o ID de aplicação.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos do Identity Experience Framework

O Azure AD B2C exige que você registre dois aplicativos que ele usa para se cadastrar e fazer login nos usuários com contas locais: *IdentityExperienceFramework*, uma API web e *ProxyIdentityExperienceFramework*, um aplicativo nativo com permissão delegada para o aplicativo IdentityExperienceFramework. Seus usuários podem se cadastrar com um endereço de e-mail ou nome de usuário e uma senha para acessar seus aplicativos registrados no inquilino, o que cria uma "conta local". As contas locais existem apenas no seu inquilino Azure AD B2C.

Você precisa registrar esses dois aplicativos no seu inquilino Azure AD B2C apenas uma vez.

### <a name="register-the-identityexperienceframework-application"></a>Registrar o aplicativo IdentityExperienceFramework

Para registrar um aplicativo no seu inquilino Azure AD B2C, você pode usar a experiência de registros do **Aplicativo (Legacy)** ou nossa nova experiência unificada **de registros de aplicativos (Preview).** [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Faça login no [portal Azure](https://portal.azure.com).
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. No menu de visão geral **do Azure Active Directory,** em **Gerenciar,** selecione **Registros de aplicativos (Legacy)**.
1. Selecione **Novo registro de aplicativo**.
1. Para **Nome**, insira `IdentityExperienceFramework`.
1. Para **Tipo de aplicativo**, escolha **Aplicativo Web/API**.
1. Para **URL de logon**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o nome de domínio de locatário do Azure AD B2C. Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).
1. Selecione **Criar**. Após a criação, copie a ID do aplicativo e salve-a para uso posterior.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Para **Nome**, insira `IdentityExperienceFramework`.
1. Em **tipos de conta suportados,** selecione Contas somente neste diretório **organizacional**.
1. Em **Redirecionar URI,** selecione **Web**e, em seguida, insira, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`onde `your-tenant-name` está o nome de domínio do inquilino Azure AD B2C.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, exponha a API adicionando um escopo:

1. Em **Gerenciar**, selecione **Expor uma API**.
1. Selecione **Adicionar um escopo,** em seguida, **selecione Salvar e continuar** a aceitar o URI de id de aplicativo padrão.
1. Digite os seguintes valores para criar um escopo que permite a execução de políticas personalizadas no seu inquilino AD B2C do Azure:
    * **Nome do escopo:**`user_impersonation`
    * **Nome de exibição de consentimento de admin:**`Access IdentityExperienceFramework`
    * **Descrição do consentimento do admin:**`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selecione **Adicionar escopo**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrar o aplicativo ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Aplicativos](#tab/applications/)

1. Em **registros de aplicativos (Legado)**, selecione Novo registro de **inscrição**.
1. Para **Nome**, insira `ProxyIdentityExperienceFramework`.
1. Para **Tipo de aplicativo**, escolha **Nativo**.
1. Para **URI de redirecionamento**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o seu locatário do Azure AD B2C.
1. Selecione **Criar**. Após a criação, copie a ID do aplicativo e salve-a para uso posterior.
1. Selecione **Configurações,** selecione **as permissões necessárias**e **selecione Adicionar**.
1. Escolha **Selecionar uma API,** procurar e selecionar **IdentityExperienceFramework**e, em seguida, clique em **Selecionar**.
1. Marque a caixa de seleção ao lado de **Acessar IdentityExperienceFramework**, clique em **Selecionar** e, em seguida, clique em **Concluído**.
1. Selecione **as permissões de concessão**e confirme selecionando **Sim**.

#### <a name="app-registrations-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Para **Nome**, insira `ProxyIdentityExperienceFramework`.
1. Em **tipos de conta suportados,** selecione Contas somente neste diretório **organizacional**.
1. Em **URI de Redirecionamento**, use a lista suspensa para selecionar **Cliente Público/Nativo (móvel e área de trabalho)**.
1. Para **URI de redirecionamento**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o seu locatário do Azure AD B2C.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, especifique que o aplicativo deve ser tratado como um cliente público:

1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **configurações avançadas,** habilite **o aplicativo Treat como cliente público** (selecione **Sim**).
1. Selecione **Salvar**.

Agora, conceda permissões ao escopo de API que você expôs anteriormente no registro *IdentityExperienceFramework:*

1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **Minhas APIs** e selecione o aplicativo **IdentityExperienceFramework.**
1. Em **Permission**, selecione o **escopo user_impersonation** que você definiu anteriormente.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)**.
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar** e, em seguida, verifique se "Concedido para..." aparece em **Status** para ambos os escopos. Pode levar alguns minutos para que as permissões sejam propagadas.

* * *

## <a name="custom-policy-starter-pack"></a>Pacote de inicialização de políticas personalizadas

As políticas personalizadas são um conjunto de arquivos XML que você carrega para o seu inquilino Azure AD B2C para definir perfis técnicos e viagens de usuário. Nós fornecemos pacotes iniciais com várias políticas pré-construídas para fazê-lo ir rapidamente. Cada um desses pacotes iniciais contém o menor número de perfis técnicos e jornadas de usuário necessárias para alcançar os cenários descritos:

- **Contas locais** - Permite o uso apenas de contas locais.
- **Contas Sociais** - Permite o uso apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** - Permite o uso de contas locais e sociais.
- **SocialAndLocalAccountsWithMFA** - Permite opções de autenticação social, local e multifatorial.

Cada pacote de início contém:

- **Arquivo base** - Poucas modificações são necessárias para a base. Exemplo: *TrustFrameworkBase.xml*
- **Arquivo de extensão** - Este arquivo é onde a maioria das alterações de configuração são feitas. Exemplo: *TrustFrameworkExtensions.xml*
- **Contando arquivos partidários** - Arquivos específicos de tarefas chamados pelo seu aplicativo. Exemplos: *SignUpOrSignin.xml*, *ProfileEdit.xml,* *PasswordReset.xml*

Neste artigo, você edita os arquivos de diretiva personalizado séxion no pacote inicial **SocialAndLocalAccounts.** Se você precisar de um editor XML, experimente [o Visual Studio Code](https://code.visualstudio.com/download), um editor leve entre plataformas.

### <a name="get-the-starter-pack"></a>Pegue o pacote de partida

Obtenha os pacotes de inicialização de políticas personalizados do GitHub e atualize os arquivos XML no pacote inicial SocialAndLocalAccounts com o nome de inquilino AD B2C do Azure.

1. [Baixe o arquivo .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clone o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Em todos os arquivos do diretório **SocialAndLocalAccounts,** substitua a string `yourtenant` pelo nome do seu inquilino Azure AD B2C.

    Por exemplo, se o nome do seu inquilino B2C `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`for *contosotenant*, todas as instâncias de se tornar .

### <a name="add-application-ids-to-the-custom-policy"></a>Adicionar IDs de aplicativo à política personalizada

Adicione as IDs de aplicativo ao arquivo de extensões *TrustFrameworkExtensions.xml*.

1. Abra `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** e encontre `<TechnicalProfile Id="login-NonInteractive">`o elemento.
1. Substitua ambas `IdentityExperienceFrameworkAppId` as instâncias com o ID do aplicativo IdentityExperienceFramework que você criou anteriormente.
1. Substitua ambas `ProxyIdentityExperienceFrameworkAppId` as instâncias com o ID do aplicativo ProxyIdentityExperienceFramework que você criou anteriormente.
1. Salve o arquivo.

## <a name="upload-the-policies"></a>Fazer upload das políticas

1. Selecione o item do menu **Identity Experience Framework** no seu inquilino B2C no portal Azure.
1. Selecione **Enviar política personalizada**.
1. Nesta ordem, faça upload dos arquivos de diretiva:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *Inscreva-seOuOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Ao carregar os arquivos, o Azure adiciona o prefixo `B2C_1A_` a cada um.

> [!TIP]
> Se o seu editor XML suportar a `TrustFrameworkPolicy_0.3.0.0.xsd` validação, valide os arquivos contra o esquema XML que está localizado no diretório raiz do pacote inicial. A validação de esquema XML identifica erros antes do upload.

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Em **políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Para selecionar o **aplicativo** na página de visão geral da diretiva personalizada, selecione o aplicativo web chamado *webapp1* que você registrou anteriormente.
1. Certifique-se de que `https://jwt.ms`a URL de **resposta** é .
1. Selecione **Executar Agora**.
1. Inscreva-se usando um endereço de e-mail.
1. Selecione **Executar agora** novamente.
1. Entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade

Como mencionado nos [pré-requisitos, o](#prerequisites)Facebook *não* é necessário para usar políticas personalizadas, mas é usado aqui para demonstrar como você pode habilitar o login social federado em uma política personalizada.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** No arquivo, substitua `client_id` o valor do ID do aplicativo do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário.
1. Em **políticas personalizadas,** selecione **B2C_1A_signup_signin**.
1. Selecione **Executar agora** e selecione o Facebook para fazer login com o Facebook e testar a política personalizada.

## <a name="next-steps"></a>Próximas etapas

Em seguida, tente adicionar o Azure Active Directory (Azure AD) como provedor de identidade. O arquivo base usado neste guia de início já contém alguns dos conteúdos necessários para adicionar outros provedores de identidade, como o Azure AD.

Para obter informações sobre como configurar o Azure AD como provedor de identidade, consulte [Configurar a inscrição e fazer login com uma conta do Azure Active Directory usando políticas personalizadas do Active Directory B2C](identity-provider-azure-ad-single-tenant-custom.md).
