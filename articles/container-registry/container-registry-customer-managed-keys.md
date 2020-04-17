---
title: Criptografia em repouso com chaves gerenciadas pelo cliente
description: Saiba mais sobre criptografia no resto do registro de contêineres do Azure e como criptografar seu registro com uma chave gerenciada pelo cliente armazenada no Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461745"
---
# <a name="encryption-using-customer-managed-keys"></a>Criptografia usando chaves gerenciadas pelo cliente

Quando você armazena imagens e outros artefatos em um registro de contêiner do Azure, o Azure criptografa automaticamente o conteúdo do registro em repouso com [chaves gerenciadas pelo serviço](../security/fundamentals/encryption-atrest.md#data-encryption-models). Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando uma chave que você cria e gerencia no Azure Key Vault. Este artigo orienta você através das etapas usando o Azure CLI e o portal Azure.

A criptografia do lado do servidor com chaves gerenciadas pelo cliente é suportada por meio da integração com [o Azure Key Vault](../key-vault/general/overview.md). Você pode criar suas próprias chaves de criptografia e armazená-las em um cofre de chaves ou pode usar as APIs do Azure Key Vault para gerar chaves de criptografia. Com o Azure Key Vault, você também pode auditar o uso de chaves.

Esse recurso está disponível no nível de serviço de registro de contêineres **Premium.** Para obter informações sobre os níveis e limites do serviço de registro, consulte [As SKUs de registro de contêineres do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Este recurso está atualmente em visualização, e algumas [limitações](#preview-limitations) se aplicam. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>
   
## <a name="preview-limitations"></a>Limitações de visualização 

* No momento, você só pode habilitar esse recurso quando criar um registro.
* Depois de ativar uma chave gerenciada pelo cliente em um registro, você não pode desativá-la.
* [Atualmente,](container-registry-content-trust.md) a confiança de conteúdo não é suportada em um registro criptografado com uma chave gerenciada pelo cliente.
* Em um registro criptografado com uma chave gerenciada pelo cliente, os logs de execução de [Tarefas ACR](container-registry-tasks-overview.md) são atualmente retidos por apenas 24 horas. Se você precisar reter logs por um período mais longo, consulte orientação para [exportar e armazenar registros de execução de tarefas](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Pré-requisitos

Para usar as etapas do Azure CLI neste artigo, você precisa da versão 2.2.0 ou posterior do Azure CLI. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Habilitar a chave gerenciada pelo cliente - CLI

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, execute o comando [az group create][az-group-create] para criar um grupo de recursos para criar o cofre-chave, o registro de contêineres e outros recursos necessários.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída pelo usuário [para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) com o comando [az identity create.][az-identity-create] Essa identidade será usada pelo seu registro para acessar o serviço Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Na saída de comando, observe os `id` `principalId`seguintes valores: e . Você precisa desses valores em etapas posteriores para configurar o acesso do registro ao cofre de chaves.

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

Para conveniência, armazene esses valores em variáveis de ambiente:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Crie um cofre de chaves com [o az keyvault criar][az-keyvault-create] para armazenar uma chave gerenciada pelo cliente para criptografia de registro. 

Para evitar a perda de dados causada por exclusões acidentais de chave ou cofre de chaves, você deve habilitar as seguintes configurações: **Proteção de exclusão suave** e **purga**. O exemplo a seguir inclui parâmetros para essas configurações: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Adicionar política de acesso ao cofre chave

Configure uma diretiva para o cofre de chaves para que a identidade possa acessá-lo. No seguinte comando [az keyvault set-policy,][az-keyvault-set-policy] você passa o ID principal da identidade gerenciada que você criou, armazenada anteriormente em uma variável de ambiente. Defina as permissões de chave para **obter**, **desembrulharKey**e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Criar a chave e obter o ID da chave

Execute o [comando az keyvault create para][az-keyvault-key-create] criar uma chave no cofre de chaves.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Na saída de comando, anote o ID da chave, `kid`. Você usa este ID na próxima etapa:

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
Para conveniência, armazene esse valor em uma variável de ambiente:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Crie um registro com a chave gerenciada pelo cliente

Execute o comando [az acr create][az-acr-create] para criar um registro e habilitar a chave gerenciada pelo cliente. Passe o ID principal de identidade gerenciado e o ID da chave, armazenado anteriormente em variáveis de ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostrar o status da criptografia

Para mostrar se a criptografia de registro com uma chave gerenciada pelo cliente está ativada, execute o comando [az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Habilitar a chave gerenciada pelo cliente - portal

### <a name="create-a-managed-identity"></a>Crie uma identidade gerenciada

Crie uma identidade gerenciada atribuída pelo usuário [para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal Azure. Para etapas, consulte [Criar uma identidade atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Anote o nome do **recurso** da identidade gerenciada. Você precisa desse nome em etapas posteriores.

![Crie identidade gerenciada atribuída pelo usuário no portal Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Para obter etapas para criar um cofre-chave, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o portal Azure](../key-vault/secrets/quick-create-portal.md).

Ao criar um cofre de chaves para uma chave gerenciada pelo cliente, na guia **Basics,** você deve habilitar as seguintes configurações de proteção: **Exclusão suave** e **proteção de purga**. Essas configurações ajudam a evitar a perda de dados causada por exclusões acidentais de chave ou cofre de chaves.

![Criar cofre de chaves no portal Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Adicionar política de acesso ao cofre chave

Configure uma diretiva para o cofre de chaves para que a identidade possa acessá-lo.

1. Navegue até o cofre das chaves.
1. Selecione **configurações** > **As políticas de acesso > +Adicionar política de acesso**.
1. Selecione **as permissões de**tecla e **selecione Obter,** **Desembrulhar a tecla**e **a chave de envoltório**.
1. Selecione **Selecionar o principal** e selecione o nome do recurso da sua identidade gerenciada atribuída pelo usuário.  
1. Selecione **Adicionar**e, em seguida, **selecione Salvar**.

![Crie a política de acesso ao cofre-chave](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Chave Create

1. Navegue até o cofre das chaves.
1. Selecione **Teclas de** > **configuração**.
1. Selecione **+Gerar/Importar** e digite um nome exclusivo para a chave.
1. Aceite os valores padrão restantes e selecione **Criar**.
1. Após a criação, selecione a tecla e tome nota da versão de tecla atual.

### <a name="create-azure-container-registry"></a>Crie um registro de contêiner do Azure

1. Selecione Criar um registro**de contêineres** > **de contêineres de** **recursos** > .
1. Na guia **Básico,** selecione ou crie um grupo de recursos e digite um nome de registro. Em **SKU,** selecione **Premium**.
1. Na guia **Criptografia,** na **chave gerenciada pelo cliente,** **selecione Ativado**.
1. Em **Identidade,** selecione a identidade gerenciada que você criou.
1. Na **tecla Criptografia,** **selecione Selecionar no Cofre de Chaves**.
1. Na **tecla Select da janela Azure Key Vault,** selecione o cofre de chaves, a chave e a versão criada na seção anterior.
1. Na guia **Criptografia,** selecione **'Revisar + criar'.**
1. Selecione **Criar** para implantar a instância de registro.

![Criar registro de contêiner no portal do Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Para ver o status de criptografia do seu registro no portal, navegue até o seu registro. Em **Configurações,** selecione **Criptografia (Visualização)**.

## <a name="enable-customer-managed-key---template"></a>Habilitar a chave gerenciada pelo cliente - modelo

Você também pode usar um modelo de Gerenciador de recursos para criar um registro e habilitar criptografia com uma chave gerenciada pelo cliente. 

O modelo a seguir cria um novo registro de contêiner e uma identidade gerenciada atribuída pelo usuário. Copie o conteúdo a seguir para um novo arquivo `CMKtemplate.json`e salve-o usando um nome de arquivo, como .

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

Siga as etapas das seções anteriores para criar os seguintes recursos:

* Cofre de chaves, identificado pelo nome
* Chave do cofre, identificada pela chave ID

Execute o seguinte [comando az group create para][az-group-deployment-create] criar o registro usando o arquivo de modelo anterior. Quando indicado, forneça um novo nome de registro e nome de identidade gerenciado, bem como o nome do cofre chave e o ID de chave que você criou. 

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

### <a name="show-encryption-status"></a>Mostrar o status da criptografia

Para mostrar o status da criptografia de registro, execute o comando [az acr encryption show-status][az-acr-encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Use o registro

Depois de habilitar um registro para criptografar dados usando uma chave gerenciada pelo cliente, você pode executar as mesmas operações de registro que você executa em um registro que não está criptografado com uma chave gerenciada pelo cliente. Por exemplo, você pode autenticar com o registro e empurrar imagens do Docker. Veja comandos de exemplo em [Empurrar e puxar uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Chave giratória

Você pode girar uma chave gerenciada pelo cliente no Azure Key Vault de acordo com suas políticas de conformidade. Crie uma nova chave e atualize o registro para criptografar dados usando a nova chave. Você pode executar essas etapas usando o Azure CLI ou no portal.

Por exemplo, execute o comando [az keyvault keykey create][az-keyvault-key-create] para criar uma nova chave:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Em seguida, execute o comando [de chave giratória de criptografia az acr,][az-acr-encryption-rotate-key] passando o novo ID de chave e o ID principal da identidade gerenciada que você configurou anteriormente:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Revogar chave

Revogue a chave de criptografia gerenciada pelo cliente alterando a política de acesso no cofre de chaves ou excluindo a chave. Por exemplo, use o comando [az keyvault delete-policy][az-keyvault-delete-policy] para alterar a política de acesso da identidade gerenciada usada pelo seu registro. Por exemplo:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A revogação da chave bloqueia efetivamente o acesso a todos os dados do registro, uma vez que o registro não pode acessar a chave de criptografia. Se o acesso à chave estiver ativado ou a chave excluída for restaurada, seu registro escolherá a chave para que você possa acessar novamente os dados do registro criptografado.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [criptografia em repouso no Azure](../security/fundamentals/encryption-atrest.md).
* Saiba mais sobre políticas de acesso e como [garantir o acesso a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).
* Para fornecer feedback sobre as chaves gerenciadas pelo cliente para o Registro de Contêineres do Azure, visite o site do [ACR GitHub](https://aka.ms/acr/issues).


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
