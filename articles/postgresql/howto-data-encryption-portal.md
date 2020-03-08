---
title: Criptografia de dados para um servidor único para PostgreSQL para Azure usando o portal do Azure
description: Saiba como configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL usando o portal do Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6028f5e618b4b480a2259241fc2380f0200cebc6
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898360"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Criptografia de dados para um servidor único para PostgreSQL para Azure usando o portal do Azure

Saiba como usar o portal do Azure para configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL do banco de dado do Azure.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para CLI do Azure

* É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
* No Azure Key Vault, crie um cofre de chaves e a chave para usar para uma chave gerenciada pelo cliente.
* O cofre de chaves deve ter as seguintes propriedades para usar como uma chave gerenciada pelo cliente:
  * [Exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Limpar protegido](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos para usar como uma chave gerenciada pelo cliente:
  * Sem data de validade
  * Não desabilitado
  * Capaz de executar operações Get, wrap Key e Unwrap Key

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões corretas para operações de chave

1. Em Key Vault, selecione **políticas de acesso** > **Adicionar política de acesso**.

   ![Captura de tela de Key Vault, com políticas de acesso e adicionar política de acesso realçado](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecione **permissões de chave**e selecione **obter**, **encapsular**, **desencapsular**e a **entidade de segurança**, que é o nome do servidor PostgreSQL. Se a entidade de segurança do servidor não puder ser encontrada na lista de entidades de segurança existentes, você precisará registrá-la. Você será solicitado a registrar a entidade de segurança do servidor ao tentar configurar a criptografia de dados pela primeira vez e ela falhará.  

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Clique em **Salvar**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Definir criptografia de dados para um servidor único do banco de dado do Azure para PostgreSQL

1. No banco de dados do Azure para PostgreSQL, selecione **criptografia de dado** para configurar a chave gerenciada pelo cliente.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, com Data Encryption realçado](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Você pode selecionar um cofre de chaves e um par de chaves, ou inserir um identificador de chave.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, com as opções de criptografia realçadas](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Clique em **Salvar**.

4. Para garantir que todos os arquivos (incluindo arquivos temporários) sejam totalmente criptografados, reinicie o servidor.

## <a name="restore-or-create-a-replica-of-the-server"></a>Restaurar ou criar uma réplica do servidor

Depois que o banco de dados do Azure para PostgreSQL servidor único é criptografado com a chave gerenciada de um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica, ou por meio de uma operação de réplica (local/entre regiões). Portanto, para um servidor PostgreSQL criptografado, você pode usar as etapas a seguir para criar um servidor restaurado criptografado.

1. No servidor, selecione **visão geral** > **restaurar**.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, com visão geral e restauração realçadas](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Ou para um servidor habilitado para replicação, no cabeçalho **configurações** , selecione **replicação**.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, com replicação realçada](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Depois que a operação de restauração for concluída, o novo servidor criado será criptografado com a chave do servidor primário. No entanto, os recursos e as opções no servidor estão desabilitados e o servidor está inacessível. Isso impede qualquer manipulação de dados, porque a identidade do novo servidor ainda não recebeu permissão para acessar o cofre de chaves.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, com status inacessível realçado](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Para tornar o servidor acessível, revalide a chave no servidor restaurado. Selecione **criptografia de dados** > **chave de revalidação**.

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, pois a entidade de serviço do novo servidor precisa receber acesso ao cofre de chaves. Para gerar a entidade de serviço, selecione **chave de revalidação**, que mostrará um erro, mas gerará a entidade de serviço. Depois disso, consulte [estas etapas](#set-the-right-permissions-for-key-operations) anteriormente neste artigo.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, com a etapa de revalidação realçada](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Você precisará conceder ao cofre de chaves acesso ao novo servidor.

4. Depois de registrar a entidade de serviço, revalide a chave novamente e o servidor retoma sua funcionalidade normal.

   ![Captura de tela do banco de dados do Azure para PostgreSQL, mostrando a funcionalidade restaurada](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Usando um modelo de Azure Resource Manager para habilitar a criptografia de dados

Além do portal do Azure, você também pode habilitar a criptografia de dados no banco de dado do Azure para PostgreSQL servidor único usando modelos de Azure Resource Manager para um servidor novo e existente.

### <a name="for-a-new-server"></a>Para um novo servidor

Use um dos modelos de Azure Resource Manager criados previamente para provisionar o servidor com a criptografia de dados habilitada: [exemplo com criptografia de dados](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Este modelo de Azure Resource Manager cria um servidor único do banco de dados do Azure para PostgreSQL e usa o **keyvault** e a **chave** passados como parâmetros para habilitar a criptografia de dados no servidor.

### <a name="for-an-existing-server"></a>Para um servidor existente
Além disso, você pode usar os modelos de Azure Resource Manager para habilitar a criptografia de dados em seus servidores únicos do banco de dado do Azure para PostgreSQL.

* Passe o URI da chave de Azure Key Vault que você copiou anteriormente na propriedade `keyVaultKeyUri` no objeto de propriedades.

* Use *2020-01-01-Preview* como a versão da API.

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

 Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para PostgreSQL criptografia de dado de servidor único com chave gerenciada pelo cliente](concepts-data-encryption-postgresql.md).
