---
title: Início Rápido – Biblioteca de clientes do Azure Key Vault para .NET (SDK v3)
description: Saiba como criar, recuperar e excluir segredos de um Azure Key Vault usando a biblioteca de clientes do .NET (v3).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078873"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Início Rápido: Biblioteca de clientes do Azure Key Vault para .NET (SDK v3)

Introdução à biblioteca de clientes do Azure Key Vault para .NET. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

> [!NOTE]
> Este guia de início rápido usa a versão v3.0.4 da biblioteca de clientes do Azure Key Vault. Para usar a versão mais atualizada da biblioteca de clientes do Azure Key Vault, confira o tópico [Biblioteca de clientes do Azure Key Vault para .NET (SDK v4)](quick-create-net.md). 

O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Use a biblioteca de clientes do Key Vault para .NET para:

- Aumentar a segurança e o controle sobre chaves e senhas.
- Criar e importar chaves de criptografia em minutos.
- Reduzir a latência com escala de nuvem e redundância global.
- Simplifique e automatize tarefas para certificados TLS/SSL.
- Usar HSMs validados para os padrões FIPS 140-2 Nível 2.

[Documentação de referência da API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.


## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

Este início rápido pressupõe que você esteja executando `dotnet`, a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) e comandos do Windows em um terminal do Windows (como o [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), o [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) ou o [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Configurando

### <a name="create-new-net-console-app"></a>Criar um novo aplicativo de console do .NET

Em uma janela de console, use o comando `dotnet new` para criar um aplicativo do console .NET com o nome `akv-dotnet`.


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

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao seu cofre de chaves

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

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

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Atribua essas variáveis de ambiente a cadeias de caracteres em seu código e, em seguida, autentique seu aplicativo passando-as para a [classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que seu aplicativo está autenticado, você pode colocar um segredo em seu cofre de chaves usando o [método SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Isso requer a URL do cofre de chaves, que está no formato `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Também requer um nome para o segredo – estamos usando "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Você pode verificar se o segredo foi definido usando o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora, você pode recuperar o valor definido anteriormente com o [método GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Agora, seu segredo está salvo como `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar a CLI do Azure ou o Azure PowerShell para remover seu cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Veja o [aplicativo de console completo no GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Implementar a [Autenticação serviço a serviço no Azure Key Vault usando o .NET](../general/service-to-service-authentication.md)
- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)
