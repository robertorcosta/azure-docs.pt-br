---
title: Início Rápido – Biblioteca de clientes Python do Azure Key Vault – gerenciar chaves
description: Saiba como criar, recuperar e excluir chaves de um Azure Key Vault usando a biblioteca de clientes do Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482106"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Início Rápido: Biblioteca de clientes de chaves do Azure Key Vault para Python

Introdução à biblioteca de clientes do Azure Key Vault para Python. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas. Ao usar o Key Vault para armazenar chaves criptográficas, você evita armazenar essas chaves em seu código, o que aumenta a segurança do seu aplicativo.

[Documentação de referência da API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Pacote (Índice de Pacote do Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Configurar o ambiente local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instale a biblioteca de chaves de Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao seu cofre de chaves

Execute o seguinte comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para autorizar sua entidade de serviço para operações de exclusão, obtenção, lista e criação em chaves. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Esse comando depende das variáveis de ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` criadas nas etapas anteriores.

Para obter mais informações, confira [Atribuir uma política de acesso – CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Criar o código de exemplo

A biblioteca de clientes do Azure Key Vault para Python permite gerenciar chaves criptográficas e ativos relacionados, como certificados e segredos. O exemplo de código a seguir demonstra como criar um cliente, definir um segredo, recuperar um segredo e excluir um segredo.

Crie um arquivo chamado *kv_keys.py* que contenha esse código.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Verifique se o código na seção anterior está em um arquivo chamado *kv_keys.py*. Execute o código com o seguinte comando:

```terminal
python kv_keys.py
```

- Se você encontrar erros de permissões, verifique se executou o [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Executar novamente o código com o mesmo nome de chave pode produzir este erro: "A Chave (Conflito) <name> está em um estado excluído, mas recuperável". Use um nome de chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

No código anterior, o objeto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) usa as variáveis de ambiente que você criou para a entidade de serviço. Você fornece essa credencial sempre que cria um objeto de cliente de uma biblioteca do Azure, como [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python), juntamente com o URI do recurso com o qual você deseja trabalhar por meio desse cliente:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Salvar uma chave

Depois de obter o objeto de cliente para o cofre de chaves, você pode armazenar uma chave usando o método [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-): 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Você também pode usar [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) ou [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

Chamar um método `create` gera uma chamada à API REST do Azure para o cofre de chaves.

Ao processar a solicitação, o Azure autentica a identidade do chamador (a entidade de serviço) usando o objeto de credencial fornecido ao cliente.

Ele também verifica se o chamador está autorizado a executar a ação solicitada. Você concedeu essa autorização à entidade de serviço anteriormente usando o [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Recuperar uma chave

Para ler uma chave do Key Vault, use o método [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-):

```python
retrieved_key = client.get_key(keyName)
 ```

Você também pode verificar se a chave foi definida com o comando da CLI do Azure [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

### <a name="delete-a-key"></a>Excluir uma chave

Para excluir uma chave, use o método [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-):

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

O método `begin_delete_key` é assíncrono e retorna um objeto do instrumento de sondagem. Chamar o método `result` do instrumento de sondagem aguarda sua conclusão.

Você pode verificar se a chave foi excluída com o comando da CLI do Azure [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

Depois de excluída, uma chave permanece em um estado excluído, mas recuperável, por algum tempo. Se você executar o código novamente, use um nome de chave diferente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você também quiser experimentar [certificados](../certificates/quick-create-python.md) e [segredos](../secrets/quick-create-python.md), poderá reutilizar o Key Vault criado neste artigo.

Caso contrário, quando tiver concluído os recursos criados neste artigo, use o seguinte comando para excluir o grupo de recursos e todos os recursos que ele contém:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da o Cofre da Chave do Azure](../general/overview.md)
- [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- [Melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com o Key Vault](../general/authentication.md)
