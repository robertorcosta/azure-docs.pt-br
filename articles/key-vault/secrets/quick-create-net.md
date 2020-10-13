---
title: Guia de Início Rápido – Biblioteca de clientes do Azure Key Vault para .NET (v4)
description: Saiba como criar, recuperar e excluir segredos de um Azure Key Vault usando a biblioteca de clientes do .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 97578233c6b636b5ffe35fa8ff0b138903425f79
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631691"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Início Rápido: biblioteca de clientes do Azure Key Vault para .NET (SDK v4)

Introdução à biblioteca de segredos do cliente do Azure Key Vault para .NET. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

[Documentação de referência da API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core)
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

Este guia de início rápido usa o `dotnet` e a CLI do Azure

## <a name="setup"></a>Instalação

### <a name="create-a-new-console-app"></a>Criar um aplicativo de console

Este guia de início rápido usa a biblioteca de identidades do Azure com a CLI do Azure para autenticar o usuário nos serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para obter mais informações, confira [Autenticar o cliente na biblioteca de clientes de identidades do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

    Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.


### <a name="create-new-net-console-app"></a>Criar um novo aplicativo de console do .NET

1. Em um shell de comando, execute o seguinte comando para criar um projeto chamado `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Altere para o diretório *key-vault-console-app* recém-criado e execute o seguinte comando para compilar o projeto:

    ```dotnetcli
    dotnet build
    ```

    A saída de compilação não deve conter nenhum aviso ou erro.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Instalar os pacotes

No shell de comando, instale a biblioteca de clientes do Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este guia de início rápido, você também precisará instalar a biblioteca de clientes do SDK do Azure para a identidade do Azure:

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Criar uma política de acesso para o cofre de chaves que conceda a permissão de segredos à sua conta de usuário

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Este aplicativo usa o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de segredos do cliente do Azure Key Vault para .NET permite que você gerencie segredos. A seção [Exemplos de código](#code-examples) mostra como criar um cliente, bem como definir, recuperar e excluir um segredo.

Todo o aplicativo de console está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior de *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações, confira [Autenticação de credenciais do Azure padrão](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#defaultazurecredential). 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que o aplicativo de console foi autenticado, adicione um segredo ao cofre de chaves. Para essa tarefa, use o método [client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). O primeiro parâmetro do método aceita um nome para o segredo "mySecret" neste exemplo.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Você pode verificar se o segredo foi definido usando o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora, você pode recuperar o valor definido anteriormente com o método [client.GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Agora, seu segredo está salvo como `secret.Value`.

### <a name="delete-a-secret"></a>Excluir um segredo

Por fim, vamos excluir o segredo do cofre de chaves com o método [client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Você pode verificar se o segredo foi excluído com o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o cofre de chaves e o grupo de recursos correspondente não forem mais necessários, use a CLI do Azure ou o Azure PowerShell para removê-los.

### <a name="delete-a-key-vault"></a>Excluir um Key Vault

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Limpar um Key Vault

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Excluir um grupo de recursos

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo

Modifique o aplicativo de console .NET Core para interagir com o Key Vault concluindo as seguintes etapas:

1. Substitua o código em *Program.cs* pelo código a seguir:

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static void Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = client.StartDeleteSecret(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                while (!operation.HasCompleted)
                {
                    Thread.Sleep(2000);
                
                    operation.UpdateStatus();
                }
                client.PurgeDeletedSecret(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Execute o comando a seguir para executar o aplicativo.

    ```dotnetcli
    dotnet run
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, mySecretPassword.

    Uma variação do seguinte resultado é exibida:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um cofre de chaves, armazenou um segredo e o recuperou. Veja o [aplicativo de console completo no GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Para saber mais sobre o Key Vault e como integrá-lo aos seus aplicativos, confira os seguintes artigos:

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira um [Tutorial – Acessar o Key Vault no aplicativo do Serviço de Aplicativo](../general/tutorial-net-create-vault-azure-web-app.md)
- Confira um [Tutorial – Acessar o Key Vault na máquina virtual](../general/tutorial-net-virtual-machine.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)
