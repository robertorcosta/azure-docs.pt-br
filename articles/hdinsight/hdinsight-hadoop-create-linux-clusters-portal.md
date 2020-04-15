---
title: Crie clusters Apache Hadoop usando navegador web, Azure HDInsight
description: Aprenda a criar aglomerados Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no HDInsight. Use o navegador web e o portal Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a5f6ac76d509a0a63c2d641f91cd91cdb2e0d19d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313818"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá como criar clusters Azure HDInsight baseados no Linux usando o portal. Detalhes adicionais estão disponíveis nos [clusters Create HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

O portal do Azure expõe a maioria das propriedades do cluster. Usando modelos do Azure Resource Manager, é possível ocultar muitos detalhes. Para obter mais informações, consulte [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-clusters"></a>Criar clusters

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu superior, selecione **+ Crie um recurso**.

    ![Crie um novo cluster no portal Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Criando um novo cluster no portal do Azure")

1. Selecione **Analytics** > **Azure HDInsight** para ir à página do **cluster Criar HDInsight.**

## <a name="basics"></a>Noções básicas

![HDInsight cria noções básicas de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Criando um novo cluster no portal do Azure")

Na guia Noções Básicas, forneça as **seguintes** informações:

|Propriedade |Descrição |
|---|---|
|Subscription|Na lista de paradas, selecione a assinatura do Azure usada para o cluster.|
|Resource group|Na lista de paradas, selecione o grupo de recursos existente ou selecione **Criar novo**.|
|Nome do cluster|Insira um nome global exclusivo.|
|Região|Na lista de paradas, selecione uma região onde o cluster é criado.|
|Tipo de cluster|Clique **em Selecionar o tipo de cluster** para abrir uma lista. Na lista, selecione o tipo de cluster desejado. Os clusters HDInsight vêm em diferentes tipos. Eles correspondem à carga de trabalho ou tecnologia para a qual o cluster está sintonizado. Não há nenhum método com suporte para criar um cluster que combine vários tipos.|
|Versão|Na lista de paradas, selecione uma **versão**. Use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).|
|Nome de usuário de logon do cluster|Forneça o nome de usuário, padrão é **admin**.|
|Senha de logon do cluster|Forneça a senha.|
|Confirmar senha de login do cluster|Redigite a senha|
|Nome de usuário do Secure Shell (SSH)|Forneça o nome de usuário, padrão é **sshuser**|
|Usar senha de logon do cluster para SSH|Se você quiser a mesma senha SSH que a senha de administrador especificada anteriormente, selecione a senha de login do cluster Use para caixa de seleção **SSH.** Caso contrário, forneça uma **SENHA** ou **CHAVE PÚBLICA** para autenticar o usuário SSH. Uma chave pública é a abordagem recomendada. Escolha **Selecionar** na parte inferior para salvar a configuração das credenciais.  Para saber mais, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selecione **A seguir: O armazenamento >>** para avançar para a próxima guia.

## <a name="storage"></a>Armazenamento

![HDInsight cria armazenamento em cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Criando um novo cluster no portal Azure - armazenamento")

### <a name="primary-storage"></a>Armazenamento primário

Na lista de isto de baixa para **o tipo de armazenamento principal,** selecione o tipo de armazenamento padrão. Os campos posteriores a serem concluídos variam de acordo com a sua seleção. Para **armazenamento azure:**

1. Para **o método Seleção,** escolha **Selecionar na lista**ou Usar **a tecla de acesso**.
    * Em **Select from list,** selecione sua **conta de armazenamento principal** na lista de desímpara baixa ou selecione Criar **novo**.
    * Para **usar a chave de acesso,** digite o nome da conta de **armazenamento**. Em seguida, forneça a **tecla Access**.

1. Para **Container,** aceite o valor padrão ou insira um novo.

### <a name="additional-azure-storage"></a>Armazenamento adicional do Azure

Opcional: Selecione **Adicionar armazenamento Azure** para armazenamento adicional de cluster. O uso de uma conta de armazenamento adicional em uma região diferente do grupo HDInsight não é suportado.

### <a name="metastore-settings"></a>Configurações do metastore

Opcional: Especifique um banco de dados SQL existente para salvar a Colmeia Apache, apache Oozie e, ou metadados Apache Ambari fora do cluster. O Banco de Dados SQL do Azure que é usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. Ao criar um metastore, não use traços nem hifens para nomear um banco de dados. Esses caracteres podem causar falha no processo de criação do cluster.

Selecione **A seguir: Segurança + >>de rede** para avançar para a próxima guia.

## <a name="security--networking"></a>Segurança + rede

![HDInsight cria rede de segurança de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight cria rede de segurança de cluster")

A partir da guia **Segurança + rede,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|Pacote de segurança empresarial|Opcional: Selecione a caixa de seleção para usar **o Enterprise Security Package**. Para obter mais informações, consulte [Configurar um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Opcional: Selecione uma versão TLS na lista de paradas. Para obter mais informações, consulte [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Rede virtual|Opcional: Selecione uma rede virtual e uma sub-rede existentes na lista de baixa. Para obter informações, consulte [Planejar uma implantação de rede virtual para clusters Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos de configuração específicos para a rede virtual.|
|Configurações de criptografia de disco|Opcional: Selecione a caixa de seleção para usar criptografia. Para obter mais informações, consulte [a criptografia de disco de chave gerenciada pelo cliente](./disk-encryption.md).|
|Proxy REST do Kafka|Esta configuração só está disponível para o tipo de cluster Kafka. Para obter mais informações, consulte [Usando um proxy REST](./kafka/rest-proxy.md).|
|Identidade|Opcional: Selecione uma identidade de serviço atribuída pelo usuário existente na lista de desímetros. Para obter mais informações, consulte [identidades gerenciadas no Azure HDInsight](./hdinsight-managed-identities.md).|

Selecione **A seguir: Configuração + >>de preços** para avançar para a próxima guia.

## <a name="configuration--pricing"></a>Configuração + preços

![Configuração de cluster de criação do HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Guia de configuração e preços")

Na guia **Configuração + preços,** forneça as seguintes informações:

|Propriedade |Descrição |
|---|---|
|+ Adicionar aplicação|Opcional: Selecione os aplicativos que quiser. Você, a Microsoft ou os fornecedores independentes de software (ISVs)  podem desenvolver esses aplicativos. Para obter mais informações, consulte [Instalar aplicativos durante a criação do cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Tamanho do nó|Opcional: Selecione um nó de tamanho diferente.|
|Número de nós|Opcional: Digite o número de nós para o tipo de nó especificado. Se você planeja mais de 32 nós de trabalhadores, selecione um tamanho de nó de cabeça com pelo menos oito núcleos e 14 GB de RAM. Planeje os nós na criação de cluster ou, dimensionando o cluster após a criação.|
|Habilitar dimensionamento automático|Opcional: Selecione a caixa de seleção para ativar o recurso. Para obter mais informações, consulte [Dimensionar automaticamente os clusters Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Adicionar ação de script|Opcional: Esta opção funciona se você quiser usar um script personalizado para personalizar um cluster, já que o cluster está sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).|

Selecione **'Revisar + criar >>** para validar a configuração do cluster e avançar para a guia final.

## <a name="review--create"></a>Examinar + criar

![HDInsight cria resumo de cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Especificar o número de números de cluster")

Revise as configurações. Selecione **Criar** para criar o cluster.

Levará algum tempo para que o cluster seja criado, geralmente, cerca de 20 minutos. Monitore as **Notificações** para conferir o processo de provisionamento.

## <a name="post-creation"></a>Criação pós

Após o término do processo de criação, selecione **Ir para Recurso** na notificação **Implantação com êxito**. A janela do cluster fornece as informações a seguir.

![Visão geral do cluster do portal HDI Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades do cluster")

Alguns dos ícones na janela são explicados da seguinte forma:

|Propriedade | Descrição |
|---|---|
|Visão geral|Fornece todas as informações essenciais sobre o cluster. Exemplos são o nome, o grupo de recursos ao qual ele pertence, a localização, o sistema operacional e a URL do painel do cluster.|
|Painéis de cluster|Direciona você para o portal Ambari associado ao cluster.|
|SSH + Login de cluster|Fornece informações necessárias para acessar o cluster usando SSH.|
|Excluir|exclui o cluster HDInsight.|

## <a name="customize-clusters"></a>Personalizar clusters

* [Personalizar clusters HDInsight usando Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Excluir o cluster

Consulte [Excluir um cluster HDInsight usando seu navegador, PowerShell ou o Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito um cluster HDInsight. Saiba agora como trabalhar com o cluster.

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver topologias em Java para o Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Criar um aplicativo independente usando o Scala](spark/apache-spark-create-standalone-application.md)
