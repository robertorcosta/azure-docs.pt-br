---
title: Início Rápido – Biblioteca de clientes Python do Azure Key Vault – gerenciar certificados
description: Saiba como criar, recuperar e excluir certificados de um Azure Key Vault usando a biblioteca de clientes do Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488619"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Início Rápido: Biblioteca de clientes de certificados do Azure Key Vault para Python

Introdução à biblioteca de clientes do Azure Key Vault para Python. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas. Usando o Key Vault para armazenar certificados, você evita armazenar certificados no código, o que aumenta a segurança do aplicativo.

[Documentação de referência da API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Pacote (Índice de Pacote do Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Configurar o ambiente local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instale a biblioteca de certificados do Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao seu cofre de chaves

Execute o seguinte comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para autorizar sua entidade de serviço para operações de obtenção, lista e criação em certificados.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Esse comando depende das variáveis de ambiente `KEY_VAULT_NAME` e `AZURE_CLIENT_ID` criadas nas etapas anteriores.

Para obter mais informações, confira [Atribuir uma política de acesso – CLI](../general/assign-access-policy-cli.md)

## <a name="create-the-sample-code"></a>Criar o código de exemplo

A biblioteca de clientes do Azure Key Vault para Python permite gerenciar certificados e ativos relacionados, como segredos e chaves criptográficas. O exemplo de código a seguir demonstra como criar um cliente, definir um segredo, recuperar um segredo e excluir um segredo.

Crie um arquivo chamado *kv_certificates.py* que contenha esse código.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Verifique se o código na seção anterior está em um arquivo chamado *kv_certificates.py*. Execute o código com o seguinte comando:

```terminal
python kv_certificates.py
```

- Se você encontrar erros de permissões, verifique se executou o [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Executar novamente o código com o mesmo nome de chave pode produzir este erro: "O Certificado (Conflito) <name> está em um estado excluído, mas recuperável". Use um nome de chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

No código anterior, o objeto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) usa as variáveis de ambiente que você criou para a entidade de serviço. Você fornece essa credencial sempre que cria um objeto de cliente de uma biblioteca do Azure, como [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python), juntamente com o URI do recurso com o qual você deseja trabalhar por meio desse cliente:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Salvar um certificado

Depois de obter o objeto de cliente para o cofre de chaves, você pode criar um certificado usando o método [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-): 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Aqui, o certificado requer uma política obtida com o método [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--).

Chamar um método `begin_create_certificate` gera uma chamada assíncrona à API REST do Azure para o cofre de chaves. A chamada assíncrona retorna um objeto do instrumento de sondagem. Para aguardar o resultado da operação, chame o método `result` do instrumentos de sondagem.

Ao processar a solicitação, o Azure autentica a identidade do chamador (a entidade de serviço) usando o objeto de credencial fornecido ao cliente.

Ele também verifica se o chamador está autorizado a executar a ação solicitada. Você concedeu essa autorização à entidade de serviço anteriormente usando o [comando `az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Para ler um certificado do Key Vault, use o método [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-):

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Verifique também se o certificado foi definido com o comando [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) da CLI do Azure.

### <a name="delete-a-certificate"></a>Excluir um certificado

Para excluir um certificado, use o método [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-):

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

O método `begin_delete_certificate` é assíncrono e retorna um objeto do instrumento de sondagem. Chamar o método `result` do instrumento de sondagem aguarda sua conclusão.

Você pode verificar se o certificado foi excluído com o comando da CLI do Azure [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

Depois de excluído, um certificado permanece em um estado excluído, mas recuperável, por algum tempo. Se você executar o código novamente, use outro nome de certificado.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você também quiser experimentar [segredos](../secrets/quick-create-python.md) e [chaves](../keys/quick-create-python.md), poderá reutilizar o Key Vault criado neste artigo.

Caso contrário, quando tiver concluído os recursos criados neste artigo, use o seguinte comando para excluir o grupo de recursos e todos os recursos que ele contém:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da o Cofre da Chave do Azure](../general/overview.md)
- [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- [Melhores práticas do Azure Key Vault](../general/best-practices.md)
- [Autenticar com o Key Vault](../general/authentication.md)
