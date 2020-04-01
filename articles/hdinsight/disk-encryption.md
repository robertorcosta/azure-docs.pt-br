---
title: Criptografia de disco de chave gerenciada pelo cliente para o Azure HDInsight
description: Este artigo descreve como usar sua própria chave de criptografia do Azure Key Vault para criptografar dados armazenados em discos gerenciados em clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c0521f384a333c3054397fb0ec7c2ab907e54f67
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411761"
---
# <a name="customer-managed-key-disk-encryption"></a>Criptografia de disco de chave gerenciada pelo cliente

O Azure HDInsight suporta criptografia de chaves gerenciadas pelo cliente para dados em discos gerenciados e discos de recursos conectados a máquinas virtuais de cluster HDInsight. Esse recurso permite que você use o Azure Key Vault para gerenciar as chaves de criptografia que protegem os dados em repouso em seus clusters HDInsight.

Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar as chaves gerenciadas pelo cliente para o HDInsight, você fornecerá as chaves de criptografia para o HDInsight usar e gerenciar essas chaves usando o Azure Key Vault.

Este documento não aborda os dados armazenados em sua conta do Azure Storage. Para obter mais informações sobre a criptografia do Azure Storage, consulte [a criptografia de armazenamento do Azure para obter dados em repouso](../storage/common/storage-service-encryption.md). Seus clusters podem ter uma ou mais contas de armazenamento Azure anexadas onde as chaves de criptografia também podem ser gerenciadas pela Microsoft ou gerenciadas pelo cliente, mas o serviço de criptografia é diferente.

## <a name="introduction"></a>Introdução

A criptografia de chave gerenciada pelo cliente é um processo de uma etapa manipulado durante a criação de clusters sem custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

Tanto o disco de recursos quanto os discos gerenciados em cada nó do cluster são criptografados com uma chave de criptografia de dados simétrica (DEK). A DEK é protegida usando a KEK (Chave de Criptografia de Chave) do seu cofre de chaves. Os processos de criptografia e descriptografia são realizados inteiramente pelo Azure HDInsight.

Se o firewall do cofre chave estiver habilitado no cofre de chaves onde a chave de criptografia de disco é armazenada, os endereços IP do Provedor de Recursos regional HDInsight para a região onde o cluster será implantado devem ser adicionados à configuração de firewall do cofre chave. Isso é necessário porque o HDInsight não é um serviço confiável de cofre chave do Azure.

Você pode usar o portal do Azure ou a CLI do Azure para gorar as chaves com segurança no cofre de chaves. Quando uma chave gira, o cluster HDInsight começa a usar a nova chave em poucos minutos. Habilite os recursos de proteção de chave [do Soft Delete](../key-vault/key-vault-ovw-soft-delete.md) para proteger contra cenários de ransomware e exclusão acidental. Os cofres-chave sem este recurso de proteção não são suportados.

|Tipo de cluster |Disco do SISTEMA OPERACIONAL (disco gerenciado) |Disco de dados (disco gerenciado) |Disco de dados temporários (SSD local) |
|---|---|---|---|
|Kafka, HBase com gravações aceleradas|[Criptografia SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Criptografia SSE + Criptografia opcional CMK|Criptografia opcional CMK|
|Todos os outros clusters (Spark, Interactive, Hadoop, HBase sem gravações aceleradas)|Criptografia SSE|N/D|Criptografia opcional CMK|

## <a name="get-started-with-customer-managed-keys"></a>Começar a usar chaves gerenciadas pelo cliente

Para criar um cluster HDInsight habilitado para a chave gerenciada pelo cliente, passaremos pelas seguintes etapas:

1. Crie identidades gerenciadas para recursos do Azure
1. Criar Azure Key Vault
1. Chave Create
1. Criar política de acesso
1. Crie o cluster HDInsight com a chave gerenciada pelo cliente ativada
1. Girando a chave de criptografia

## <a name="create-managed-identities-for-azure-resources"></a>Crie identidades gerenciadas para recursos do Azure

Crie uma identidade gerenciada atribuída pelo usuário para autenticar no Key Vault.

Consulte [Criar uma identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para etapas específicas. Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [Identidades gerenciadas no Azure HDInsight](hdinsight-managed-identities.md). Certifique-se de salvar a ID do recurso de identidade gerenciada para quando você a adicionar à política de acesso do Key Vault.

## <a name="create-azure-key-vault"></a>Criar Azure Key Vault

Crie um cofre da chave. Consulte [Criar o Cofre de Chaves do Azure](../key-vault/quick-create-portal.md) para obter etapas específicas.

O HDInsight é compatível apenas com o Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para o Azure Key Vault. Lembre-se que o cofre da chave deve ter **soft delete** ativado. Para obter mais informações sobre como importar as chaves existentes, visite [Sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Chave Create

1. A partir do seu novo cofre de chaves, navegue até **as teclas de** > **configuração** > +**Gerar/Importar**.

    ![Gerar uma nova chave no Azure Key Vault](./media/disk-encryption/create-new-key.png "Gerar uma nova chave no Azure Key Vault")

1. Forneça um nome e selecione **Criar**. Mantenha o **tipo de chave** padrão do **RSA**.

    ![gera nome-chave](./media/disk-encryption/create-key.png "Gerar nome-chave")

1. Ao retornar à página **Chaves,** selecione a chave criada.

    ![lista chave do cofre](./media/disk-encryption/key-vault-key-list.png)

1. Selecione a versão para abrir a página **Versão chave.** Quando você usa sua própria chave para criptografia de cluster HDInsight, você precisa fornecer a uri chave. Copie o **Identificador de chave** e salve-o em algum local até que esteja pronto para criar o cluster.

    ![obter identificador chave](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Criar política de acesso

1. A partir do seu novo cofre de chaves, navegue até as políticas de acesso às > **configurações** > **+ Adicione política de acesso**. **Settings**

    ![Criar uma nova política de acesso do Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Na página **adicionar política de acesso,** forneça as seguintes informações:

    |Propriedade |Descrição|
    |---|---|
    |Permissões-chave|Selecione **Obter,** **Desembrulhar a tecla**e **envoltório da tecla**.|
    |Permissões Secretas|Selecione **'Obter',** **Definir**e **Excluir**.|
    |Selecione o principal|Selecione a identidade gerenciada atribuída pelo usuário que você criou anteriormente.|

    ![Definir Selecionar Entidade de Segurança para a política de acesso do Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecione **Adicionar**.

1. Clique em **Salvar**.

    ![Salvar a política de acesso do Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Criar cluster com criptografia de disco de chave gerenciada pelo cliente

Agora você está pronto para criar um novo cluster do HDInsight. A chave gerenciada pelo cliente só pode ser aplicada a novos clusters durante a criação de clusters. A criptografia não pode ser removida de clusters-chave gerenciados pelo cliente e a chave gerenciada pelo cliente não pode ser adicionada aos clusters existentes.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Durante a criação do cluster, forneça o identificador de **tecla**completo, incluindo a versão-chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave.

![Criar novo cluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir mostra como usar o Azure CLI para criar um novo cluster Apache Spark com criptografia de disco ativada. Para obter mais informações, consulte [Azure CLI az hdinsight criar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia

Pode haver cenários em que você pode querer alterar as chaves de criptografia usadas pelo cluster HDInsight depois de criado. Isso pode ser facilmente através do portal. Para esta operação, o cluster deve ter acesso tanto à chave atual quanto à nova chave pretendida, caso contrário, a operação da chave giratória falhará.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para girar a chave, você precisa do URI do cofre da chave base. Depois de fazer isso, vá para a seção de propriedades do cluster HDInsight no portal e clique em **Alterar chave** em URL de chave de criptografia **de disco**. Digite a nova url de chave e envie para girar a chave.

![girar chave de criptografia de disco](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir mostra como girar a chave de criptografia de disco para um cluster HDInsight existente. Para obter mais informações, consulte [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Para usar as chaves gerenciadas pelo cliente usando um modelo do Gerenciador de recursos, atualize seu modelo com as seguintes alterações:

1. No arquivo **azuredeploy.json,** adicione a seguinte propriedade ao objeto "recursos":

    ```json
       "diskEncryptionProperties":
         {
                 "vaultUri": "[parameters('diskEncryptionVaultUri')]",
                  "keyName": "[parameters('diskEncryptionKeyName')]",
                  "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
                   "msiResourceId": "[parameters('diskEncryptionMsiResourceId')]"
         }

1. In the **azuredeploy.parameters.json** file, add the following parameters. You can get the values of these parameters from the Key Vault URI and the managed Identity. For example, if you have the following URI and identity values,
    * Sample key vault URI: https://<KeyVault_Name>.vault.azure.net/keys/clusterkey/<Cluster_Key_Value>
    * Sample user-assigned managed identity: "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>

    The parameters in the **azuredeploy.parameters.json** file are:

    ```json
   "diskEncryptionVaultUri": {
            "value": "https://<KeyVault_Name>.vault.azure.net"
        },
        "diskEncryptionKeyName": {
            "value": "clusterkey"
        },
        "diskEncryptionKeyVersion": {
            "value": "<Cluster_Key_Value>"
        },
        "diskEncryptionMsiResourceId": {
            "value": "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>"
        }
    ```

## <a name="faq-for-customer-managed-key-encryption"></a>Perguntas frequentes sobre criptografia de chaves gerenciadas pelo cliente

**Como o cluster HDInsight acessa meu cofre principal?**

O HDInsight acessa a instância do Azure Key Vault usando a identidade gerenciada que você associa ao cluster HDInsight. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Este recurso está disponível para todos os clusters no HDInsight?**

A criptografia de chave gerenciada pelo cliente está disponível para todos os tipos de cluster, exceto o Spark 2.1 e o 2.2.

**Posso usar várias chaves para criptografar diferentes discos ou pastas?**

Não, todos os discos gerenciados e discos de recursos são criptografados pela mesma chave.

**O que acontece se o cluster perder o acesso ao cofre da chave ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal Apache Ambari. Neste estado, a operação **Change Key** falhará. Uma vez restaurado o acesso à chave, os avisos ambari desaparecerão e operações como a rotação de chaves podem ser executadas com sucesso.

![alerta Ambari de acesso chave](./media/disk-encryption/ambari-alert.png)

**Como poderei recuperar o cluster se as chaves forem excluídas?**

Uma vez que apenas as teclas habilitadas para "Soft Delete" são suportadas, se as chaves forem recuperadas no cofre de chaves, o cluster deve recuperar o acesso às teclas. Para recuperar uma chave azure Key Vault, consulte [Desfazer-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Quais tipos de disco são criptografados? Os discos/discos de recursos do SISTEMA OPERACIONAL também são criptografados?**

Os discos de recursos e os discos de dados/gerenciados são criptografados. Os discos do SO não são criptografados.

**Se um cluster for ampliado, os novos nós suportarão as chaves gerenciadas pelo cliente perfeitamente?**

Sim. O cluster precisa acessar a chave no cofre de chaves durante ao escalar verticalmente. A mesma chave é usada para criptografar discos gerenciados e discos de recursos no cluster.

**As chaves gerenciadas pelo cliente estão disponíveis na minha localização?**

As chaves gerenciadas pelo cliente HDInsight estão disponíveis em todas as nuvens públicas e nuvens nacionais.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault](../key-vault/key-vault-overview.md).
* [Visão geral da segurança corporativa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
