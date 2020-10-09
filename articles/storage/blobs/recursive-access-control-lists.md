---
title: Definir ACLs recursivamente para Azure Data Lake Storage Gen2 | Microsoft Docs
description: Você pode adicionar, atualizar e remover listas de controle de acesso recursivamente para itens filho existentes de um diretório pai.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/07/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: cedb6d162829d63aaac1a36b35abee1faeae3f1b
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843389"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Definir listas de controle de acesso (ACLs) recursivamente para Azure Data Lake Storage Gen2

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Agora, você também pode adicionar, atualizar e remover ACLs recursivamente para itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho.

> [!NOTE]
> A capacidade de definir listas de acesso recursivamente está em visualização pública e está disponível em todas as regiões.  

[Bibliotecas](#libraries)  |  do [Exemplos](#code-samples)  |  [Práticas](#best-practice-guidelines)  |  recomendadas [Enviar comentários](#provide-feedback)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar um.

- As permissões corretas para executar o processo de ACL recursivo. A permissão correta inclui um dos seguintes: 

  - Uma [entidade de segurança](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.   

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar o processo de ACL recursivo. Isso inclui todos os itens filho no diretório ou contêiner de destino. 

- Uma compreensão de como as ACLs são aplicadas a diretórios e arquivos. Consulte [controle de acesso em Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Consulte a seção **configurar seu projeto** deste artigo para exibir as diretrizes de instalação para o PowerShell, o SDK do .net e o SDK do Python.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale as bibliotecas necessárias.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verifique se o .NET Framework está instalado. Consulte [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
3. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Feche e reabra o console do PowerShell.

5. Instale o módulo de visualização **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="net"></a>[.NET](#tab/dotnet)

1. Abra uma janela de comando (por exemplo: Windows PowerShell).

2. No diretório do projeto, instale o pacote de visualização do Azure. Storage. files. datalake usando o `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Adicione-as usando as instruções na parte superior do seu arquivo de código.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Baixe a [biblioteca de cliente Azure data Lake Storage para Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Instale a biblioteca que você baixou usando [Pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Adicione essas instruções de importação na parte superior do seu arquivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Conectar-se à sua conta

Você pode se conectar usando o Azure Active Directory (AD) ou usando uma chave de conta. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Abra uma janela de comando do Windows PowerShell e entre em sua assinatura do Azure com o `Connect-AzAccount` comando e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento na qual você deseja criar e gerenciar diretórios. Neste exemplo, substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Em seguida, escolha como você deseja que seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: obter autorização usando o Azure Active Directory (AD)

Com essa abordagem, o sistema garante que sua conta de usuário tenha as atribuições apropriadas do Azure RBAC (controle de acesso baseado em função) e permissões de ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

A tabela a seguir mostra cada uma das funções com suporte e sua funcionalidade de configuração de ACL.

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: obter autorização usando a chave da conta de armazenamento

Com essa abordagem, o sistema não verifica as permissões de ACL ou RBAC do Azure.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

Para usar os trechos de código neste artigo, você precisará criar uma instância de [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que representa a conta de armazenamento.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar seu aplicativo com o Azure AD.

Depois de instalar o pacote, adicione esta instrução using à parte superior do seu arquivo de código.

```csharp
using Azure.Identity;
```

Obtenha uma ID do cliente, um segredo do cliente e uma ID de locatário. Para fazer isso, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

Este exemplo cria uma instância de [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa abordagem é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) usando uma chave de conta.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca do cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="python"></a>[Python](#tab/python)

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Substitua o valor de espaço reservado `storage_account_name` pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor de espaço reservado pela chave de acesso da conta de armazenamento.

---

## <a name="set-an-acl-recursively"></a>Definir uma ACL recursivamente

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [atualizar uma ACL recursivamente](#update-an-acl-recursively) deste artigo.   

Esta seção contém exemplos de como definir uma ACL 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Defina uma ACL recursivamente usando o `Set-AzDataLakeGen2AclRecursive` cmdlet.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se você quiser definir uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Defina uma ACL recursivamente chamando o método **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser definir uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser definida. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Defina uma ACL recursivamente chamando o método **DataLakeDirectoryClient.set_access_control_recursive** .

Se você quiser definir uma entrada ACL **padrão** , adicione a cadeia de caracteres `default:` ao início de cada cadeia de caracteres de entrada de ACL. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . 

Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a ACL padrão deve ser definida. Se esse parâmetro for `True` , a lista de entradas de ACL será precedida pela cadeia de caracteres `default:` . 

As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução. Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Atualizar uma ACL recursivamente

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [definir uma ACL recursivamente](#set-an-acl-recursively) deste artigo. 

Para atualizar uma ACL, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas.

Esta seção contém exemplos de como atualizar uma ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Atualize uma ACL recursivamente usando o cmdlet  **Update-AzDataLakeGen2AclRecursive** . 

Este exemplo atualiza uma entrada ACL com permissão de gravação. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se você quiser atualizar uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser atualizar uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo atualiza uma entrada ACL com permissão de gravação. Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser atualizada. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient.update_access_control_recursive** . Se você quiser atualizar uma entrada de ACL **padrão** , adicione a cadeia de caracteres `default:` ao início de cada cadeia de caracteres de entrada de ACL. 

Este exemplo atualiza uma entrada ACL com permissão de gravação.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a ACL padrão deve ser atualizada. Se esse parâmetro for `True` , a entrada de ACL atualizada será precedida pela cadeia de caracteres `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Você pode remover uma ou mais entradas de ACL recursivamente. Para remover uma entrada de ACL, crie um novo objeto ACL para a entrada de ACL a ser removida e use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas. 

Esta seção contém exemplos de como remover uma ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Remova as entradas ACL usando o cmdlet **Remove-AzDataLakeGen2AclRecursive** . 

Este exemplo remove uma entrada ACL do diretório raiz do contêiner.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Se você quiser remover uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Remova as entradas de ACL chamando o método **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser remover uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a entrada deve ser removida da ACL padrão. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.remove_access_control_recursive** . Se você quiser remover uma entrada ACL **padrão** , adicione a cadeia de caracteres `default:` ao início da cadeia de caracteres de entrada ACL. 

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a entrada deve ser removida da ACL padrão. Se esse parâmetro for `True` , a entrada de ACL atualizada será precedida pela cadeia de caracteres `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        if is_default_scope:
           acl = 'default:user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão. Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Retornar resultados para a variável. Falha nas entradas de pipe para uma tabela formatada.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Com base na saída da tabela, você pode corrigir quaisquer erros de permissão e, em seguida, retomar a execução usando o token de continuação.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de ``null`` para o parâmetro de token de continuação. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de ``None`` para o parâmetro de token de continuação. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Recursos

Esta seção contém links para bibliotecas e exemplos de código.

#### <a name="libraries"></a>Bibliotecas

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Exemplos de código

- PowerShell: exemplo de [Leiame](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: exemplo de [Leiame](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: exemplo de [Leiame](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Diretrizes de práticas recomendadas

Esta seção fornece algumas diretrizes de práticas recomendadas para definir as ACLs recursivamente. 

#### <a name="handling-runtime-errors"></a>Manipulando erros de tempo de execução

Um erro de tempo de execução pode ocorrer por vários motivos (por exemplo: uma interrupção ou um problema de conectividade de cliente). Se você encontrar um erro de tempo de execução, reinicie o processo de ACL recursivo. As ACLs podem ser reaplicadas a itens sem causar um impacto negativo. 

#### <a name="handling-permission-errors-403"></a>Tratamento de erros de permissão (403)

Se você encontrar uma exceção de controle de acesso durante a execução de um processo de ACL recursivo, a [entidade de segurança](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) do AD poderá não ter permissão suficiente para aplicar uma ACL a um ou mais dos itens filho na hierarquia de diretórios. Quando ocorre um erro de permissão, o processo é interrompido e um token de continuação é fornecido. Corrija o problema de permissão e, em seguida, use o token de continuação para processar o conjunto de um restante. Os diretórios e arquivos que já foram processados com êxito não precisam ser processados novamente. Você também pode optar por reiniciar o processo de ACL recursivo. As ACLs podem ser reaplicadas a itens sem causar um impacto negativo. 

#### <a name="credentials"></a>Credenciais 

Recomendamos que você provisione uma entidade de segurança do Azure AD que tenha sido atribuída à função de [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo da conta de armazenamento de destino ou do contêiner. 

#### <a name="performance"></a>Desempenho 

Para reduzir a latência, recomendamos que você execute o processo de ACL recursivo em uma VM (máquina virtual) do Azure localizada na mesma região que a sua conta de armazenamento. 

#### <a name="acl-limits"></a>Limites de ACL

O número máximo de ACLs que você pode aplicar a um diretório ou arquivo é de 32 ACLs de acesso e 32 ACLs padrão. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Fornecer comentários ou relatar problemas

Você pode fornecer seus comentários ou relatar um problema em  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>Consulte também

- [Controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Problemas conhecidos](data-lake-storage-known-issues.md)


