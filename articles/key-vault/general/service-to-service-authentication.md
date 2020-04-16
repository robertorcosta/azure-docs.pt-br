---
title: Autenticação serviço a serviço no Azure Key Vault usando o .NET
description: Use a biblioteca Microsoft.Azure.Services.AppAuthentication para se autenticar no Azure Key Vault usando o .NET.
keywords: credenciais de local de autenticação do Azure Key Vault
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: d6ac5961cbecf4e81c0b6bcc25c39aad42b18416
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429831"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticação serviço a serviço no Azure Key Vault usando o .NET

Para autenticar o Azure Key Vault, você precisa de uma credencial do Azure Active Directory (Azure AD), um segredo compartilhado ou um certificado.

Gerenciar tais credenciais pode ser difícil. É tentador empacotar credenciais em um aplicativo, incluindo-as em arquivos de origem ou configuração. O `Microsoft.Azure.Services.AppAuthentication` para a biblioteca .NET simplifica esse problema. Ele usa as credenciais do desenvolvedor para autenticação durante o desenvolvimento local. Quando a solução é implantada posteriormente no Azure, a biblioteca alterna automaticamente para as credenciais do aplicativo. O uso de credenciais de desenvolvedor durante o desenvolvimento local é mais seguro porque você não precisa criar credenciais azure AD ou compartilhar credenciais entre desenvolvedores.

A `Microsoft.Azure.Services.AppAuthentication` biblioteca gerencia a autenticação automaticamente, o que, por sua vez, permite que você se concentre em sua solução, em vez de suas credenciais. Ele suporta o desenvolvimento local com o Microsoft Visual Studio, Azure CLI ou Azure AD Integrated Authentication. Quando implantada em um recurso do Azure que oferece suporte a uma identidade gerenciada, a biblioteca usa automaticamente as [identidades gerenciadas para recursos do Azure](../../active-directory/msi-overview.md). Nenhuma alteração de configuração ou de código é necessária. A biblioteca também suporta o uso direto das [credenciais](../../azure-resource-manager/resource-group-authenticate-service-principal.md) de cliente SD do Azure quando uma identidade gerenciada não está disponível ou quando o contexto de segurança do desenvolvedor não pode ser determinado durante o desenvolvimento local.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- A extensão de Autenticação de Aplicativo para Visual Studio, disponível como uma extensão separada para o Visual Studio 2017 Update 5 e empacotado com o produto na Atualização 6 e posterior. Com a Atualização 6 ou posterior, você pode verificar a instalação da extensão de Autenticação de Aplicativos selecionando as ferramentas de desenvolvimento do Azure dentro do instalador do Visual Studio.

## <a name="using-the-library"></a>Usando a biblioteca

Para aplicativos .NET, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do pacote `Microsoft.Azure.Services.AppAuthentication`. Estas são algumas dicas para começar:

1. Selecione **ferramentas** > **NuGet Package Manager** > **Gerencie pacotes nuget para solução para** adicionar referências aos pacotes [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet ao seu projeto.

1. Adicione os códigos a seguir:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

A classe `AzureServiceTokenProvider` armazena em cache o token na memória e recupera-o do Azure AD logo antes da expiração. Então, você não precisa mais verificar a `GetAccessTokenAsync` expiração antes de chamar o método. Basta chamar o método quando desejar usar o token.

O método `GetAccessTokenAsync` exige um identificador de recurso. Para saber mais sobre os serviços do Microsoft Azure, consulte [O que é identidades gerenciadas para os recursos do Azure](../../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Autenticação de desenvolvimento local

Para o desenvolvimento local, existem dois cenários de autenticação primária: [autenticação para serviços do Azure](#authenticating-to-azure-services)e [autenticação para serviços personalizados.](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Autenticação nos serviços do Azure

As máquinas locais não suportam identidades gerenciadas para os recursos do Azure. Como resultado, a biblioteca `Microsoft.Azure.Services.AppAuthentication` usa suas credenciais de desenvolvedor para ser executada no ambiente de desenvolvimento local. Quando a solução é implantada no Azure, a biblioteca usa uma identidade gerenciada para mudar para um fluxo de concessão de credenciais do cliente do OAuth 2.0. Esta abordagem significa que você pode testar o mesmo código local mente e remotamente sem preocupação.

Para o desenvolvimento local, o `AzureServiceTokenProvider` busca tokens usando o **Visual Studio**, a **CLI (interface de linha de comando) do Azure** ou a **Autenticação Integrada do Azure AD**. Cada opção é tentada na sequência e a biblioteca usa a primeira opção bem-sucedida. Se nenhuma opção funcionar, uma exceção `AzureServiceTokenProviderException` será gerada com informações detalhadas.

#### <a name="authenticating-with-visual-studio"></a>Autenticando com o Visual Studio

Para autenticar usando o Visual Studio:

1. Faça login no Visual Studio e use **opções de**&nbsp;>&nbsp;**ferramentas** para abrir **opções.**

1. Selecione **Autenticação de serviço do Azure,** escolha uma conta para o desenvolvimento local e selecione **OK**.

Se você encontrar problemas usando o Visual Studio, como erros que envolvem o arquivo do provedor de token, revise cuidadosamente as etapas anteriores.

Você pode precisar reautenticar seu token de desenvolvedor. Para isso, selecione **Opções de**&nbsp;>&nbsp;**ferramentas**e selecione **Azure&nbsp;Service&nbsp;Authentication**. Procure um **link de reautenticação** na conta selecionada. Selecione-o para autenticar.

#### <a name="authenticating-with-azure-cli"></a>Autenticando com a CLI do Azure

Para usar o Azure CLI para o desenvolvimento local, certifique-se de ter a versão [Azure CLI v2.0.12](/cli/azure/install-azure-cli) ou posterior.

Para usar o Azure CLI:

1. Procure o Azure CLI na barra de tarefas do Windows para abrir o **Prompt de comando do Microsoft Azure**.

1. Faça login no portal Azure: *az login* para fazer login no Azure.

1. Verifique o acesso inserindo *a entrada az conta get-token --recurso https://vault.azure.net *. Se você receber um erro, verifique se a versão certa do Azure CLI está instalada corretamente.

   Se o Azure CLI não estiver instalado no diretório padrão, `AzureServiceTokenProvider` você poderá receber um relatório de erro que não pode encontrar o caminho para o Azure CLI. Use a variável de ambiente **AzureCLIPath** para definir a pasta de instalação do Azure CLI. `AzureServiceTokenProvider` adiciona o diretório especificado na variável de ambiente **AzureCLIPath** à variável de ambiente **Path**, quando necessário.

1. Se você estiver conectado ao Azure CLI usando várias contas ou sua conta tiver acesso a várias assinaturas, você precisa especificar a assinatura a ser usada. Digite o conjunto de contas az de comando *--assinatura <>de assinatura *.

Esse comando gera a saída apenas em caso de falha. Para verificar as configurações da `az account list`conta corrente, digite o comando .

#### <a name="authenticating-with-azure-ad-authentication"></a>Autenticação com autenticação Azure AD

Para usar a autenticação do Azure AD, verifique se:

- Seu Diretório Ativo no local sincroniza com o Azure AD. Para obter mais informações, consulte [O que é identidade híbrida com o Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Seu código está sendo executado em um computador com domínio.

### <a name="authenticating-to-custom-services"></a>Autenticação em serviços personalizados

Quando um serviço chama os serviços do Azure, as etapas anteriores funcionam porque os serviços do Azure permitem o acesso a usuários e aplicativos.

Ao criar um serviço que chama um serviço personalizado, use as credenciais do cliente do Azure AD para autenticação de desenvolvimento local. Há duas opções:

- Use uma entidade de serviço para entrar no Azure:

    1. Crie uma entidade de serviço. Para obter mais informações, consulte [Criar uma entidade de serviço do Azure com CLI do Azure ](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Use o Azure CLI para fazer login com o seguinte comando:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Como a entidade de serviço pode não ter acesso a uma assinatura, use o argumento `--allow-no-subscriptions`.

- Use variáveis de ambiente para especificar detalhes da entidade de serviço. Para obter mais informações, consulte [Executando o aplicativo usando um diretor de serviço](#running-the-application-using-a-service-principal).

Depois de fazer o adesão `AzureServiceTokenProvider` ao Azure, use o diretor de serviço para recuperar um token para o desenvolvimento local.

Essa abordagem se aplica apenas ao desenvolvimento local. Quando a solução é implantada no Azure, a biblioteca muda uma identidade gerenciada com propósito de autenticação.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Executando o aplicativo usando identidade gerenciada ou identidade atribuída pelo usuário

Quando você executa o código em um Serviço de Aplicativo do Azure ou em uma VM do Azure com uma identidade gerenciada habilitada, a biblioteca usa automaticamente a identidade gerenciada. Não são necessárias alterações de código, mas a identidade gerenciada deve ter *permissões* para o cofre da chave. Você pode dar à identidade gerenciada *permissões* através das Políticas de *Acesso*do cofre de chaves .

Alternativamente, você pode autenticar com uma identidade atribuída pelo usuário. Para obter mais informações sobre identidades atribuídas pelo usuário, consulte [Sobre identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Para autenticar com uma identidade atribuída pelo usuário, você precisa especificar o ID do cliente da identidade atribuída pelo usuário na seqüência de conexões. A seqüência de conexões é especificada no [Connection String Support](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Executando o aplicativo usando uma Entidade de Serviço

Pode ser necessário criar uma credencial do Cliente do Azure AD para autenticar. Essa situação pode acontecer nos seguintes exemplos:

- O código é executado em um ambiente de desenvolvimento local, mas não na identidade do desenvolvedor. O Service Fabric, por exemplo, usa a [conta NetworkService](../../service-fabric/service-fabric-application-secret-management.md) para o desenvolvimento local.

- O código é executado em um ambiente de desenvolvimento local e você se autentica em um serviço personalizado e, portanto, não pode usar sua identidade de desenvolvedor.

- Seu código está sendo executado em um recurso de computação do Azure que ainda não suporta identidades gerenciadas para recursos do Azure, como o Azure Batch.

Existem três métodos principais de usar um Diretor de Serviço para executar sua aplicação. Para usar qualquer um deles, você deve primeiro criar um diretor de serviço. Para obter mais informações, consulte [Criar uma entidade de serviço do Azure com CLI do Azure ](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Use um certificado na loja de chaves local para entrar no Azure AD

1. Crie um certificado principal de serviço usando o comando Azure CLI [ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Este comando cria um arquivo .pem (chave privada) armazenado no diretório inicial. Implante este certificado na loja *LocalMachine* ou *CurrentUser.*

    > [!Important]
    > O comando CLI gera um arquivo .pem, mas o Windows só fornece suporte nativo para certificados PFX. Para gerar um certificado PFX, use os comandos PowerShell mostrados aqui: [Crie o principal de serviço com certificado auto-assinado](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Esses comandos também implantam automaticamente o certificado.

1. Defina uma variável de ambiente chamada **AzureServicesAuthConnectionString** para o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Substitua *{AppId}*, *{TenantId}* e *{Thumbprint}* pelos valores gerados na Etapa 1. Substitua *{CertificateStore}* por *LocalMachine*' ou *CurrentUser*, com base no seu plano de implantação.

1. Execute o aplicativo.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Use uma credencial secreta compartilhada para entrar no Azure AD

1. Crie um certificado principal de serviço com uma senha usando o comando Azure CLI [ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) com o parâmetro --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Defina uma variável de ambiente chamada **AzureServicesAuthConnectionString** para o seguinte valor:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Substitua _{AppId}_, _{TenantId}_ e _{ClientSecret}_ pelos valores gerados na Etapa 1.

1. Execute o aplicativo.

Depois que tudo estiver configurado corretamente, nenhuma alteração de código adicional será necessária. O `AzureServiceTokenProvider` usa a variável de ambiente e o certificado para se autenticar no Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Use um certificado no Key Vault para entrar no Azure AD

Esta opção permite que você armazene o certificado de cliente de um diretor de serviço no Key Vault e o use para autenticação principal do serviço. Você pode usar esta opção para os seguintes cenários:

- Autenticação local, onde você deseja autenticar usando um diretor de serviço explícito, e deseja manter a credencial principal do serviço com segurança em um cofre de chaves. A conta do desenvolvedor deve ter acesso ao cofre principal.

- Autenticação do Azure onde você deseja usar credencial explícita e deseja manter a credencial principal do serviço com segurança em um cofre de chaves. Você pode usar esta opção para um cenário de inquilino cruzado. A identidade gerenciada deve ter acesso ao cofre de chaves.

A identidade gerenciada ou sua identidade de desenvolvedor devem ter permissão para recuperar o certificado de cliente do Key Vault. A biblioteca AppAuthentication usa o certificado recuperado como credencial do cliente do diretor do serviço.

Para usar um certificado de cliente para autenticação principal do serviço:

1. Crie um certificado principal de serviço e armazene-o automaticamente no seu Cofre de Chaves. Use o [azure CLI ad sp create-for-rbac \<--keyvault keyvaultname> --cert \<certificatename> -create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) command:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    O identificador de certificado será uma URL no formato`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Substitua `{KeyVaultCertificateSecretIdentifier}` nesta seqüência de conexões pelo identificador de certificado:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Por exemplo, se o seu cofre principal foi chamado *de myKeyVault* e você criou um certificado chamado *myCert,* o identificador de certificado seria:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Suporte à cadeia de conexão

Por padrão, o `AzureServiceTokenProvider` usa vários métodos para recuperar um token.

Para controlar o processo, use uma cadeia de conexão passada para o construtor `AzureServiceTokenProvider` ou especificada na variável de ambiente *AzureServicesAuthConnectionString*.

Há suporte para as seguintes opções:

| Opção de seqüência de conexão | Cenário | Comentários|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Desenvolvimento local | `AzureServiceTokenProvider`usa o AzureCli para obter token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Desenvolvimento local | `AzureServiceTokenProvider`usa o Visual Studio para obter token. |
| `RunAs=CurrentUser` | Desenvolvimento local | `AzureServiceTokenProvider`usa a Autenticação Integrada Azure AD para obter token. |
| `RunAs=App` | [Identidades gerenciadas dos recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`usa uma identidade gerenciada para obter token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identidade atribuída pelo usuário para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`usa uma identidade atribuída pelo usuário para obter token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticação de serviços personalizados | `KeyVaultCertificateSecretIdentifier`é o identificador secreto do certificado. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Entidade de serviço | O `AzureServiceTokenProvider` usa um certificado para obter um token do Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Entidade de serviço | O `AzureServiceTokenProvider` usa um certificado para obter um token do Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Entidade de serviço |O `AzureServiceTokenProvider` usa um segredo para obter um token do Azure AD. |

## <a name="samples"></a>Exemplos

Para ver `Microsoft.Azure.Services.AppAuthentication` a biblioteca em ação, consulte as seguintes amostras de código.

- [Usar uma identidade gerenciada para recuperar um segredo do Azure Key Vault em runtime](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Implante de forma programática um modelo do Azure Resource Manager de uma VM do Azure com uma identidade gerenciada](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Use uma amostra do .NET Core e uma identidade gerenciada para chamar os serviços do Azure de uma VM Linux do Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Solução de problemas de autenticação de aplicativos

### <a name="common-issues-during-local-development"></a>Questões comuns durante o desenvolvimento local

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>O Azure CLI não está instalado, você não está logado ou não tem a versão mais recente

Execute *aaz conta get-access-token* para ver se o Azure CLI mostra um token para você. Se ele **disser que não foi encontrado tal programa,** instale a [versão mais recente do Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Talvez você receba uma solicitação para entrar.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider não consegue encontrar o caminho para o Azure CLI

O AzureServiceTokenProvider procura o Azure CLI em seus locais de instalação padrão. Se não conseguir encontrar o Azure CLI, defina a variável **ambiente AzureCLIPath** para a pasta de instalação do Azure CLI. O AzureServiceTokenProvider adicionará a variável de ambiente à variável de ambiente Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Você está logado no Azure CLI usando várias contas, a mesma conta tem acesso a assinaturas em vários inquilinos ou você recebe um erro de acesso negado ao tentar fazer chamadas durante o desenvolvimento local

Usando o Azure CLI, defina a assinatura padrão para uma que tenha a conta que você deseja usar. A assinatura deve estar no mesmo inquilino que o recurso que você deseja acessar: **conjunto de conta az --assinatura [assinatura-id]**. Se nenhuma saída for vista, ela conseguiu. Verifique se a conta certa agora é o padrão usando **a lista de contas az**.

### <a name="common-issues-across-environments"></a>Questões comuns em ambientes

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Acesso não autorizado, acesso negado, proibido ou erro semelhante

O principal usado não tem acesso ao recurso que está tentando acessar. Conceda acesso à sua conta de usuário ou ao MSI "Contribuinte" do App Service a um recurso. Qual deles depende se você está executando a amostra em seu computador local ou implantado no Azure para o seu Serviço de Aplicativo. Alguns recursos, como cofres-chave, também têm suas próprias políticas de [acesso](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) que você usa para conceder acesso a diretores, como usuários, aplicativos e grupos.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Problemas comuns quando implantados no Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>A identidade gerenciada não está configurada no Serviço de Aplicativos

Verifique as variáveis de ambiente MSI_ENDPOINT e MSI_SECRET existem usando [o console de depuração Kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Se essas variáveis de ambiente não existirem, a Identidade Gerenciada não será habilitada no Serviço de Aplicativos.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Problemas comuns quando implantados localmente com o IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Não é possível recuperar tokens ao depurar aplicativo no IIS

Por padrão, o AppAuth é executado em um contexto de usuário diferente no IIS. É por isso que ele não tem acesso ao uso da sua identidade de desenvolvedor para recuperar tokens de acesso. Você pode configurar o IIS para executar com o contexto do usuário com as duas etapas a seguir:
- Configure o Pool de aplicativos para o aplicativo web para ser executado como sua conta de usuário atual. Confira mais informações [aqui](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Configure "setProfileEnvironment" como "True". Veja mais informações [aqui](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Vá para %windir%\System32\inetsrv\config\applicationHost.config
    - Procure por "setProfileEnvironment". Se estiver definido como "Falso", mude para "True". Se não estiver presente, adicione-o como um atributo ao elemento processModel (),/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmente defina-o como "True".

- Aprenda sobre [identidades gerenciadas dos recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml).
- Saiba mais sobre os [cenários de autenticação do Azure AD](../../active-directory/develop/active-directory-authentication-scenarios.md).
