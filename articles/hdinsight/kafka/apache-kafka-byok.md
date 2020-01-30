---
title: Traga sua própria chave para Apache Kafka no Azure HDInsight
description: Este artigo descreve como usar sua própria chave do Azure Key Vault para criptografar dados armazenados no Apache Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 72fd23e4283925b91d749fef0afac4e87e93405c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841621"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Traga sua própria chave para Apache Kafka no Azure HDInsight

O Azure HDInsight inclui suporte ao BYOK (Bring Your Own Key) para Apache Kafka. Essa funcionalidade permite que você tenha e gerencie as chaves usadas para criptografar dados em repouso.

Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar o BYOK, poderá fornecer a chave de criptografia para o HDInsight usar e gerenciá-la usando o Azure Key Vault.

A criptografia de BYOK é um processo de uma etapa realizado durante a criação do cluster sem nenhum custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com o Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

Todas as mensagens para o cluster do Kafka (incluindo réplicas mantidas pelo Kafka) são criptografadas com uma DEK (Chave de Criptografia de Dados). A DEK é protegida usando a KEK (Chave de Criptografia de Chave) do seu cofre de chaves. Os processos de criptografia e descriptografia são realizados inteiramente pelo Azure HDInsight.

Você pode usar o portal do Azure ou a CLI do Azure para gorar as chaves com segurança no cofre de chaves. Quando uma chave é girada, o cluster do Kafka do HDInsight começa a usar a nova chave em questão de minutos. Habilite os recursos de proteção de chave de "exclusão reversível" para proteger contra cenários de ransomware e exclusão acidental. Não há suporte para cofres de chaves sem esse recurso de proteção.

## <a name="get-started-with-byok"></a>Introdução ao BYOK

Para criar um cluster Kafka habilitado para BYOK, vamos percorrer as seguintes etapas:

1. Criar identidades gerenciadas para recursos do Azure
2. Configurar Azure Key Vault e chaves
3. Criar cluster HDInsight Kafka com BYOK habilitado
4. Girando a chave de criptografia

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades gerenciadas para recursos do Azure

Para autenticar no Key Vault, crie uma identidade gerenciada atribuída pelo usuário usando o [portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)ou [CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](../hdinsight-managed-identities.md). Embora o Azure Active Directory seja necessário para as identidades gerenciadas e o BYOK para Kafka, o ESP (Enterprise Security Package) não é um requisito. Certifique-se de salvar a ID do recurso de identidade gerenciada para quando você a adicionar à política de acesso do Key Vault.

![Criar uma identidade gerenciada atribuída pelo usuário no portal do Azure](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>Configurar o Key Vault e as chaves

O HDInsight é compatível apenas com o Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para o Azure Key Vault. Lembre-se de que as chaves devem ter "exclusão reversível". O recurso de "exclusão reversível" está disponível por meio das interfacesC#REST, .net/, PowerShell e CLI do Azure.

1. Para criar um novo cofre de chaves, siga o início rápido do [Azure Key Vault](../../key-vault/quick-create-cli.md). Para obter mais informações sobre como importar as chaves existentes, visite [Sobre chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md).

1. Habilite a "exclusão reversível" no cofre de chaves usando o comando [AZ keyvault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Criar chaves.

    a. Para criar uma nova chave, selecione **Gerar/Importar** do menu **Chaves** em **Configurações**.

    ![Gerar uma nova chave no Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Gerar uma nova chave no Azure Key Vault")

    b. Defina **Opções** para **Gerar** e forneça um nome à chave.

    ![O Apache Kafka gera o nome da chave](./media/apache-kafka-byok/apache-kafka-create-key.png "Gerar nome da chave")

    c. Selecione a chave que você criou na lista de chaves.

    ![Lista de chaves do Apache Kafka Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. Ao usar sua própria chave de criptografia de cluster do Kafka, será necessário fornecer o URI da chave. Copie o **Identificador de chave** e salve-o em algum local até que esteja pronto para criar o cluster.

    ![Identificador de chave Get do Apache Kafka](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Adicione identidade gerenciada à política de acesso do cofre de chaves.

    a. Crie uma nova política de acesso do Azure Key Vault.

    ![Criar uma nova política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. Em **Selecionar Entidade de Segurança**, escolha a identidade gerenciada atribuída pelo usuário que você criou.

    ![Definir Selecionar Entidade de Segurança para a política de acesso do Azure Key Vault](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. Defina **Permissões de Chave** como **Obter**, **Decodificar Chave** e **Codificar Chave**.

    ![Definir permissões de chave para Azure Key Vault acesso Policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Definir permissões de chave para Azure Key Vault acesso Policy1")

    d. Defina **Permissões do Segredo** como **Obter**, **Definir** e **Excluir**.

    ![Definir permissões de chave para policy2 acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Definir permissões de chave para policy2 acesso de Azure Key Vault")

    e. Clique em **Salvar**.

    ![Salvar política de acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Criar cluster HDInsight

Agora você está pronto para criar um novo cluster do HDInsight. O BYOK pode ser aplicado apenas a novos clusters durante a criação do cluster. Não é possível remover a criptografia de clusters de BYOK e o BYOK não pode ser adicionado a clusters existentes.

![Criptografia de disco do Kafka no portal do Azure](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka.png)

Durante a criação do cluster, forneça a URL completa, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave. Para obter detalhes completos de criação de cluster, consulte [criar Apache Hadoop clusters usando o portal do Azure](./apache-kafka-get-started.md)

## <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia

Pode haver cenários em que você talvez queira alterar as chaves de criptografia usadas pelo cluster Kafka depois que ele tiver sido criado. Isso pode ser facilmente por meio do Portal. Para essa operação, o cluster deve ter acesso à chave atual e à nova chave pretendida, caso contrário, a operação de rotação de teclas falhará.

Para girar a chave, você deve ter a URL completa da nova chave (consulte a etapa 3 de [Configurar o Key Vault e as chaves](#set-up-the-key-vault-and-keys)). Depois disso, vá para a seção Propriedades do cluster Kafka no portal e clique em **alterar chave** em URL da **chave de criptografia do disco**. Insira na nova URL de chave e envie para girar a chave.

![Kafka girar a chave de criptografia de disco](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Perguntas frequentes para BYOK para Apache Kafka

**Como o cluster do Kafka acessa meu cofre de chaves?**

Associe uma identidade gerenciada ao cluster de Kafka do HDInsight durante a criação do cluster. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esse recurso está disponível para todos os clusters do Kafka no HDInsight?**

A criptografia de BYOK é possível apenas para o cluster do Kafka 1.1 e posteriores.

**É possível ter chaves diferentes para partições/tópicos diferentes?**

Não, todos os discos gerenciados no cluster são criptografados pela mesma chave.

**O que acontecerá se o cluster perder o acesso ao cofre de chaves ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal do Apache Ambari. Nesse estado, a operação de **alteração da chave** falhará. Depois que o acesso à chave for restaurado, os avisos Ambaris desaparecerão e as operações como a rotação de chaves poderão ser executadas com êxito.

![Alerta de Ambari de acesso à chave Apache Kafka](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Como poderei recuperar o cluster se as chaves forem excluídas?**

Como apenas as chaves habilitadas para "exclusão reversível" têm suporte, se as chaves forem recuperadas no cofre de chaves, o cluster deverá recuperar o acesso às chaves. Para recuperar uma chave de Azure Key Vault, consulte [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [AZ-keyvault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**É possível ter aplicativos de produtor/consumidor trabalhando com um cluster de BYOK e não BYOK simultaneamente?**

Sim. O uso de BYOK é transparente para os aplicativos de produtor/consumidor. A criptografia ocorre na camada do sistema operacional. Não é necessário fazer nenhuma alteração nos aplicativos Kafka de produtor/consumidor existentes.

**Os discos de recursos/discos de sistema operacional também são criptografados?**

Não. Discos do sistema operacional e discos de recursos não são criptografados.

**Se um cluster for escalado verticalmente, os novos agentes darão suporte ao BYOK perfeitamente?**

Sim. O cluster precisa acessar a chave no cofre de chaves durante ao escalar verticalmente. As mesmas chaves são usadas para criptografar todos os discos gerenciados no cluster.

**O BYOK está disponível na minha localização?**

O BYOK do Kafka está disponível em todas as nuvens públicas.

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../../key-vault/key-vault-overview.md)
* Para começar a usar o Azure Key Vault, consulte [Introdução ao Azure Key Vault](../../key-vault/key-vault-overview.md).
