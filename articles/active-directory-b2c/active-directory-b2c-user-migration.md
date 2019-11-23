---
title: User migration approaches in Azure Active Directory B2C
description: Discusses both core and advanced concepts on user migration using the Azure AD Graph API, and optionally using Azure AD B2C custom policies.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9c01e22cfa96321994c16df6b61a52ebd4137549
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322926"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrate users to Azure Active Directory B2C

When you migrate your identity provider to Azure Active Directory B2C (Azure AD B2C), you might also need to migrate the user accounts. Este artigo explica como migrar as contas de usuário existentes de qualquer provedor de identidade para o Azure AD B2C. The article is not meant to be prescriptive, but rather, it describes a few scenarios. O desenvolvedor é responsável por escolher o mais adequado.

## <a name="user-migration-flows"></a>Fluxos de migração do usuário

With Azure AD B2C, you can migrate users through the [Azure AD Graph API][B2C-GraphQuickStart]. O processo de migração do usuário se enquadra em dois fluxos:

- **Pré-migração**: este fluxo se aplica quando você precisar limpar o acesso às credenciais do usuário (nome de usuário e senha) ou quando as credenciais forem criptografadas, mas você pode descriptografá-las. O processo de pré-migração envolve a leitura dos usuários do provedor de identidade antigo e a criação de novas contas no diretório do Azure AD B2C.

- **Pré-migração e redefinição de senha**: esse fluxo se aplica quando a senha do usuário não está acessível. Por exemplo:
  - A senha é armazenada no formato HASH.
  - A senha é armazenada em um provedor de identidade que você não pode acessar. Seu provedor de identidade antigo valida a credencial do usuário chamando um serviço Web.

Em ambos os fluxos, primeiro, você executa o processo de pré-migração, lê os usuários do seu provedor de identidade antigo e cria novas contas no diretório do Azure AD B2C. If you don't have the password, you create the account by using a password that's generated randomly. Em seguida, peça ao usuário para alterar a senha ou, quando o usuário fizer logon pela primeira vez, o Azure AD B2C pedirá ao usuário para redefini-la.

## <a name="password-policy"></a>Política de senha

A política de senha do Azure AD B2C (para contas locais) se baseia na política do Azure AD. The Azure AD B2C sign-up or sign-in and password reset policies use the "strong" password strength and don't expire any passwords. Para obter mais informações, consulte [Política de senha do Azure AD][AD-PasswordPolicies].

Se as contas que você deseja migrar usarem um nível de segurança de senha mais fraco do que o [nível de segurança de senha forte imposto pelo Azure AD B2C][AD-PasswordPolicies], você poderá desabilitar o requisito de senha forte. Para alterar a política de senha padrão, defina a propriedade `passwordPolicies` como `DisableStrongPassword`. Por exemplo, você pode modificar a solicitação de criação de usuário da seguinte maneira:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Etapa 1: usar a API do Graph do Microsoft Azure Active Directory para migrar usuários

Você cria a conta de usuário do Azure AD B2C por meio da API do Graph (com a senha ou com uma senha aleatória). Esta seção descreve o processo de como criar contas de usuário no diretório do Azure AD B2C usando a API do Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Etapa 1.1: registrar seu aplicativo em seu locatário

Para se comunicar com a API do Graph, primeiro, você precisa ter uma conta de serviço com privilégios administrativos. In Azure AD, you register an application and enable write access to the directory. The application credentials are the **Application ID** and **Application Secret**. O aplicativo atua em nome próprio e não como usuário, para chamar a API do Graph.

First, register an application that you can use for management tasks like user migration.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Step 1.2: Grant administrative permission to your application

Next, grant the application the Azure AD Graph API permissions required for writing to the directory.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Step 1.3: Create the application secret

Create a client secret (key) for use by the user migration application that you configure in a later step.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Now you have an application with permissions to create, read, and update users in your Azure AD B2C tenant.

### <a name="step-14-optional-environment-cleanup"></a>Etapa 1.4: (Opcional) limpeza de ambiente

The *Read and write directory data* permission does *not* include the right to delete users. Para permitir que seu aplicativo exclua os usuários (para limpar o ambiente), você deve executar uma etapa extra, que envolve a execução do PowerShell para definir permissões de Administrador da Conta de Usuário. Caso contrário, você pode pular para a próxima seção.

> [!IMPORTANT]
> Você precisa usar uma conta de administrador de locatário B2C que seja *local* para o locatário B2C. The account name syntax is *admin\@contosob2c.onmicrosoft.com*.

In this PowerShell script, which requires the [Azure AD PowerShell V2 module][AD-Powershell], do the following:

1. Conecte-se ao seu serviço online. Para fazer isso, execute o cmdlet `Connect-AzureAD` no prompt de comando do Windows PowerShell e forneça suas credenciais.

1. Use a **ID do Aplicativo** para atribuir o aplicativo à função de administrador da conta de usuário. Essas funções têm identificadores bem conhecidos, então, tudo o que você precisa fazer é inserir sua **ID de Aplicativo** no script.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Altere o valor `$AppId` pela **ID do Aplicativo** do Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Etapa 2: Exemplo de aplicativo pré-migração

You can find the pre-migration code sample in the community-maintained `azure-ad-b2c/user-migration` GitHub repository:

[azure-ad-b2c/user-migration/pre-migration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Etapa 2.1: editar o arquivo de dados de migração

O aplicativo de exemplo usa um arquivo JSON que contém dados de usuários fictícios. Após executar com êxito a amostra, você pode alterar o código para consumir os dados de seu próprio banco de dados. Ou você pode exportar o perfil do usuário para um arquivo JSON e, em seguida, definir o aplicativo para usar esse arquivo.

Para editar o arquivo JSON, abra a solução do Visual Studio `AADB2C.UserMigration.sln`. No projeto `AADB2C.UserMigration`, abra o arquivo `UsersData.json`.

![Portion of UsersData.json file showing JSON blocks of two users](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Como você pode ver, o arquivo contém uma lista de entidades do usuário. Cada entidade de usuário tem as seguintes propriedades:

- email
- displayName
- firstName
- lastName
- senha (pode estar vazia)

> [!NOTE]
> No tempo de compilação, o Visual Studio copia o arquivo para o diretório `bin`.

### <a name="step-22-configure-the-application-settings"></a>Etapa 2.2: definir as configurações do aplicativo

No projeto `AADB2C.UserMigration`, abra o arquivo *App.config*. Substitua as três configurações do aplicativo a seguir pelos seus próprios valores:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - O uso de uma cadeia de conexão de tabela do Azure é descrito posteriormente nas próximas seções.
> - O nome do locatário do B2C é o domínio que você inseriu durante a criação do locatário e é exibido no portal do Azure. O nome do locatário geralmente termina com o sufixo *.onmicrosoft.com* (por exemplo, *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Etapa 2.3: executar o processo pré-migração

Clique com o botão direito do mouse na solução `AADB2C.UserMigration` e, em seguida, recompile o exemplo. Se tiver êxito, agora deverá você ter um arquivo executável `UserMigration.exe` localizado em `AADB2C.UserMigration\bin\Debug\net461`. Para executar o processo de migração, use um dos seguintes parâmetros de linha de comando:

- Para **migrar usuários com senha**, use o comando `UserMigration.exe 1`.

- Para **migrar usuários com senha aleatória**, use o comando `UserMigration.exe 2`. Essa operação também cria uma entidade de tabela do Azure. Mais tarde, você configura a política para chamar o serviço de API REST. O serviço usa uma tabela do Azure para controlar e gerenciar o processo de migração.

![Command Prompt window showing output of UserMigration.exe command](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Etapa 2.4: verificar o processo pré-migração

Para validar a migração, use um dos dois métodos a seguir:

- Para pesquisar um usuário por nome de exibição, use o portal do Azure:

   1. Open **Azure AD B2C**, and then select **Users**.
   1. Na caixa de pesquisa, digite o nome de exibição do usuário e, em seguida, visualize o perfil do usuário.

- To retrieve a user by sign-in email address, use the sample application:

   1. Execute o comando a seguir:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Você também pode recuperar um usuário por nome de exibição usando o comando a seguir: `UserMigration.exe 4 "<Display name>"`.

   1. Abra o arquivo UserProfile.json em um editor JSON para ver as informações do usuário.

      ![UserProfile.json file open in the Visual Studio Code editor](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Etapa 2.5: (Opcional) limpeza de ambiente

Se deseja limpar seu locatário do Azure AD e remover os usuários do diretório do Azure AD, execute o comando `UserMigration.exe 5`.

> [!NOTE]
> * Para limpar o seu locatário, configure as permissões de Administrador da Conta de Usuário para seu aplicativo.
> * O aplicativo de migração de exemplo limpa todos os usuários listados no arquivo JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Etapa 2.6: entrar com usuários migrados (com senha)

Depois de executar o processo de pré-migração com as senhas dos usuários, as contas estão prontas para uso e os usuários podem entrar no seu aplicativo usando o Azure AD B2C. Se você não tiver acesso às senhas dos usuários, continue para a próxima seção.

## <a name="step-3-help-users-reset-their-password"></a>Etapa 3: ajudar os usuários a redefinir sua senha

Se você migrar usuários com uma senha aleatória, eles precisarão redefinir a senha. Para ajudá-los a redefinir a senha, envie um email de boas-vindo com um link para redefinir a senha.

To get the link to your password reset policy, follow these steps. This procedure assumes you've previously created a password reset [custom policy](active-directory-b2c-get-started-custom.md).

1. Select the directory containing your Azure AD B2C tenant by using the **Directory + subscription** filter in the upper-right section of the [Azure portal](https://portal.azure.com).
1. Select **Azure AD B2C** in the left-hand menu (or from within **All services**).
1. Under **Policies**, select **Identity Experience Framework**.
1. Select your password reset policy. For example, *B2C_1A_PasswordReset*.
1. Select your application in the **Select application** drop-down.

    > [!NOTE]
    > **Run now** requires at least one application to be registered in your tenant. To learn how to register applications, see [Tutorial: Register an application in Azure Active Directory B2C][B2C-AppRegister].

1. Copy the URL shown in the **Run now endpoint** text box, and then send it to your users.

    ![Password reset policy page with Run now endpoint highlighted](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Etapa 4: [Opcional] alterar sua política para verificar e definir o status de migração do usuário

> [!NOTE]
> Para verificar e alterar o status de migração do usuário, você deve usar uma política personalizada. The set-up instructions from [Get started with custom policies][B2C-GetStartedCustom] must be completed.

Quando os usuários tentarem entrar sem redefinir a senha primeiro, sua política deve retornar uma mensagem de erro amigável. Por exemplo:

> *Your password has expired. To reset it, select the Reset Password link.*

Esta etapa opcional requer o uso d e políticas personalizadas do Azure AD B2C conforme descrito no artigo [Introdução a políticas personalizadas][B2C-GetStartedCustom].

Nesta seção, você deve alterar a política para verificar o status de migração de usuário na entrada. Se o usuário não alterou a senha, retorne uma mensagem de erro HTTP 409 pedindo para o usuário selecionar o link **Esqueceu sua senha?** .

Para acompanhar a alteração da senha, você deve usar a tabela do Azure. Quando você executa o processo de pré-migração com parâmetro de linha de comando `2`, cria a entidade de usuário em uma tabela do Azure. O serviço faz o seguinte:

- Ao entrar, a política do Azure AD B2C invoca o serviço RESTful de migração, enviando uma mensagem de email como uma declaração de entrada. O serviço pesquisa o endereço de email na tabela do Azure. Se o endereço existir, o serviço gerará uma mensagem de erro: *Você deve alterar a senha*.

- Após o usuário alterar a senha com êxito, remova a entidade da tabela do Azure.

> [!NOTE]
> Usamos uma tabela do Azure para simplificar o exemplo. Você pode armazenar o status de migração em qualquer banco de dados ou como uma propriedade personalizada na conta do Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: atualizar a configuração de aplicativo

1. Para testar a demonstração da API RESTful, abra `AADB2C.UserMigration.sln` no Visual Studio.
1. In the `AADB2C.UserMigration.API` project, open the *Web.config* file. Substitua a configuração por aquela configurada na [Etapa 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Etapa 4.2: implantar o aplicativo Web no Serviço de Aplicativo do Azure

No Gerenciador de Soluções, clique com o botão direito do mouse em `AADB2C.UserMigration.API` e selecione “Publicar...”. Siga as instruções para publicar no Serviço de Aplicativo do Azure. Para obter mais informações, consulte: [Implantar seu aplicativo no Serviço de Aplicativo do Azure][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Etapa 4.3: adicionar perfil técnico e validação de perfil técnico à sua política

1. No Gerenciador de Soluções, expanda "Itens de solução" e abra o arquivo de política *TrustFrameworkExtensions.xml*.
1. Altere os campos `TenantId`, `PublicPolicyUri` e `<TenantId>` de `yourtenant.onmicrosoft.com` para o nome do seu locatário.
1. Under the `<TechnicalProfile Id="login-NonInteractive">` element, replace all instances of `ProxyIdentityExperienceFrameworkAppId` and `IdentityExperienceFrameworkAppId` with the Application IDs configured in [Getting started with custom policies][B2C-GetStartedCustom].
1. No nó `<ClaimsProviders>`, encontre o seguinte snippet de código XML. Altere o valor de `ServiceUrl` para apontar para a URL do Serviço de Aplicativo do Azure.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

O perfil técnico precedente define uma declaração de entrada: `signInName` (enviar como email). Ao entrar, a declaração é enviada para seu ponto de extremidade RESTful.

After you define the technical profile for your RESTful API, configure the existing `SelfAsserted-LocalAccountSignin-Email` technical profile to additionally call your REST API technical profile by overriding it within your *TrustFrameworkExtensions.xml* file:

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Then, change the `Id` of the `LocalAccountSignIn` technical profile to `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Etapa 4.4: carregar a política em seu locatário

1. No [Portal do Azure][Portal], mude para o [contexto do locatário do Azure AD B2C][B2C-NavContext] e, em seguida, selecione **Azure AD B2C**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Todas as Políticas**.
1. Selecione **Carregar Política**.
1. Marque a caixa de seleção **Substituir a política caso ela exista**.
1. Carregue o arquivo *TrustFrameworkExtensions.xml* e certifique-se de que a validação dele seja aprovada.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Etapa 4.5: testar a política personalizada usando Executar Agora

1. Select **Azure AD B2C**, and then select **Identity Experience Framework**.
1. Abra *B2C_1A_signup_signin*, a política personalizada da RP (terceira parte confiável) carregada e selecione **Executar agora**.
1. Enter the credentials of one of the migrated users, and then select **Sign In**. A API REST deve emitir a seguinte mensagem de erro:

    ![Sign-in Sign-up page showing the change password error message](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Etapa 4.6: (Opcional) solução de problemas na sua API REST

Você pode monitorar e exibir informações de log quase em tempo real.

1. No menu de configurações do seu aplicativo RESTful, em **Monitoramento**, selecione **Logs de diagnóstico**.
1. Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**.
1. Defina o **Nível** como **Detalhado**.
1. Selecione **Salvar**

    ![Diagnostics logs configuration page in Azure portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. No menu **Configurações**, selecione **Fluxo de Log**.
1. Verifique a saída da API RESTful.

> [!IMPORTANT]
> Use os logs de diagnóstico somente durante o desenvolvimento e teste. A saída API RESTful pode conter informações confidenciais que não devem ser expostas na produção.

## <a name="optional-download-the-complete-policy-files"></a>(Opcional) Baixar os arquivos da política completa

After you complete the [Get started with custom policies][B2C-GetStartedCustom] walk-through, we recommend that you build your scenario by using your own custom policy files. For your reference, we have provided [sample policy files][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
