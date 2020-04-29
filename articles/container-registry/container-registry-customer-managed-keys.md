---
title: Criptografia em repouso com chaves gerenciadas pelo cliente
description: Saiba mais sobre a criptografia em repouso do registro de contêiner do Azure e como criptografar o registro com uma chave gerenciada pelo cliente armazenada em Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461745"
---
# <a name="encryption-using-customer-managed-keys"></a>Criptografia usando chaves gerenciadas pelo cliente

Quando você armazena imagens e outros artefatos em um registro de contêiner do Azure, o Azure criptografa automaticamente o conteúdo do registro em repouso com [chaves gerenciadas pelo serviço](../security/fundamentals/encryption-atrest.md#data-encryption-models). Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando uma chave que você cria e gerencia no Azure Key Vault. Este artigo orienta você pelas etapas usando o CLI do Azure e o portal do Azure.

Há suporte para a criptografia do lado do servidor com chaves gerenciadas pelo cliente por meio da integração com o [Azure Key Vault](../key-vault/general/overview.md). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com Azure Key Vault, você também pode auditar o uso da chave.

Esse recurso está disponível na camada de serviço do registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Este recurso está atualmente em visualização e algumas [limitações](#preview-limitations) se aplicam. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>
   
## <a name="preview-limitations"></a>Limitações de visualização 

* Atualmente, você pode habilitar esse recurso somente quando você cria um registro.
* Depois de habilitar uma chave gerenciada pelo cliente em um registro, você não pode desabilitá-la.
* No momento, não há suporte para [confiança de conteúdo](container-registry-content-trust.md) em um registro criptografado com uma chave gerenciada pelo cliente.
* Em um registro criptografado com uma chave gerenciada pelo cliente, os logs de execução de [tarefas de ACR](container-registry-tasks-overview.md) são atualmente mantidos por apenas 24 horas. Se você precisar manter os logs por um período mais longo, consulte Diretrizes para [exportar e armazenar logs de execução de tarefa](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Pré-requisitos

Para usar as etapas de CLI do Azure neste artigo, você precisa CLI do Azure versão 2.2.0 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Habilitar a CLI de chave gerenciada pelo cliente

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, execute o comando [AZ Group Create][az-group-create] para criar um grupo de recursos para criar o cofre de chaves, o registro de contêiner e outros recursos necessários.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma [identidade gerenciada atribuída pelo usuário para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) com o comando [AZ Identity Create][az-identity-create] . Essa identidade será usada pelo registro para acessar o serviço de Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Na saída do comando, anote os seguintes valores: `id` e. `principalId` Você precisará desses valores em etapas posteriores para configurar o acesso ao registro para o cofre de chaves.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Para sua conveniência, armazene esses valores em variáveis de ambiente:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Crie um cofre de chaves com [AZ keyvault Create][az-keyvault-create] para armazenar uma chave gerenciada pelo cliente para criptografia do registro. 

Para evitar a perda de dados causada por exclusões de chave ou cofre de chaves acidentais, você deve habilitar as seguintes configurações: **exclusão reversível** e **limpeza de proteção**. O exemplo a seguir inclui parâmetros para essas configurações: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Adicionar política de acesso do cofre de chaves

Configure uma política para o cofre de chaves para que a identidade possa acessá-la. No comando [AZ keyvault Set-Policy][az-keyvault-set-policy] a seguir, você passa a ID da entidade de segurança gerenciada que você criou, armazenada anteriormente em uma variável de ambiente. Defina as permissões de chave para **Get**, **unwrapKey**e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Criar chave e obter ID da chave

Execute o comando [AZ keyvault Key Create][az-keyvault-key-create] para criar uma chave no cofre de chaves.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Na saída do comando, anote a ID da chave, `kid`. Você usará essa ID na próxima etapa:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Para sua conveniência, armazene esse valor em uma variável de ambiente:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um registro com chave gerenciada pelo cliente

Execute o comando [AZ ACR Create][az-acr-create] para criar um registro e habilitar a chave gerenciada pelo cliente. Passe a ID da entidade de segurança gerenciada e a ID da chave, armazenadas anteriormente em variáveis de ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostrar status da criptografia

Para mostrar se a criptografia do registro com uma chave gerenciada pelo cliente está habilitada, execute o comando [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Habilitar chave gerenciada pelo cliente – Portal

### <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Crie uma [identidade gerenciada atribuída pelo usuário para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal do Azure. Para obter as etapas, consulte [criar uma identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Anote o nome do **recurso** da identidade gerenciada. Você precisa desse nome em etapas posteriores.

![Criar identidade gerenciada atribuída pelo usuário no portal do Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Para obter as etapas para criar um cofre de chaves, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o portal do Azure](../key-vault/secrets/quick-create-portal.md).

Ao criar um cofre de chaves para uma chave gerenciada pelo cliente, na guia **noções básicas** , você deve habilitar as seguintes configurações de proteção: **exclusão reversível** e **proteção de limpeza**. Essas configurações ajudam a evitar a perda de dados causada por exclusões de chave ou cofre de chaves acidentais.

![Criar um cofre de chaves no portal do Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Adicionar política de acesso do cofre de chaves

Configure uma política para o cofre de chaves para que a identidade possa acessá-la.

1. Navegue até o cofre de chaves.
1. Selecione **configurações** > **políticas de acesso > + adicionar política de acesso**.
1. Selecione **permissões de chave**e selecione **obter**, **desencapsular chave**e **encapsular chave**.
1. Selecione **selecionar entidade de segurança** e selecione o nome do recurso de sua identidade gerenciada atribuída pelo usuário.  
1. Selecione **Adicionar**e, em seguida, selecione **salvar**.

![Criar política de acesso do cofre de chaves](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Chave Create

1. Navegue até o cofre de chaves.
1. Selecione **configurações** > **chaves**.
1. Selecione **+ gerar/importar** e insira um nome exclusivo para a chave.
1. Aceite os valores padrão restantes e selecione **criar**.
1. Após a criação, selecione a chave e anote a versão da chave atual.

### <a name="create-azure-container-registry"></a>Crie um registro de contêiner do Azure

1. Selecione **criar um recurso** > **contêineres** > **registro de contêiner**.
1. Na guia **noções básicas** , selecione ou crie um grupo de recursos e insira um nome de registro. Em **SKU**, selecione **Premium**.
1. Na guia **criptografia** , em **chave gerenciada pelo cliente**, selecione **habilitado**.
1. Em **identidade**, selecione a identidade gerenciada que você criou.
1. Em **chave de criptografia**, selecione **selecionar em Key Vault**.
1. Na janela **selecionar chave de Azure Key Vault** , selecione o cofre de chaves, a chave e a versão que você criou na seção anterior.
1. Na guia **criptografia** , selecione **revisão + criar**.
1. Selecione **criar** para implantar a instância do registro.

![Criar registro de contêiner no portal do Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Para ver o status de criptografia do registro no portal, navegue até o registro. Em **configurações**, selecione **criptografia (versão prévia)**.

## <a name="enable-customer-managed-key---template"></a>Habilitar o modelo de chave gerenciado pelo cliente

Você também pode usar um modelo do Resource Manager para criar um registro e habilitar a criptografia com uma chave gerenciada pelo cliente. 

O modelo a seguir cria um novo registro de contêiner e uma identidade gerenciada atribuída pelo usuário. Copie o conteúdo a seguir em um novo arquivo e salve-o usando um nome `CMKtemplate.json`de arquivo, como.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Siga as etapas nas seções anteriores para criar os seguintes recursos:

* Cofre de chaves, identificado pelo nome
* Chave do Key Vault, identificada pela ID da chave

Execute o comando [AZ Group Deployment Create][az-group-deployment-create] a seguir para criar o registro usando o arquivo de modelo anterior. Quando indicado, forneça um novo nome de registro e nome de identidade gerenciada, bem como o nome do cofre de chaves e a ID de chave que você criou. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Mostrar status da criptografia

Para mostrar o status da criptografia do registro, execute o comando [AZ ACR Encryption show-status] [AZ-ACR-Encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Usar o registro

Depois de habilitar um registro para criptografar dados usando uma chave gerenciada pelo cliente, você pode executar as mesmas operações de registro que você executa em um registro que não está criptografado com uma chave gerenciada pelo cliente. Por exemplo, você pode autenticar com o registro e enviar por push imagens do Docker. Consulte os comandos de exemplo em [enviar por push e efetuar pull de uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Tecla de rotação

Você pode girar uma chave gerenciada pelo cliente em Azure Key Vault de acordo com suas políticas de conformidade. Crie uma nova chave e, em seguida, atualize o registro para criptografar dados usando a nova chave. Você pode executar essas etapas usando o CLI do Azure ou no Portal.

Por exemplo, execute o comando [AZ keyvault Key Create][az-keyvault-key-create] para criar uma nova chave:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Em seguida, execute o comando [AZ ACR Encryption Rotate-Key][az-acr-encryption-rotate-key] , passando a nova ID de chave e a ID principal da identidade gerenciada que você configurou anteriormente:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Revogar chave

Revogue a chave de criptografia gerenciada pelo cliente alterando a política de acesso no cofre de chaves ou excluindo a chave. Por exemplo, use o comando [AZ keyvault excluir-Policy][az-keyvault-delete-policy] para alterar a política de acesso da identidade gerenciada usada pelo registro. Por exemplo:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A revogação da chave efetivamente bloqueia o acesso a todos os dados do registro, já que o registro não pode acessar a chave de criptografia. Se o acesso à chave estiver habilitado ou a chave excluída for restaurada, o registro selecionará a chave para que você possa acessar novamente os dados de registro criptografados.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a criptografia em repouso no Azure](../security/fundamentals/encryption-atrest.md).
* Saiba mais sobre as políticas de acesso e como [proteger o acesso a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).
* Para fornecer comentários sobre chaves gerenciadas pelo cliente para o registro de contêiner do Azure, visite o [site GitHub do ACR](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
