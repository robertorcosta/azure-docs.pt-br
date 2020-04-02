---
title: Criptografia de dados - Azure CLI - para Banco de Dados Azure para PostgreSQL - Servidor único
description: Saiba como configurar e gerenciar a criptografia de dados para o seu banco de dados Azure para servidor PostgreSQL Single usando o Cli do Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c778130a95be1aa0afe81241130b7daf165360c4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522128"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Criptografia de dados para banco de dados Azure para servidor PostgreSQL Único usando o Cli do Azure

Aprenda a usar o Azure CLI para configurar e gerenciar a criptografia de dados para o seu banco de dados Azure para servidor PostgreSQL Single.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para Azure CLI

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* Crie um cofre de chaves e uma chave para usar para uma chave gerenciada pelo cliente. Também habilite a proteção contra expurgo e a exclusão suave no cofre da chave.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* No Azure Key Vault criado, crie a chave que será usada para a criptografia de dados do Banco de Dados Do Azure para servidor PostgreSQL Single.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Para usar um cofre de chaves existente, ele deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:
  * [Exclusão suave](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Expurgo protegido](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos para usar como uma chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Executar **operações get,** **wrap** e **desembrulhar**

## <a name="set-the-right-permissions-for-key-operations"></a>Defina as permissões certas para operações-chave

1. Existem duas maneiras de obter a identidade gerenciada para o seu banco de dados Azure para servidor PostgreSQL Single.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Crie um novo banco de dados Azure para o servidor MySQL com uma identidade gerenciada.

    ```azurecli-interactive
    az postgres server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Atualize um banco de dados Azure existente para o servidor MySQL para obter uma identidade gerenciada.

    ```azurecli-interactive
    az postgres server update –name <server name>  -g <resoure_group> --assign-identity
    ```

2. Defina as **permissões key** **(Get**, **Wrap**, **Desembrulhe)** para o **Principal**, que é o nome do servidor PostgreSQL Single.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Defina a criptografia de dados para o Banco de Dados Do Azure para servidor único PostgreSQL

1. Habilite a criptografia de dados para o banco de dados Do Zure para servidor PostgreSQL Single usando a chave criada no Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Url da chave:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Usando criptografia de dados para restaurar ou replicar servidores

Depois que o banco de dados Do Zure para o servidor PostgreSQL Single é criptografado com a chave gerenciada do cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia através de uma operação local ou de georestauração, ou através de uma operação de réplica (local/região transversal). Assim, para um servidor postgresql único criptografado, você pode usar as seguintes etapas para criar um servidor restaurado criptografado.

### <a name="creating-a-restoredreplica-server"></a>Criando um servidor restaurado/replica

  *  [Criar um servidor de restauração](howto-restore-server-cli.md) 
  *  [Criar um servidor de réplica de leitura](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Uma vez que o servidor seja restaurado, revalide a criptografia de dados do servidor restaurado

    ```azurecli-interactive
    az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Recurso adicional para a chave que está sendo usada para o Banco de Dados Azure para servidor PostgreSQL Único

### <a name="get-the-key-used"></a>Use a chave

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Liste a chave usada

    ```azurecli-interactive
    az postgres server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Solte a chave que está sendo usada

    ```azurecli-interactive
    az postgres server key delete -g <resource_group> --kid <key url> 
    ```
## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Usando um modelo do Azure Resource Manager para permitir a criptografia de dados

Além do portal Azure, você também pode habilitar a criptografia de dados em seu banco de dados Azure para servidor único PostgreSQL usando modelos do Azure Resource Manager para servidor novo e existente.

### <a name="for-a-new-server"></a>Para um novo servidor

Use um dos modelos pré-criados do Azure Resource Manager para provisionar o servidor com criptografia de dados ativada: [Exemplo com criptografia de dados](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Este modelo do Azure Resource Manager cria um banco de dados Azure para servidor PostgreSQL Single e usa o **KeyVault** e **o Key** passed como parâmetros para habilitar a criptografia de dados no servidor.

### <a name="for-an-existing-server"></a>Para um servidor existente
Além disso, você pode usar os modelos do Azure Resource Manager para habilitar a criptografia de dados no banco de dados Azure existente para servidores PostgreSQL Single.

* Passe o ID de recurso da chave Azure Key `Uri` Vault que você copiou anteriormente sob a propriedade no objeto propriedades.

* Use *a pré-visualização 2020-01-01* como a versão da API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

 Para saber mais sobre criptografia de dados, consulte [O Banco de Dados Do Azure para criptografia de dados do servidor Único PostgreSQL com a chave gerenciada pelo cliente](concepts-data-encryption-postgresql.md).
