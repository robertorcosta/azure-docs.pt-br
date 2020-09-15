---
title: Início Rápido – Biblioteca de clientes Python do Azure Key Vault – gerenciar segredos
description: Saiba como criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de cliente do Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489197"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Início Rápido: Biblioteca de clientes de segredos do Azure Key Vault para Python

Introdução à biblioteca de clientes do Azure Key Vault para Python. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas. Ao usar o Key Vault para armazenar segredos, você evita armazenar segredos no código, o que aumenta a segurança do aplicativo.

[Documentação de referência da API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [Pacote (Índice de Pacote do Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Configurar o ambiente local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instale a biblioteca de segredos do Key Vault:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao seu cofre de chaves

Execute o seguinte comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para autorizar sua entidade de serviço para operações de obtenção, lista e definição em segredos. Esse comando depende das variáveis de ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` criadas nas etapas anteriores.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Esse comando depende das variáveis de ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` criadas nas etapas anteriores.

Para obter mais informações, confira [Atribuir uma política de acesso – CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Criar o código de exemplo

A biblioteca de clientes do Azure Key Vault para Python permite gerenciar segredos e ativos relacionados, como certificados e chaves criptográficas. O exemplo de código a seguir demonstra como criar um cliente, definir um segredo, recuperar um segredo e excluir um segredo.

Crie um arquivo chamado *kv_secrets.py* que contenha esse código.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Verifique se o código na seção anterior está em um arquivo chamado *kv_secrets.py*. Execute o código com o seguinte comando:

```terminal
python kv_secrets.py
```

- Se você encontrar erros de permissões, verifique se executou o [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Executar novamente o código com o mesmo nome de segredo pode produzir o erro "O segredo (conflito) <name> está em um estado excluído, mas recuperável". Use um nome de segredo diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

No código anterior, o objeto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) usa as variáveis de ambiente que você criou para a entidade de serviço. Você fornece essa credencial sempre que cria um objeto de cliente de uma biblioteca do Azure, como [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python), juntamente com o URI do recurso com o qual você deseja trabalhar por meio desse cliente:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Salvar um segredo

Depois de obter o objeto de cliente para o cofre de chaves, você pode armazenar um segredo usando o método [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-): 

```python
client.set_secret(secretName, secretValue)
```

Chamar `set_secret` gera uma chamada à API REST do Azure para o cofre de chaves.

Ao processar a solicitação, o Azure autentica a identidade do chamador (a entidade de serviço) usando o objeto de credencial fornecido ao cliente.

Ele também verifica se o chamador está autorizado a executar a ação solicitada. Você concedeu essa autorização à entidade de serviço anteriormente usando o [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Para ler um segredo do Key Vault, use o método [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-):

```python
retrieved_secret = client.get_secret(secretName)
 ```

O valor do segredo está contido em `retrieved_secret.value`.

Você também pode recuperar um segredo com o comando da CLI do Azure [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Excluir um segredo

Para excluir um segredo, use o método [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-):

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

O método `begin_delete_secret` é assíncrono e retorna um objeto do instrumento de sondagem. Chamar o método `result` do instrumento de sondagem aguarda sua conclusão.

Você pode verificar se o segredo foi removido com o comando da CLI do Azure [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Depois de excluído, um segredo permanece em um estado excluído, mas recuperável, por algum tempo. Se você executar o código novamente, use um nome de segredo diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você também quiser experimentar [certificados](../certificates/quick-create-python.md) e [chaves](../keys/quick-create-python.md), poderá reutilizar o Key Vault criado neste artigo.

Caso contrário, quando tiver concluído os recursos criados neste artigo, use o seguinte comando para excluir o grupo de recursos e todos os recursos que ele contém:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da o Cofre da Chave do Azure](../general/overview.md)
- [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- [Melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com o Key Vault](../general/authentication.md)
