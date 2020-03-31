---
title: Gerenciar chaves de conta de armazenamento com o Azure Key Vault e o Azure CLI
description: As chaves da conta de armazenamento fornecem uma integração perfeita entre o Azure Key Vault e o acesso baseado em chaves a uma conta de armazenamento Do Zure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 104f3423b07eaa3269ffccc054cd2f779bbdabf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199811"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gerenciar chaves de conta de armazenamento com key vault e o Azure CLI

Uma conta de armazenamento Azure usa credenciais que incluem um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de uma chave criptográfica. O Key Vault gerencia as chaves da conta de armazenamento armazenando-as como [segredos do Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Você pode usar o recurso de chave de armazenamento gerenciado do Key Vault para listar (sincronizar) chaves com uma conta de armazenamento Azure e regenerar (girar) as chaves periodicamente. Você pode gerenciar chaves para contas de armazenamento e contas de armazenamento Classic.

Ao usar o recurso de chave da conta de armazenamento gerenciado, considere os seguintes pontos:

- Os valores-chave nunca são devolvidos em resposta a um chamador.
- Apenas o Key Vault deve gerenciar as chaves da sua conta de armazenamento. Não gerencie as chaves você mesmo e evite interferir nos processos do Key Vault.
- Apenas um único objeto do Key Vault deve gerenciar as chaves da conta de armazenamento. Não permita o gerenciamento de chaves de vários objetos.
- Você pode solicitar o Key Vault para gerenciar sua conta de armazenamento com um diretor de usuário, mas não com um diretor de serviço.
- Regenerar chaves usando apenas o Key Vault. Não regenere manualmente as chaves da conta de armazenamento.

Recomendamos o uso da integração do Azure Storage com o Azure Active Directory (Azure AD), o serviço de gerenciamento de identidade e acesso baseado na nuvem da Microsoft. A integração do Azure AD está disponível para [blobs e filas do Azure](../storage/common/storage-auth-aad.md)e fornece acesso baseado em token OAuth2 ao Azure Storage (assim como o Azure Key Vault).

O Azure AD permite que você autentique seu aplicativo cliente usando um aplicativo ou identidade de usuário, em vez de credenciais de conta de armazenamento. Você pode usar [uma identidade gerenciada do Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando você executar no Azure. As identidades gerenciadas removem a necessidade de autenticação do cliente e armazenamento de credenciais dentro ou com seu aplicativo.

O Azure AD usa o RBAC (Role-Based Access Control, controle de acesso baseado em função) para gerenciar a autorização, que também é suportada pelo Key Vault.

## <a name="service-principal-application-id"></a>ID do aplicativo principal do serviço

Um inquilino Azure AD fornece cada aplicativo registrado com um [diretor de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object). O principal serviço serve como id de aplicativo, que é usado durante a configuração de autorização para acesso a outros recursos do Azure via RBAC.

Key Vault é um aplicativo da Microsoft pré-registrado em todos os inquilinos do Azure AD. O Key Vault está registrado o mesmo ID de aplicativo em cada nuvem do Azure.

| Locatários | Nuvem | ID do aplicativo |
| --- | --- | --- |
| AD do Azure | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| AD do Azure | Público do Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, você deve primeiro fazer o seguinte:

- [Instale a CLI do Azure](/cli/azure/install-azure-cli).
- [Criar um cofre de chaves](quick-create-cli.md)
- [Crie uma conta de armazenamento Azure.](../storage/common/storage-account-create.md?tabs=azure-cli) O nome da conta de armazenamento deve usar apenas letras e números minúsculos. O comprimento do nome deve ser entre 3 e 24 caracteres.
      
## <a name="manage-storage-account-keys"></a>Gerenciar chaves da conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Autue sua sessão CLI do Azure usando os comandos [de login az.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Dê acesso ao Key Vault à sua conta de armazenamento

Use o comando a [atribuição de função](/cli/azure/role/assignment?view=azure-cli-latest) Azure CLI az criar o comando para dar ao Key Vault acesso à sua conta de armazenamento. Forneça ao comando os seguintes valores de parâmetro:

- `--role`: Passe a função rBAC "Função de serviço do operador chave da conta de armazenamento". Essa função limita o escopo de acesso à sua conta de armazenamento. Para obter uma conta de armazenamento clássica, passe "Classic Storage Account Key Operator Service Role" em vez disso.
- `--assignee-object-id`: Passe o valor "93c27d83-f79b-4cb2-8dd4-4aa716542e74", que é o Object ID do Key Vault na nuvem pública do Azure. (Para obter o ID do Objeto para Cofre de Chaves na nuvem do Governo Do Azure, consulte [ID de aplicativo principal do serviço](#service-principal-application-id).)
- `--scope`: Passe o ID de recurso da `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`sua conta de armazenamento, que está no formulário . Para encontrar seu ID de assinatura, use o comando Azure CLI [az account list;](/cli/azure/account?view=azure-cli-latest#az-account-list) para encontrar o nome da sua conta de armazenamento e o grupo de recursos da conta de armazenamento, use o comando Azure CLI [az storage account list.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Crie uma conta de armazenamento gerenciada do Cofre chave

 Crie uma conta de armazenamento gerenciado do Key Vault usando o comando [azure CLI az keyvault storage.](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) Estabeleça um período de regeneração de 90 dias. Após 90 dias, key `key1` vault regenera e `key2` troca `key1`a chave ativa de . `key1`é então marcado como a chave ativa. Forneça ao comando os seguintes valores de parâmetro:

- `--vault-name`: Passe o nome do seu cofre de chaves. Para encontrar o nome do seu cofre de chaves, use o comando Azure CLI [az keyvault list.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)
- `-n`: Passe o nome da sua conta de armazenamento. Para encontrar o nome da sua conta de armazenamento, use o comando Azure CLI [az storage account list.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
- `--resource-id`: Passe o ID de recurso da `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`sua conta de armazenamento, que está no formulário . Para encontrar seu ID de assinatura, use o comando Azure CLI [az account list;](/cli/azure/account?view=azure-cli-latest#az-account-list) para encontrar o nome da sua conta de armazenamento e o grupo de recursos da conta de armazenamento, use o comando Azure CLI [az storage account list.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokens de assinatura de acesso compartilhado

Você também pode pedir ao Key Vault para gerar tokens de assinatura de acesso compartilhado. Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode conceder aos clientes acesso a recursos em sua conta de armazenamento sem compartilhar as chaves de sua conta. Uma assinatura de acesso compartilhado fornece uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer as chaves de sua conta.

Os comandos desta seção completam as seguintes ações:

- Defina uma definição `<YourSASDefinitionName>`de assinatura de acesso compartilhado de conta . A definição está definida em uma `<YourStorageAccountName>` conta de `<YourKeyVaultName>`armazenamento gerenciado do Key Vault no cofre principal.
- Crie um token de assinatura de acesso compartilhado de conta para os serviços Blob, File, Table e Queue. O token é criado para os tipos de recursos Serviço, Contêiner e Objeto. O token é criado com todas as permissões, sobre https e com as datas de início e término especificadas.
- Defina uma definição de assinatura de acesso compartilhada do Key Vault no cofre. A definição tem o modelo URI do token de assinatura de acesso compartilhado que foi criado. A definição tem o tipo `account` de assinatura de acesso compartilhado e é válida por N dias.
- Verifique se a assinatura de acesso compartilhado foi salva no cofre da chave como um segredo.

### <a name="create-a-shared-access-signature-token"></a>Crie um token de assinatura de acesso compartilhado

Crie uma definição de assinatura de acesso compartilhado usando o comando Azure CLI [az storage generate-sas.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Esta operação `storage` requer `setsas` as permissões.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Depois que a operação é executada com sucesso, copie a saída.

```console
"se=2020-01-01&sp=***"
```

Esta saída será passada `--template-id` para o parâmetro na próxima etapa.

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso compartilhado

Use o comando Azure CLI [az keyvault storage sas-definition create,](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) `--template-id` passando a saída da etapa anterior para o parâmetro, para criar uma definição de assinatura de acesso compartilhado.  Você pode fornecer o nome `-n` de sua escolha para o parâmetro.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifique a definição de assinatura de acesso compartilhado

Você pode verificar se a definição de assinatura de acesso compartilhado foi armazenada em seu cofre de chaves usando a lista secreta do cofre [de chave Azure](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) CLI az e comandos [de show secreto do az keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

Primeiro, encontre a definição de assinatura de acesso compartilhado em seu cofre de chaves usando o comando [az keyvault secret list.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

O segredo correspondente à sua definição de SAS terá essas propriedades:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Agora você pode usar o comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e a `id` propriedade para visualizar o conteúdo desse segredo.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

A saída deste comando mostrará sua`value`seqüência de definição SAS como .


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [chaves, segredos e certificados.](https://docs.microsoft.com/rest/api/keyvault/)
- Revisar artigos no blog da equipe do [Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Consulte a documentação de referência [de armazenamento do cofre de chave az.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)
