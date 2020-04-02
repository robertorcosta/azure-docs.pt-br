---
title: Crie um aplicativo Ad do Azure no Azure Data Explorer
description: Saiba como criar um aplicativo Azure AD no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550507"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Crie um registro de aplicativo do Azure Active Directory no Azure Data Explorer

A autenticação do aplicativo Azure Active Directory (Azure AD) é usada para aplicativos, como um serviço autônomo ou um fluxo programado, que precisam acessar o Azure Data Explorer sem a presença de um usuário. Se você estiver se conectando a um banco de dados do Azure Data Explorer usando um aplicativo, como um aplicativo web, você deve autenticar usando a autenticação principal do serviço. Este artigo detalha como criar e registrar um diretor de serviço Azure AD e, em seguida, autorizá-lo a acessar um banco de dados do Azure Data Explorer.

## <a name="create-azure-ad-application-registration"></a>Criar registro de aplicativo Ad do Azure

A autenticação do aplicativo Azure AD requer a criação e registro de um aplicativo com o Azure AD. Um princípio de serviço é criado automaticamente quando o registro do aplicativo é criado em um inquilino AD do Azure. 

1. Faça login no [portal Azure](https://portal.azure.com) e abra a `Azure Active Directory` lâmina

    ![Selecione diretório ativo do Azure no menu do portal](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Selecione a lâmina **de registros** do App e selecione **Novo registro**

    ![Inicie um novo registro de aplicativo](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Preencha as seguintes informações: 

    * **Nome** 
    * **Tipos de conta com suporte**
    * **Redirecionar a Web URI** > **Web**
        > [!IMPORTANT] 
        > O tipo de aplicação deve ser **Web**. O URI é opcional e é deixado em branco neste caso.
    * Selecionar **Registro**

    ![Registre novo registro de aplicativo](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Selecione a **lâmina Visão geral** e copie o **ID do aplicativo**.

    > [!NOTE]
    > Você precisará do ID do aplicativo para autorizar o diretor do serviço a acessar o banco de dados.

    ![Copiar iD de registro de aplicativo](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Nos **Certificados &** lâmina de segredos, selecione **Novo segredo do cliente**

    ![Iniciar a criação do segredo do cliente](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > Este artigo descreve o uso de um segredo de cliente para as credenciais do aplicativo.  Você também pode usar um certificado X509 para autenticar seu aplicativo. Selecione **o certificado Upload** e siga as instruções para carregar a parte pública do certificado.

1. Digite uma descrição, expiração e selecione **Adicionar**

    ![Digite parâmetros secretos do cliente](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Copie o valor da chave.

    > [!NOTE]
    > Quando você sair desta página, o valor-chave não estará acessível.  Você precisará da chave para configurar as credenciais do cliente no banco de dados.

    ![Copiar o valor da chave secreta do cliente](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Sua aplicação foi criada. Se você só precisar de acesso a um recurso autorizado do Azure Data Explorer, como no exemplo programático abaixo, pule a próxima seção. Para obter suporte a permissões delegadas, consulte [configurar permissões delegadas para o registro do aplicativo](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Configure permissões delegadas para o registro do aplicativo

Se seu aplicativo precisar acessar o Azure Data Explorer usando as credenciais do usuário de chamada, configure permissões delegadas para o registro do aplicativo. Por exemplo, se você estiver construindo uma API web para acessar o Azure Data Explorer e quiser autenticar usando as credenciais do usuário que está *chamando* sua API.  

1. Na **lâmina de permissões da API,** **selecione Adicionar uma permissão**.
1. Selecione **APIs que minha organização usa**. Procure e selecione **o Azure Data Explorer**.

    ![Adicionar permissão de API do Azure Data Explorer](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. Em **permissões delegadas,** selecione a caixa **de user_impersonation** e adicione **permissões**

    ![Selecione permissões delegadas com personificação do usuário](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Conceda ao principal serviço acesso a um banco de dados do Azure Data Explorer

Agora que o registro do aplicativo principal do seu serviço foi criado, você precisa conceder ao principal serviço o acesso ao seu banco de dados Do Azure Data Explorer. 

1. Na [Web UI,](https://dataexplorer.azure.com/)conecte-se ao seu banco de dados e abra uma guia de consulta.

1. Execute o comando a seguir:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Por exemplo: 
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    O último parâmetro é uma string que aparece como notas quando você consulta as funções associadas a um banco de dados.
    
    > [!NOTE]
    > Depois de criar o registro do aplicativo, pode haver um atraso de vários minutos até que o Azure Data Explorer possa referencia-lo. Se, ao executar o comando, você receber um erro de que o aplicativo não for encontrado, espere e tente novamente.

Para obter mais informações, consulte as permissões de gerenciamento e [ingestão de funções](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)de [segurança](/azure/kusto/management/security-roles) .  

## <a name="using-application-credentials-to-access-a-database"></a>Usando credenciais de aplicativos para acessar um banco de dados

Use as credenciais do aplicativo para acessar programáticamente seu banco de dados usando a biblioteca de clientes do [Azure Data Explorer](/azure/kusto/api/netfx/about-kusto-data.md).

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Especifique o id do aplicativo e a chave do registro de aplicativo (principal de serviço) criado anteriormente.

Para obter mais informações, consulte [autenticar com o Azure AD para acesso ao Azure Data Explorer](/azure/kusto/management/access-control/how-to-authenticate-with-aad) e [use o Azure Key Vault com o aplicativo web .NET Core](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="invalid-resource-error"></a>Erro de recurso inválido

Se o aplicativo for usado para autenticar usuários ou aplicativos para acesso ao Azure Data Explorer, você deve configurar permissões delegadas para o aplicativo de serviço Azure Data Explorer. Declare que seu aplicativo pode autenticar usuários ou aplicativos para acesso ao Azure Data Explorer. Não fazê-lo resultará em um erro semelhante ao seguinte, quando uma tentativa de autenticação é feita:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Você precisará seguir as instruções sobre a [configuração de permissões delegadas para o aplicativo de serviço Azure Data Explorer](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Habilitar erro de consentimento do usuário

O administrador do inquilino Azure AD pode aprovar uma política que impede os usuários inquilinos de dar consentimento aos aplicativos. Essa situação resultará em um erro semelhante ao seguinte, quando um usuário tenta fazer login no seu aplicativo:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Você precisará entrar em contato com o administrador do Azure AD para conceder consentimento para todos os usuários do inquilino ou habilitar o consentimento do usuário para o seu aplicativo específico.

## <a name="next-steps"></a>Próximas etapas

* Consulte [as strings de conexão Kusto](/azure/kusto/api/connection-strings/kusto.md) para obter a lista de strings de conexão suportadas.
