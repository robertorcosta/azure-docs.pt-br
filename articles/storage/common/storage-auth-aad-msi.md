---
title: Autorizar o acesso a dados com uma identidade gerenciada
titleSuffix: Azure Storage
description: Aprenda a usar identidades gerenciadas para recursos do Azure para autorizar o acesso a dados de blob e fila de aplicativos em execução em máquinas virtuais do Azure, aplicativos de função, conjuntos de escala de máquinas virtuais, entre outros.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255334"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorize o acesso a dados de blob e fila com identidades gerenciadas para recursos do Azure

Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure AD (Active Directory) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). As identidades gerenciadas para os recursos do Azure podem autorizar o acesso a dados de blob e fila usando credenciais Azure AD de aplicativos em execução em máquinas virtuais (VMs) do Azure, aplicativos de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao usar identidades gerenciadas para recursos do Azure, juntamente com a autenticação do Azure AD, você pode evitar armazenar credenciais com seus aplicativos que são executados na nuvem.  

Este artigo mostra como autorizar o acesso a dados de blob ou fila de uma VM Azure usando identidades gerenciadas para recursos do Azure. Ele também descreve como testar seu código no ambiente de desenvolvimento.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM

Antes de usar identidades gerenciadas para recursos do Azure para autorizar o acesso a blobs e filas de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo de Gerenciador de recursos do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Para obter mais informações sobre identidades gerenciadas, consulte [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Autenticar com a biblioteca de identidade azure

A biblioteca de clientes azure Identity fornece suporte de autenticação de token Azure Azure AD para o [Azure SDK](https://github.com/Azure/azure-sdk). As versões mais recentes das bibliotecas clientes do Azure Storage para .NET, Java, Python e JavaScript integram-se à biblioteca Azure Identity para fornecer um meio simples e seguro para adquirir um token OAuth 2.0 para autorização de solicitações de armazenamento do Azure.

Uma vantagem da biblioteca de clientes do Azure Identity é que ele permite que você use o mesmo código para autenticar se seu aplicativo está sendo executado no ambiente de desenvolvimento ou no Azure. A biblioteca de clientes azure Identity para .NET autentica um diretor de segurança. Quando seu código está sendo executado no Azure, o principal de segurança é uma identidade gerenciada para os recursos do Azure. No ambiente de desenvolvimento, a identidade gerenciada não existe, de modo que a biblioteca do cliente autentica o usuário ou um diretor de serviço para fins de teste.

Após a autenticação, a biblioteca de clientes do Azure Identity recebe uma credencial simbólica. Essa credencial de token é então encapsulada no objeto cliente de serviço que você cria para executar operações contra o Azure Storage. A biblioteca lida com isso para você perfeitamente, obtendo a credencial de token apropriada.

Para obter mais informações sobre a biblioteca de clientes azure Identity para .NET, consulte [a biblioteca de clientes azure Identity para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Para obter documentação de referência para a biblioteca de clientes do Azure Identity, consulte [Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Atribuir funções de controle de acesso baseado em função (RBAC) para acesso a dados

Quando um principal de segurança do Azure AD tenta acessar dados de blob ou fila, esse principal de segurança deve ter permissões para o recurso. Se o principal de segurança é uma identidade gerenciada no Azure ou uma conta de usuário Azure AD executando código no ambiente de desenvolvimento, o principal de segurança deve ser atribuído a uma função RBAC que concede acesso a blob ou dados de fila no Azure Storage. Para obter informações sobre como atribuir permissões via RBAC, consulte a seção **intitulada Atribuir funções RBAC para direitos de acesso** em Autorizar o acesso a [blobs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Autenticar o usuário no ambiente de desenvolvimento

Quando seu código estiver em execução no ambiente de desenvolvimento, a autenticação pode ser tratada automaticamente ou pode exigir um login do navegador, dependendo das ferramentas que você está usando. Por exemplo, o Microsoft Visual Studio suporta o SSO (Single Sign-on), de modo que a conta de usuário Ativa Azure AD seja usada automaticamente para autenticação. Para obter mais informações sobre o SSO, consulte [O login único em aplicativos](../../active-directory/manage-apps/what-is-single-sign-on.md).

Outras ferramentas de desenvolvimento podem solicitar o login através de um navegador da Web.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticar um diretor de serviços no ambiente de desenvolvimento

Se o seu ambiente de desenvolvimento não suportar logon único ou login através de um navegador da Web, então você pode usar um diretor de serviço para autenticar a partir do ambiente de desenvolvimento.

#### <a name="create-the-service-principal"></a>Criar a entidade de serviço

Para criar um princípio de serviço com o Azure CLI e atribuir uma função RBAC, chame o comando [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Forneça uma função de acesso a dados do Azure Storage para atribuir ao novo diretor de serviço. Além disso, fornecer o escopo para a atribuição da função. Para obter mais informações sobre as funções incorporadas fornecidas para o Armazenamento Azure, consulte [Funções incorporadas para os recursos do Azure](../../role-based-access-control/built-in-roles.md).

Se você não tiver permissões suficientes para atribuir uma função ao diretor do serviço, você pode precisar pedir ao proprietário ou administrador da conta para executar a atribuição da função.

O exemplo a seguir usa o Azure CLI para criar um novo principal de serviço e atribuir a função **de leitor de dados do Blob de armazenamento** a ele com escopo da conta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O `az ad sp create-for-rbac` comando retorna uma lista de propriedades principais de serviço no formato JSON. Copie esses valores para que você possa usá-los para criar as variáveis de ambiente necessárias no próximo passo.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> As atribuições de função RBAC podem levar alguns minutos para se propagar.

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A biblioteca de clientes azure Identity lê valores de três variáveis de ambiente em tempo de execução para autenticar o principal do serviço. A tabela a seguir descreve o valor a definir para cada variável de ambiente.

|Variável de ambiente|Valor
|-|-
|`AZURE_CLIENT_ID`|O ID do aplicativo para o diretor de serviço
|`AZURE_TENANT_ID`|ID do inquilino Azure AD do diretor de serviço
|`AZURE_CLIENT_SECRET`|A senha gerada para o diretor do serviço

> [!IMPORTANT]
> Depois de definir as variáveis de ambiente, feche e reabra a janela do console. Se você estiver usando o Visual Studio ou outro ambiente de desenvolvimento, talvez seja necessário reiniciar o ambiente de desenvolvimento para que ele registre as novas variáveis do ambiente.

Para obter mais informações, consulte [Criar identidade para o aplicativo Azure no portal](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: crie um blob de blocos

Adicione as `using` seguintes diretivas ao seu código para usar as bibliotecas de clientes azure Identity e Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Para obter uma credencial de token que seu código pode usar para autorizar solicitações ao Azure Storage, crie uma instância da classe [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) O exemplo de código a seguir mostra como obter a credencial de token autenticada e usá-la para criar um objeto cliente de serviço e, em seguida, usar o cliente de serviço para carregar uma nova bolha:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Para autorizar solicitações contra blob ou dados de fila com o Azure AD, você deve usar HTTPS para essas solicitações.

## <a name="next-steps"></a>Próximas etapas

- [Gerencie os direitos de acesso aos dados de armazenamento com RBAC](storage-auth-aad-rbac.md).
- [Use o Azure AD com aplicativos de armazenamento](storage-auth-aad-app.md).
- [Execute os comandos Azure CLI ou PowerShell com credenciais Azure AD para acessar dados de blob ou fila](authorize-active-directory-powershell.md).
