---
title: Criptografia de disco de chave gerenciada pelo cliente para o Azure HDInsight
description: Este artigo descreve como usar sua própria chave de criptografia de Azure Key Vault para criptografar dados armazenados em discos gerenciados em clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536294"
---
# <a name="customer-managed-key-disk-encryption"></a>Criptografia de disco de chave gerenciada pelo cliente

O Azure HDInsight dá suporte à criptografia de chave gerenciada pelo cliente para dados em discos gerenciados e discos de recursos anexados às máquinas virtuais do cluster HDInsight. Esse recurso permite que você use Azure Key Vault para gerenciar as chaves de criptografia que protegem dados em repouso em seus clusters HDInsight.

Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar chaves gerenciadas pelo cliente para o HDInsight, forneça as chaves de criptografia para o HDInsight usar e gerenciar essas chaves usando Azure Key Vault.

Este documento não aborda dados armazenados em sua conta de armazenamento do Azure. Para obter mais informações sobre a criptografia de armazenamento do Azure, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md). Seus clusters podem ter uma ou mais contas de armazenamento do Azure anexadas em que as chaves de criptografia também podem ser gerenciadas pela Microsoft ou gerenciadas pelo cliente, mas o serviço de criptografia é diferente.

## <a name="introduction"></a>Introdução

A criptografia de chave gerenciada pelo cliente é um processo de uma etapa manipulado durante a criação do cluster sem custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

O disco de recursos e os discos gerenciados em cada nó do cluster são criptografados com uma DEK (chave de criptografia de dados simétrica). A DEK é protegida usando a KEK (Chave de Criptografia de Chave) do seu cofre de chaves. Os processos de criptografia e descriptografia são realizados inteiramente pelo Azure HDInsight.

Se o Firewall do cofre de chaves estiver habilitado no cofre de chaves em que a chave de criptografia de disco estiver armazenada, os endereços IP do provedor de recursos regionais do HDInsight para a região em que o cluster será implantado deverão ser adicionados à configuração do firewall do cofre de chaves. Isso é necessário porque o HDInsight não é um serviço de cofre de chaves do Azure confiável.

Você pode usar o portal do Azure ou a CLI do Azure para gorar as chaves com segurança no cofre de chaves. Quando uma tecla gira, o cluster HDInsight começa a usar a nova chave em minutos. Habilite os recursos de proteção de chave de [exclusão reversível](../key-vault/general/overview-soft-delete.md) para proteger contra cenários de ransomware e exclusão acidental. Não há suporte para cofres de chaves sem esse recurso de proteção.

|Tipo de cluster |Disco do sistema operacional (disco gerenciado) |Disco de dados (disco gerenciado) |Disco de dados temporário (SSD local) |
|---|---|---|---|
|Kafka, HBase com gravações aceleradas|[Criptografia SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Criptografia SSE + criptografia CMK opcional|Criptografia CMK opcional|
|Todos os outros clusters (Spark, interativo, Hadoop, HBase sem gravações aceleradas)|Criptografia SSE|N/D|Criptografia CMK opcional|

## <a name="get-started-with-customer-managed-keys"></a>Começar a usar chaves gerenciadas pelo cliente

Para criar um cluster HDInsight habilitado para chave gerenciada pelo cliente, percorreremos as seguintes etapas:

1. Criar identidades gerenciadas para recursos do Azure
1. Criar Azure Key Vault
1. Chave Create
1. Criar política de acesso
1. Criar cluster HDInsight com a chave gerenciada pelo cliente habilitada
1. Girando a chave de criptografia

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades gerenciadas para recursos do Azure

Crie uma identidade gerenciada atribuída pelo usuário para se autenticar no Key Vault.

Consulte [criar uma identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para etapas específicas. Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](hdinsight-managed-identities.md). Certifique-se de salvar a ID do recurso de identidade gerenciada para quando você a adicionar à política de acesso do Key Vault.

## <a name="create-azure-key-vault"></a>Criar Azure Key Vault

Crie um cofre da chave. Consulte [criar Azure Key Vault](../key-vault/secrets/quick-create-portal.md) para obter etapas específicas.

O HDInsight é compatível apenas com o Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para o Azure Key Vault. Lembre-se de que o cofre de chaves deve ter a **exclusão reversível** habilitada. Para obter mais informações sobre como importar as chaves existentes, visite [Sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Chave Create

1. Em seu novo cofre de chaves, navegue até **configurações** > **chaves** > **+ gerar/importar**.

    ![Gerar uma nova chave no Azure Key Vault](./media/disk-encryption/create-new-key.png "Gerar uma nova chave no Azure Key Vault")

1. Forneça um nome e, em seguida, selecione **criar**. Mantenha o **tipo de chave** padrão do **RSA**.

    ![gera o nome da chave](./media/disk-encryption/create-key.png "Gerar nome da chave")

1. Ao retornar para a página **chaves** , selecione a chave que você criou.

    ![lista de chaves do Key Vault](./media/disk-encryption/key-vault-key-list.png)

1. Selecione a versão para abrir a página de **versão da chave** . Ao usar sua própria chave para a criptografia do cluster HDInsight, você precisa fornecer o URI da chave. Copie o **Identificador de chave** e salve-o em algum local até que esteja pronto para criar o cluster.

    ![obter identificador de chave](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Criar política de acesso

1. Em seu novo cofre de chaves, navegue até **configurações** > **políticas** > de acesso **+ Adicionar política de acesso**.

    ![Criar uma nova política de acesso do Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Na página **Adicionar política de acesso** , forneça as seguintes informações:

    |Propriedade |Descrição|
    |---|---|
    |Permissões de chave|Selecione **obter**, **desencapsular chave**e **encapsular chave**.|
    |Permissões de segredo|Selecione **obter**, **definir**e **excluir**.|
    |Selecionar entidade|Selecione a identidade gerenciada atribuída pelo usuário que você criou anteriormente.|

    ![Definir Selecionar Entidade de Segurança para a política de acesso do Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecione **Adicionar**.

1. Selecione **Salvar**.

    ![Salvar política de acesso de Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Criar cluster com criptografia de disco de chave gerenciada pelo cliente

Agora você está pronto para criar um novo cluster do HDInsight. A chave gerenciada pelo cliente só pode ser aplicada a novos clusters durante a criação do cluster. A criptografia não pode ser removida de clusters de chaves gerenciados pelo cliente, e a chave gerenciada pelo cliente não pode ser adicionada a clusters existentes.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Durante a criação do cluster, forneça o **identificador de chave**completo, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave.

![Criar novo cluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir mostra como usar CLI do Azure para criar um novo cluster de Apache Spark com a criptografia de disco habilitada. Para obter mais informações, consulte [CLI do Azure AZ hdinsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

O exemplo a seguir mostra como usar um modelo de Azure Resource Manager para criar um novo cluster de Apache Spark com a criptografia de disco habilitada. Para obter mais informações, consulte [o que são modelos de ARM?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

Este exemplo usa o PowerShell para chamar o modelo.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

O conteúdo do modelo de gerenciamento de recursos `azuredeploy.json`,:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia

Pode haver cenários em que você talvez queira alterar as chaves de criptografia usadas pelo cluster HDInsight depois que ele tiver sido criado. Isso pode ser facilmente por meio do Portal. Para essa operação, o cluster deve ter acesso à chave atual e à nova chave pretendida, caso contrário, a operação de rotação de teclas falhará.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para girar a chave, você precisa do URI do cofre de chaves de base. Depois de fazer isso, vá para a seção de propriedades do cluster HDInsight no portal e clique em **alterar chave** em **URL da chave de criptografia do disco**. Insira na nova URL de chave e envie para girar a chave.

![girar chave de criptografia de disco](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir mostra como girar a chave de criptografia de disco para um cluster HDInsight existente. Para obter mais informações, consulte [CLI do Azure AZ hdinsight Rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Perguntas frequentes para criptografia de chave gerenciada pelo cliente

**Como o cluster HDInsight acessa meu cofre de chaves?**

O HDInsight acessa sua instância de Azure Key Vault usando a identidade gerenciada que você associa ao cluster HDInsight. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esse recurso está disponível para todos os clusters no HDInsight?**

A criptografia de chave gerenciada pelo cliente está disponível para todos os tipos de cluster, exceto Spark 2,1 e 2,2.

**Posso usar várias chaves para criptografar diferentes discos ou pastas?**

Não, todos os discos gerenciados e discos de recursos são criptografados pela mesma chave.

**O que acontecerá se o cluster perder o acesso ao cofre de chaves ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal do Apache Ambari. Nesse estado, a operação de **alteração da chave** falhará. Depois que o acesso à chave for restaurado, os avisos Ambaris desaparecerão e as operações como a rotação de chaves poderão ser executadas com êxito.

![alerta de Ambari de acesso à chave](./media/disk-encryption/ambari-alert.png)

**Como poderei recuperar o cluster se as chaves forem excluídas?**

Como apenas as chaves habilitadas para "exclusão reversível" têm suporte, se as chaves forem recuperadas no cofre de chaves, o cluster deverá recuperar o acesso às chaves. Para recuperar uma chave de Azure Key Vault, consulte [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [AZ-keyvault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Quais tipos de disco são criptografados? OS discos de recursos/discos do sistema operacional também são criptografados?**

Os discos de recursos e os discos gerenciados são criptografados. OS discos do sistema operacional não estão criptografados.

**Se um cluster for escalado verticalmente, os novos nós oferecerão suporte a chaves gerenciadas pelo cliente de forma direta?**

Sim. O cluster precisa acessar a chave no cofre de chaves durante ao escalar verticalmente. A mesma chave é usada para criptografar discos gerenciados e discos de recursos no cluster.

**As chaves gerenciadas pelo cliente estão disponíveis no meu local?**

As chaves gerenciadas pelo cliente do HDInsight estão disponíveis em todas as nuvens públicas e nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault](../key-vault/general/overview.md).
* [Visão geral da segurança corporativa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
