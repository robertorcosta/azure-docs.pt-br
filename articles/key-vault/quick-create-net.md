---
title: Início Rápido – Biblioteca de clientes do Azure Key Vault para .NET
description: Fornece critérios de formato e de conteúdo para escrever inícios rápidos para bibliotecas de clientes do SDK do Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 3ccc5c7c0def7ec1d8d2f8927dc8f8e5d3678a52
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718978"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Início Rápido: Biblioteca de clientes do Azure Key Vault para .NET

Introdução à biblioteca de clientes do Azure Key Vault para .NET. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Use a biblioteca de clientes do Key Vault para .NET para:

- Aumentar a segurança e o controle sobre chaves e senhas.
- Criar e importar chaves de criptografia em minutos.
- Reduzir a latência com escala de nuvem e redundância global.
- Simplificar e automatizar tarefas para certificados SSL/TLS.
- Usar HSMs validados para os padrões FIPS 140-2 Nível 2.

[Documentação de referência da API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [SDK do .NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este início rápido pressupõe que você esteja executando `dotnet`, a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e comandos do Windows em um terminal do Windows (como o [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), o [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) ou o [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Configurando

### <a name="create-new-net-console-app"></a>Criar um novo aplicativo de console do .NET

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido.

Em uma janela de console, use o comando `dotnet new` para criar um novo aplicativo do console com o nome `akv-dotnet`.


```console
dotnet new console -n akvdotnet
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de clientes do Azure Key Vault para .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Para este início rápido, também será necessário instalar os pacotes a seguir:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

Este início rápido usa um Azure Key Vault pré-criado. Você pode criar um cofre de chaves seguindo as etapas no [Início rápido da CLI do Azure](quick-create-cli.md), no [Início rápido do Azure PowerShell](quick-create-powershell.md) ou no [Início rápido do portal do Azure](quick-create-portal.md). Como alternativa, você pode simplesmente executar os comandos da CLI do Azure abaixo.

> [!Important]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço

A maneira mais simples de autenticar um aplicativo .NET baseado em nuvem é com uma identidade gerenciada; confira [Usar uma identidade gerenciada do Serviço de Aplicativo para acessar o Azure Key Vault](managed-identity.md) para obter detalhes. Para simplificar, no entanto, este início rápido cria um aplicativo de console .NET. A autenticação de um aplicativo da área de trabalho no Azure exige o uso de uma entidade de serviço e uma política de controle de acesso.

Crie uma entidade de serviço usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) da CLI do Azure:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Essa operação retornará uma série de pares de chave/valor. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Anote a clientId e o clientSecret, pois usaremos essas informações na etapa [Autenticação no cofre de chaves](#authenticate-to-your-key-vault) abaixo.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao seu cofre de chaves

Crie uma política de acesso para o cofre de chaves que concede permissão à entidade de serviço passando a clientId para o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Conceda à entidade de serviço permissões para obter, listar e definir chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes do Azure Key Vault para .NET permite gerenciar chaves e ativos relacionados, como certificados e segredos. Os exemplos de código abaixo mostrarão como definir e como recuperar um segredo.

Todo o aplicativo de console está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior de seu código:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Autenticar no cofre de chaves

Este início rápido do .NET usa variáveis de ambiente para armazenar credenciais que não deveriam ser colocadas em código. 

Antes de compilar e executar seu aplicativo, use o comando `setx` para definir as variáveis de ambiente `akvClientId`, `akvClientSecret`, `akvTenantId` e `akvSubscriptionId` como os valores que você anotou acima.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Sempre que chamar `setx`, você deverá receber uma resposta de "ÊXITO: o valor especificado foi salvo. "

Atribua essas variáveis de ambiente a cadeias de caracteres em seu código e, em seguida, autentique seu aplicativo passando-as para a [classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que seu aplicativo está autenticado, você pode colocar um segredo em seu cofre de chaves usando o [método SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Isso requer a URL do cofre de chaves, que está no formato `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Também requer um nome para o segredo – estamos usando "mySecret".  Talvez você queira atribuir essas cadeias de caracteres a variáveis para reutilização.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Você pode verificar se o segredo foi definido usando o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora, você pode recuperar o valor definido anteriormente com o [método GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Agora, seu segredo está salvo como `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar a CLI do Azure ou o Azure PowerShell para remover seu cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Veja o [aplicativo de console completo no GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Implementar a [Autenticação serviço a serviço no Azure Key Vault usando o .NET](service-to-service-authentication.md)
- Leia uma [Visão geral do Azure Key Vault](key-vault-overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examine as [Melhores práticas do Azure Key Vault](key-vault-best-practices.md)
