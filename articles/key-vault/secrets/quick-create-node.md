---
title: Início Rápido – Biblioteca de clientes do Azure Key Vault para Node.js (v4)
description: Saiba como criar, recuperar e excluir segredos de um Azure Key Vault usando a biblioteca de clientes do Node.js
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 609076cd204457bb7c952c91cd249b1aece58022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421450"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Início Rápido: biblioteca de clientes do Azure Key Vault para Node.js (v4)

Introdução à biblioteca de clientes do Azure Key Vault para Node.js. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Use a biblioteca de clientes do Key Vault para Node.js para:

- Aumentar a segurança e o controle sobre chaves e senhas.
- Criar e importar chaves de criptografia em minutos.
- Reduzir a latência com escala de nuvem e redundância global.
- Simplifique e automatize tarefas para certificados TLS/SSL.
- Usar HSMs validados para os padrões FIPS 140-2 Nível 2.

[Documentação de referência da API](/javascript/api/overview/azure/key-vault?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacote (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org) atual para o seu sistema operacional.
- [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este início rápido pressupõe que você está executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) em uma janela de terminal do Linux.

## <a name="setting-up"></a>Configurando

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de segredos do Azure Key Vault para Node.js.

```console
npm install @azure/keyvault-secrets
```

Para este guia de início rápido, você precisará instalar também o pacote azure.identity:

```console
npm install @azure/identity
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

A maneira mais simples de autenticar um aplicativo baseado em nuvem é com uma identidade gerenciada; confira [Usar uma identidade gerenciada do Serviço de Aplicativo para acessar o Azure Key Vault](../general/managed-identity.md) para obter detalhes. Para simplificar, no entanto, este início rápido cria um aplicativo de console. A autenticação de um aplicativo da área de trabalho no Azure exige o uso de uma entidade de serviço e uma política de controle de acesso.

Crie uma entidade de serviço usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) da CLI do Azure:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Essa operação retornará uma série de pares de chave/valor. 

```azurecli
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Anote o clientId e o clientSecret, pois eles serão usados na etapa abaixo [Definir variável de ambiente](#set-environmental-variables).

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao seu cofre de chaves

Crie uma política de acesso para o cofre de chaves que concede permissão à entidade de serviço passando a clientId para o comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Conceda à entidade de serviço permissões para obter, listar e definir chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Definir variáveis de ambiente

O método DefaultAzureCredential em nosso aplicativo depende de três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID`. Defina essas variáveis para os valores clientId, clientSecret e tenantId que você anotou na etapa [Criar uma entidade de serviço](#create-a-service-principal) usando o formato `export VARNAME=VALUE`. (Isso define apenas as variáveis para o shell atual e os processos criados do shell; para adicionar permanentemente essas variáveis ao seu ambiente, edite seu arquivo `/etc/environment `). 

Você também precisará salvar o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes do Azure Key Vault para Node.js permite gerenciar chaves e ativos relacionados, como certificados e segredos. Os exemplos de código abaixo mostrarão como criar um cliente, definir, recuperar e excluir um segredo.

Todo o aplicativo de console está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior de seu código:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

A autenticação no cofre de chaves e a criação de um cliente de cofre de chaves dependem das variáveis ambientais na etapa [Definir variáveis de ambiente](#set-environmental-variables) acima e o [construtor SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

O nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato `https://<your-key-vault-name>.vault.azure.net`. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que seu aplicativo foi autenticado, é possível colocar um segredo em seu cofre de chaves usando o [método client.setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-). Isso requer um nome para o segredo, estamos usando "mySecret" neste exemplo.  

```javascript
await client.setSecret(secretName, secretValue);
```

Você pode verificar se o segredo foi definido usando o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora, você pode recuperar o valor definido anteriormente com o [método client.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Agora, seu segredo está salvo como `retrievedSecret.value`.

### <a name="delete-a-secret"></a>Excluir um segredo

Por fim, vamos excluir o segredo do cofre de chaves com o [método client.beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

Você pode verificar se o segredo foi excluído com o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar a CLI do Azure ou o Azure PowerShell para remover seu cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)