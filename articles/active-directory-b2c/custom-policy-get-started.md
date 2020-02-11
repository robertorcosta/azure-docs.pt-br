---
title: Introdução às políticas personalizadas-Azure Active Directory B2C
description: Saiba como começar a usar políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5a0e5846dd541e4997c271aee180b3790efa16e9
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114030"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introdução às políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Políticas personalizadas](custom-policy-overview.md) são arquivos de configuração que definem o comportamento do seu locatário Azure Active Directory B2C (Azure ad B2C). Neste artigo, você criará uma política personalizada que dá suporte para inscrição ou entrada de conta local usando um endereço de email e uma senha. Você também pode preparar o ambiente para adicionar provedores de identidade.

## <a name="prerequisites"></a>Prerequisites

- Se você ainda não tiver um, [crie um locatário Azure ad B2C](tutorial-create-tenant.md) que esteja vinculado à sua assinatura do Azure.
- [Registre seu aplicativo](tutorial-register-applications.md) no locatário que você criou para que ele possa se comunicar com Azure ad B2C.
- Conclua as etapas em [Configurar inscrição e entrar com uma conta do Facebook](identity-provider-facebook.md) para configurar um aplicativo do Facebook.

## <a name="add-signing-and-encryption-keys"></a>Adicionar chaves de criptografia e de assinatura

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **diretório + assinatura** na barra de ferramentas do portal e selecione o diretório que contém seu locatário Azure ad B2C.
1. Na portal do Azure, procure e selecione **Azure ad B2C**.
1. Na página Visão geral, em **políticas**, selecione **estrutura de experiência de identidade**.

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

Adicione o [segredo](identity-provider-facebook.md) do aplicativo do Facebook como uma chave de política. Você pode usar o segredo do aplicativo criado como parte dos pré-requisitos deste artigo.

1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Para **Nome**, insira `FacebookSecret`. O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Em **segredo**, insira o *segredo* do aplicativo do Facebook em Developers.Facebook.com. Esse valor é o segredo, não a ID do aplicativo.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

## <a name="register-identity-experience-framework-applications"></a>Registrar aplicativos do Identity Experience Framework

Azure AD B2C exige que você registre dois aplicativos que ele usa para inscrever-se e conectar usuários com contas locais: *IdentityExperienceFramework*, uma API Web e *ProxyIdentityExperienceFramework*, um aplicativo nativo com permissão delegada para o aplicativo IdentityExperienceFramework. Os usuários podem se inscrever com um endereço de email ou nome de usuário e uma senha para acessar seus aplicativos registrados no locatário, que cria uma "conta local". As contas locais existem somente em seu locatário Azure AD B2C.

Você precisa registrar esses dois aplicativos em seu locatário do Azure AD B2C apenas uma vez.

### <a name="register-the-identityexperienceframework-application"></a>Registrar o aplicativo IdentityExperienceFramework

Para registrar um aplicativo em seu locatário do Azure AD B2C, você pode usar a experiência de **registros de aplicativo (herdada)** ou nossa nova experiência de registros de aplicativo unificada **(versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na portal do Azure, procure e selecione **Azure Active Directory**.
1. No menu de visão geral do **Azure Active Directory** , em **gerenciar**, selecione **registros de aplicativo (Herdado)** .
1. Selecione **Novo registro de aplicativo**.
1. Para **Nome**, insira `IdentityExperienceFramework`.
1. Para **Tipo de aplicativo**, escolha **Aplicativo Web/API**.
1. Para **URL de logon**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o nome de domínio de locatário do Azure AD B2C. Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).
1. Selecione **Criar**. Após a criação, copie a ID do aplicativo e salve-a para uso posterior.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Para **Nome**, insira `IdentityExperienceFramework`.
1. Em **tipos de conta com suporte**, selecione **contas somente neste diretório organizacional**.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o nome de domínio do seu locatário Azure ad B2C.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, exponha a API adicionando um escopo:

1. Em **Gerenciar**, selecione **Expor uma API**.
1. Selecione **Adicionar um escopo**e, em seguida, selecione **salvar e continuar** para aceitar o URI da ID do aplicativo padrão.
1. Insira os valores a seguir para criar um escopo que permita a execução da política personalizada em seu locatário Azure AD B2C:
    * **Nome do escopo**: `user_impersonation`
    * **Nome de exibição de consentimento do administrador**: `Access IdentityExperienceFramework`
    * **Descrição do consentimento do administrador:** `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selecione **Adicionar escopo**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrar o aplicativo ProxyIdentityExperienceFramework

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Em **registros de aplicativo (Herdado)** , selecione **novo registro de aplicativo**.
1. Para **Nome**, insira `ProxyIdentityExperienceFramework`.
1. Para **Tipo de aplicativo**, escolha **Nativo**.
1. Para **URI de redirecionamento**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o seu locatário do Azure AD B2C.
1. Selecione **Criar**. Após a criação, copie a ID do aplicativo e salve-a para uso posterior.
1. Selecione **configurações**, selecione **permissões necessárias**e, em seguida, selecione **Adicionar**.
1. Escolha **selecionar uma API**, procure e selecione **IdentityExperienceFramework**e, em seguida, clique em **selecionar**.
1. Marque a caixa de seleção ao lado de **Acessar IdentityExperienceFramework**, clique em **Selecionar** e, em seguida, clique em **Concluído**.
1. Selecione **conceder permissões**e, em seguida, confirme selecionando **Sim**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de Aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Escolha **Registros de Aplicativo (versão prévia)** e depois selecione **Novo Registro**.
1. Para **Nome**, insira `ProxyIdentityExperienceFramework`.
1. Em **tipos de conta com suporte**, selecione **contas somente neste diretório organizacional**.
1. Em **URI de Redirecionamento**, use a lista suspensa para selecionar **Cliente Público/Nativo (móvel e área de trabalho)** .
1. Para **URI de redirecionamento**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, em que `your-tenant-name` é o seu locatário do Azure AD B2C.
1. Em **Permissões**, marque a caixa de seleção *Dar consentimento do administrador às permissões OpenID e offline_access*.
1. Selecione **Registrar**.
1. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Em seguida, especifique que o aplicativo deve ser tratado como um cliente público:

1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Experimentar a nova experiência** (se mostrado).
1. Em **Configurações avançadas**, habilite **tratar aplicativo como um cliente público** (selecione **Sim**).
1. Clique em **Salvar**.

Agora, conceda permissões ao escopo da API que você expôs anteriormente no registro *IdentityExperienceFramework* :

1. Em **Gerenciar**, selecione **Permissões de API**.
1. Em **Permissões Configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **minhas APIs** e, em seguida, selecione o aplicativo **IdentityExperienceFramework** .
1. Em **permissão**, selecione o escopo de **user_impersonation** que você definiu anteriormente.
1. Selecione **Adicionar Permissões**. Conforme as instruções, aguarde alguns minutos antes de seguir para a próxima etapa.
1. Selecione **Fornecer o consentimento do administrador para (nome do seu locatário)** .
1. Selecione a conta de administrador conectada no momento ou entre com uma conta no seu locatário do Azure AD B2C que tenha recebido, pelo menos, a função *Administrador de aplicativos de nuvem*.
1. Selecione **Aceitar**.
1. Selecione **Atualizar** e, em seguida, verifique se "Concedido para..." aparece em **Status** para ambos os escopos. Pode levar alguns minutos para que as permissões sejam propagadas.

* * *

## <a name="custom-policy-starter-pack"></a>Pacote de início de política personalizada

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir perfis técnicos e percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas para ajudá-lo a ir rapidamente. Cada um desses pacotes de início contém o menor número de perfis técnicos e percursos do usuário necessários para atingir os cenários descritos:

- **LocalAccounts** – habilita o uso somente de contas locais.
- **SocialAccounts** – habilita o uso apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** – habilita o uso de contas locais e sociais.
- **SocialAndLocalAccountsWithMFA** -habilita opções de autenticação de multifator, locais e sociais.

Cada pacote de início contém:

- **Arquivo base** -poucas modificações são necessárias para a base. Exemplo: *TrustFrameworkBase. xml*
- **Arquivo de extensão** -esse arquivo é onde a maioria das alterações de configuração são feitas. Exemplo: *TrustFrameworkExtensions. xml*
- **Arquivos de terceira parte confiável** -arquivos específicos da tarefa chamados por seu aplicativo. Exemplos: *SignUpOrSignin. xml*, *ProfileEdit. xml*, *PasswordReset. xml*

Neste artigo, você edita os arquivos de política personalizada XML no pacote de início do **SocialAndLocalAccounts** . Se você precisar de um editor de XML, tente [Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.

### <a name="get-the-starter-pack"></a>Obter o pacote de início

Obtenha os pacotes de início de política personalizados do GitHub e, em seguida, atualize os arquivos XML no pacote de início do SocialAndLocalAccounts com seu nome de locatário Azure AD B2C.

1. [Baixe o arquivo. zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou clone o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Em todos os arquivos no diretório **SocialAndLocalAccounts** , substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário Azure ad B2C.

    Por exemplo, se o nome do seu locatário B2C for *contosotenant*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Adicionar IDs de aplicativo à política personalizada

Adicione as IDs de aplicativo ao arquivo de extensões *TrustFrameworkExtensions.xml*.

1. Abra `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** e localize o elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Substitua as duas instâncias de `IdentityExperienceFrameworkAppId` pela ID do aplicativo IdentityExperienceFramework que você criou anteriormente.
1. Substitua as duas instâncias de `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo ProxyIdentityExperienceFramework que você criou anteriormente.
1. Salve o arquivo.

## <a name="upload-the-policies"></a>Fazer upload das políticas

1. Selecione o item de menu **Identity Experience Framework** em seu locatário B2C no portal do Azure.
1. Selecione **carregar política personalizada**.
1. Nesta ordem, carregue os arquivos de política:
    1. *TrustFrameworkBase. xml*
    1. *TrustFrameworkExtensions. xml*
    1. *SignUpOrSignin. xml*
    1. *ProfileEdit. xml*
    1. *PasswordReset. xml*

À medida que você carrega os arquivos, o Azure adiciona o prefixo `B2C_1A_` a cada um.

> [!TIP]
> Se o seu editor de XML oferecer suporte à validação, valide os arquivos em relação ao esquema XML `TrustFrameworkPolicy_0.3.0.0.xsd` localizado no diretório raiz do pacote inicial. A validação de esquema XML identifica erros antes do upload.

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Em **políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Para **Selecionar aplicativo** na página Visão geral da política personalizada, selecione o aplicativo Web chamado *webapp1* que você registrou anteriormente.
1. Verifique se a **URL de resposta** está `https://jwt.ms`.
1. Selecione **Executar Agora**.
1. Inscreva-se usando um endereço de email.
1. Selecione **executar agora** novamente.
1. Entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="add-facebook-as-an-identity-provider"></a>Adicionar o Facebook como um provedor de identidade

1. No arquivo de `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** , substitua o valor de `client_id` pela ID do aplicativo do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário.
1. Em **políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Selecione **executar agora** e selecione Facebook para entrar com o Facebook e testar a política personalizada.

## <a name="next-steps"></a>Próximas etapas

Em seguida, tente adicionar Azure Active Directory (Azure AD) como um provedor de identidade. O arquivo base usado neste guia de introdução já contém parte do conteúdo de que você precisa para adicionar outros provedores de identidade, como o Azure AD.

Para obter informações sobre como configurar o Azure AD como e o provedor de identidade, consulte [Configurar inscrição e entrada com uma conta de Azure Active Directory usando Active Directory B2C políticas personalizadas](identity-provider-azure-ad-single-tenant-custom.md).
