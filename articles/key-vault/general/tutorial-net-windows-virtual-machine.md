---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual do Windows no .NET | Microsoft Docs
description: Neste tutorial, você vai configurar um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8db1c511ab9defb140720655588b27279a0f08be
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085473"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual do Windows no .NET

O Azure Key Vault ajuda a proteger segredos como chaves de API, as cadeias de conexão de banco de dados que você precisa para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, você aprenderá como fazer um aplicativo de console ler as informações no Azure Key Vault. O aplicativo usará a identidade gerenciada da máquina virtual para se autenticar no Key Vault. 

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie um grupos de recursos.
> * Crie um cofre da chave.
> * Adicionar um segredo ao cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilitar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Atribuir permissões à identidade da VM.

Antes de começar, leia [Conceitos básicos do Key Vault](basic-concepts.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * O [SDK do .NET Core 3.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de começar a codificar, você precisará criar alguns recursos, colocar um segredo no cofre de chaves e atribuir permissões.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). 

Este exemplo cria o grupo de recursos localizado no Oeste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usará o grupo de recursos recém criado neste tutorial.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Criar um cofre de chaves e preenchê-lo com um segredo

Crie um cofre de chaves em seu grupo de recursos, fornecendo o comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) com as seguintes informações:

* Nome do cofre de chaves: uma cadeia de caracteres de 3 a 24 caracteres contendo somente números (0 a 9), letras (a a z, A a Z) e hifens (-)
* Nome do grupo de recursos
* Localização: **Oeste dos EUA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre de chaves.

Agora, adicione um segredo ao cofre de chaves usando o comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma máquina virtual usando um dos seguintes métodos:

* [A CLI do Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [O portal do Azure](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM
Crie uma identidade atribuída pelo sistema para a máquina virtual com o comando [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign):

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM
Atribua as permissões de identidade criadas anteriormente ao cofre de chaves, com o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy):

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Entrar na máquina virtual

Para entrar na máquina virtual, siga as instruções em [Conectar e entrar em uma máquina virtual do Azure executando o Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Configurar o aplicativo de console

Criar um aplicativo de console e instalar os pacotes necessários usando o comando `dotnet`.

### <a name="install-net-core"></a>Instalar o .NET Core

Para instalar o .NET Core, acesse a página [Downloads do .NET](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Abra um prompt de comando.

Você pode exibir "Olá, Mundo" no console executando os comandos a seguir:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de clientes de Segredos do Azure Key Vault para .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este guia de início rápido, você precisará instalar o seguinte pacote de identidade para autenticação no Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Editar o aplicativo de console

Abra o arquivo *Program.cs* e adicione esses pacotes:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adicione essas linhas, atualizando o URI para refletir o `vaultUri` do cofre de chaves. O código abaixo está usando ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) para autenticação no cofre de chaves, que está usando o token da identidade gerenciada do aplicativo para autenticar. Ele também está usando retirada exponencial para novas tentativas, caso o cofre de chaves esteja sendo limitado.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

O código anterior mostra como realizar operações com o Azure Key Vault em uma máquina virtual do Windows.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
