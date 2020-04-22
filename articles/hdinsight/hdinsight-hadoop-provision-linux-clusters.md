---
title: Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais
description: Configure clusters Hadoop, Kafka, Spark, HBase, R Server ou Storm para HDInsight em um navegador, na CLI clássica do Azure, no Azure PowerShell, na REST ou no SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 6bba71ab35920027cc7296e774936c3d1deb8f92
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770309"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Configure os clusters no HDInsight com o Apache Hadoop, o Apache Spark, o Apache Kafka e muito mais

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Aprenda a configurar e configurar Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services ou Apache Storm no HDInsight. Além disso, saiba como personalizar clusters e adicionar segurança ingressando-os em um domínio.

Um cluster Hadoop é composto por várias máquinas virtuais (nós), usadas para processamento distribuído de tarefas. O Azure HDInsight manipula os detalhes de implementação da instalação e configuração de nós individuais, portanto, você precisa fornecer apenas informações de configuração geral.

> [!IMPORTANT]  
> A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado. Saiba como [excluir um cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Métodos de instalação de cluster

A tabela a seguir mostra os diferentes métodos que você pode usar para configurar um cluster HDInsight.

| Clusters criados com | Navegador da Web | Linha de comando | API REST | . |
| --- |:---:|:---:|:---:|:---:|
| [Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [PowerShell do Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Modelos do Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Este artigo orienta você através da configuração no [portal Azure,](https://portal.azure.com)onde você pode criar um cluster HDInsight.

## <a name="basics"></a>Noções básicas

![hdinsight criar opções personalizadas rápida](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Detalhes do projeto

[O Azure Resource Manager](../azure-resource-manager/management/overview.md) ajuda você a trabalhar com os recursos em sua aplicação como um grupo, referido como um [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups)do Azure . Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada.

### <a name="cluster-details"></a>Detalhes do cluster

#### <a name="cluster-name"></a>Nome do cluster

Os nomes de cluster do HDInsight têm as seguintes restrições:

* Caracteres permitidos: a-z, 0-9, A-Z
* Comprimento máximo: 59
* Nomes reservados: aplicativos
* O escopo de nomeação de cluster é para todos os Azure, em todas as assinaturas. Assim, o nome do cluster deve ser único em todo o mundo.
* Os seis primeiros caracteres devem ser únicos dentro de uma rede virtual

#### <a name="region"></a>Região

Você não precisa especificar o local do cluster explicitamente: o cluster está no mesmo local que o armazenamento padrão. Para obter uma lista de regiões suportadas, selecione a lista suspensa **região** nos [preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Tipo de cluster

Atualmente, o Azure HDInsight apresenta os seguintes tipos de cluster, cada um com um conjunto de componentes para fornecer determinadas funcionalidades.

> [!IMPORTANT]  
> Clusters HDInsight estão disponíveis em vários tipos, cada um para uma carga de trabalho ou tecnologia distinta. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster. Se sua solução exige tecnologias que sejam distribuídas entre vários tipos de cluster HDInsight, uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network) pode conectar os tipos de cluster necessários.

| Tipo de cluster | Funcionalidade |
| --- | --- |
| [O Hadoop](hadoop/apache-hadoop-introduction.md) |Consulta de Lote e a análise de dados armazenados |
| [HBase](hbase/apache-hbase-overview.md) |Processamento de grandes quantidades de dados NoSQL sem esquema |
| [Consulta Interativa](./interactive-query/apache-interactive-query-get-started.md) |Caching na memória para consultas de Hive interativas e mais rápidas |
| [Kafka](kafka/apache-kafka-introduction.md) | Uma plataforma de streaming distribuída que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real |
| [ML Services](r-server/r-server-overview.md) |Uma variedade de recursos de estatísticas de Big Data, modelagem preditiva e aprendizado de máquina |
| [Spark](spark/apache-spark-overview.md) |Processamento na memória, consultas interativas, processamento de transmissão de microlotes |
| [Storm](storm/apache-storm-overview.md) |Processamento de eventos em tempo real |

#### <a name="version"></a>Versão

Escolha a versão do HDInsight para este cluster. Para saber mais, confira [Versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Credenciais de cluster

Com os clusters HDInsight, você pode configurar duas contas de usuário durante a criação de cluster:

* Nome de usuário de login de cluster: O nome de usuário padrão é *administrador*. Ele usa a configuração básica no portal Azure. Às vezes é chamado de "Usuário de cluster", ou "usuário HTTP".
* Nome de usuário Secure Shell (SSH): Usado para se conectar ao cluster através do SSH. Para obter mais informações, consulte [Use SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

O nome de usuário HTTP tem as seguintes restrições:

* Personagens especiais `_` permitidos: e`@`
* Personagens não permitidos:\/#;."', :'!*?(){}[]<>|&-=+%~^espaço
* Comprimento máximo: 20

O nome de usuário SSH tem as seguintes restrições:

* Personagens especiais`_` permitidos: e`@`
* Personagens não permitidos:\/#;."', :'!*?(){}[]<>|&-=+%~^espaço
* Comprimento máximo: 64
* Nomes reservados: hadoop, usuários, oozie, colmeia, maped, ambari-qa, zookeeper, tez, hdfs, sqoop, fio, hcat, ams, hbase, tempestade, administrador, administrador, administrador, usuário, usuário1, teste, usuário2, test1, user3, admin1, 1, 123, a, actuser, adm, adm2, aspnet, backup, console, david, convidado, john, proprietário, raiz, servidor, sql, suporte, support_388945a0, sys, test2, test3, user

## <a name="storage"></a>Armazenamento

![Configurações de armazenamento em cluster: pontos finais compatíveis com HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Embora uma instalação local do Hadoop use o HDFS (Sistema de Arquivos Distribuído Hadoop) para armazenamento no cluster, na nuvem você usa pontos de extremidade de armazenamento conectados ao cluster. Usar o armazenamento em nuvem significa que você pode excluir com segurança os clusters HDInsight usados para computação enquanto ainda retém seus dados.

Os clusters HDInsight podem usar as seguintes opções de armazenamento:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage General Purpose v2
* Azure Storage General Purpose v1
* Blob do Bloco de Armazenamento Azure **(suportado apenas como armazenamento secundário)**

Para obter mais informações sobre opções de armazenamento com o HDInsight, consulte [Comparar opções de armazenamento para uso com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Não há suporte para usar uma conta de armazenamento adicional em um local diferente do cluster HDInsight.

Durante a configuração, para o ponto de extremidade de armazenamento padrão, você especifica um contêiner de blob de uma conta de Armazenamento do Azure ou um Data Lake Storage. O armazenamento padrão contém os logs de aplicativo e de sistema. Opcionalmente, você pode especificar contas adicionais do Armazenamento do Azure e do Data Lake Storage vinculadas que o cluster possa acessar. O cluster HDInsight e as contas de armazenamento padrão dependentes devem estar na mesma localização do Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Configurações do metastore

Você pode criar metastores Hive ou Apache Oozie opcionais. No entanto, nem todos os tipos de cluster dão suporte a metastores e o SQL Data Warehouse do Azure não é compatível com metastores.

Para obter mais informações, consulte [Usar armazenamentos de metadados externos no Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Ao criar um metastore personalizado, não use traços, hifens ou espaços no nome do banco de dados. Isso pode fazer com que o processo de criação de cluster falhe.

#### <a name="sql-database-for-hive"></a>Banco de dados SQL para Colmeia

Se você deseja reter suas tabelas Hive depois de excluir um cluster HDInsight, use uma metastore personalizada. Você poderá então anexar a metastore a outro cluster HDInsight.

Uma metaloja HDInsight criada para uma versão de cluster HDInsight não pode ser compartilhada em diferentes versões de cluster HDInsight. Para obter uma lista das versões do HDInsight, consulte [Versões do HDInsight com suporte](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>Banco de dados SQL para Oozie

Para aumentar o desempenho durante o uso do Oozie, use um metastore personalizado. Uma metastore também pode fornecer acesso a dados de trabalho do Oozie depois de você excluir o cluster.

#### <a name="sql-database-for-ambari"></a>Banco de dados SQL para Ambari

O Ambari é usado para monitorar clusters HDInsight, fazer alterações de configuração e armazenar informações de gerenciamento de clusters, bem como histórico de trabalho. O recurso Personalizado Ambari DB permite que você implante um novo cluster e configure o Ambari em um banco de dados externo que você gerencia. Para obter mais informações, consulte [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Não é possível reutilizar um metastore personalizado do Oozie. Para usar um metastore personalizado do Oozie, é necessário fornecer um Banco de Dados SQL do Azure vazio ao criar o cluster HDInsight.

## <a name="security--networking"></a>Segurança + rede

![opções de criação do hdinsight escolher pacote de segurança empresarial](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Pacote de segurança empresarial

Para tipos de cluster Hadoop, HBase, Kafka, Spark e Consulta Interativa, você pode optar por habilitar o **Enterprise Security Package**. Esse pacote fornece a opção de ter uma configuração mais segura de cluster usando o Apache Ranger e integrando com o Azure Active Directory. Para obter mais informações, consulte [Visão geral da segurança corporativa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

O Pacote de Segurança Empresarial permite que você integre o HDInsight com o Active Directory e o Apache Ranger. Vários usuários podem ser criados usando o pacote de segurança empresarial.

Para obter mais informações sobre a criação de cluster HDInsight ingressado no domínio, consulte [Criar ambiente de área restrita do HDInsight ingressado no domínio](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Para obter mais informações, consulte [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Rede virtual

Se sua solução exige tecnologias que sejam distribuídas entre vários tipos de cluster HDInsight, uma [rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network) pode conectar os tipos de cluster necessários. Essa configuração permite que os clusters e qualquer código que você implantar neles se comuniquem diretamente uns com os outros.

Para obter mais informações sobre como usar uma rede virtual Azure com o HDInsight, consulte [Planejar uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para obter um exemplo de como usar dois tipos de cluster em uma rede virtual do Azure, confira [Usar Streaming Estruturado do Apache Spark com o Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Para obter mais informações sobre como usar o HDInsight com uma rede virtual, incluindo requisitos específicos de configuração para a rede virtual, consulte [Planejar uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Configuração de criptografia de disco

Para obter mais informações, consulte [a criptografia de disco de chave gerenciada pelo cliente](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Proxy REST do Kafka

Esta configuração só está disponível para o tipo de cluster Kafka. Para obter mais informações, consulte [Usando um proxy REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Identidade

Para obter mais informações, consulte [identidades gerenciadas no Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Configuração + preços

![HDInsight escolha o tamanho do seu nó](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Você é cobrado pelo uso do nó enquanto o cluster existir. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído. Os clusters não podem ser desalocados ou colocados em espera.

### <a name="node-configuration"></a>Configuração do nó

Cada tipo de cluster tem seu próprio número de nós, terminologia para nós no cluster e tamanho da VM padrão. Na tabela a seguir, o número de nós para cada tipo de nó está entre parênteses.

| Type | Nós | Diagrama |
| --- | --- | --- |
| O Hadoop |Nó de cabeça (2), Nó do trabalhador (1+) |![Nós de cluster Hadoop do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Servidor de cabeçalho (2), Servidor de região (1 +), Nó mestre/do ZooKeeper (3) |![Configuração do tipo de cluster HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nó do Nimbus (2), Servidor do supervisor (1+), Nó do ZooKeeper (3) |![Configuração do tipo de cluster de tempestade HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Nó de cabeça (2), nó do trabalhador (1+), nó ZooKeeper (3) (grátis para o tamanho A1 ZooKeeper VM) |![Configuração do tipo de cluster de faísca do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Para obter mais informações, consulte [Configuração de nó padrão e tamanhos de máquina virtual para clusters](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) em "Quais são os componentes do Hadoop e as versões no HDInsight?"

O custo de clusters HDInsight é determinado pelo número de nós e pelos tamanhos de máquinas virtuais para os nós.

Diferentes tipos de cluster têm diferentes tipos de nó, números de nós e tamanhos de nós:
* Padrão de tipo de cluster Hadoop:
    * Dois *nós de cabeçalho*  
    * Quatro *nódulos operários*
* Padrão de tipo de cluster Storm:
    * Dois *nós Nimbus*
    * Três *nós ZooKeeper*
    * Quatro *nós supervisores*

Se você está apenas testando o HDInsight, recomendamos que você use um nó Worker. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> O limite de tamanho do cluster varia entre as assinaturas do Azure. Contate o [suporte de cobrança do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) para aumentar o limite.

Quando você usa o portal Azure para configurar o cluster, o tamanho do nó está disponível através da guia **Configuração + preços.** No portal, você também pode ver o custo associado aos diferentes tamanhos de nó.

### <a name="virtual-machine-sizes"></a>Tamanhos de máquina virtual

Quando você implantar clusters, escolha os recursos de computação com base na solução que você planeja implantar. As VMs a seguir são usadas para clusters HDInsight:

* VMs A e série D1-4: [Tamanhos de VM Linux de uso geral](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* VM série D11-14: [Tamanhos de VM Linux com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Para descobrir qual valor você deve usar para especificar um tamanho de VM ao criar um cluster usando os diferentes SDKs ou ao usar o Azure PowerShell, consulte [Tamanhos de VM a serem usados para clusters HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Deste artigo vinculado, use o valor na coluna **tamanho** das tabelas.

> [!IMPORTANT]  
> Se você precisar de mais de 32 nós do Worker em um cluster, você deve selecionar um tamanho de nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM.

Para obter mais informações, consulte [Tamanhos para máquinas virtuais](../virtual-machines/windows/sizes.md). Para obter informações sobre os preços dos vários tamanhos, confira [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Adicionar aplicativo

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster HDInsight baseado em Linux. Você pode usar os aplicativos fornecidos pela Microsoft, a terceiros ou que você desenvolver por conta própria. Para saber mais, confira [Instalar aplicativos de terceiros do Apache Hadoop no Azure HDInsight](hdinsight-apps-install-applications.md).

A maioria dos aplicativos HDInsight é instalada em um nó de borda vazia.  Um nó de borda vazio é uma máquina virtual Linux com as mesmas ferramentas de cliente instaladas e configuradas do nó principal. Você pode usar o nó de borda para acessar o cluster, testar e hospedar seus aplicativos clientes. Para saber mais, confira [Usar nós de borda vazia no HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Ações de script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

Alguns componentes nativos do Java, como Apache Mahout e Cascading, podem ser executados no cluster como arquivos Java Archive (JAR). Esses arquivos JAR podem ser distribuídos para o Armazenamento do Azure e enviados aos clusters HDInsight por meio de mecanismos de envio de trabalho do Hadoop. Para obter mais informações, consulte [Enviar Apache trabalhos do Hadoop por meio de programação](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Se você tiver problemas para implantar arquivos JAR nos clusters do HDInsight ou ao chamar arquivos JAR nesses clusters, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).
>
> A cascata não tem suporte do HDInsight e não está qualificada para o Suporte da Microsoft. Para listas de componentes suportados, consulte [As novidades nas versões de cluster fornecidas pelo HDInsight](hdinsight-component-versioning.md).

Às vezes, você deseja definir os seguintes arquivos de configuração durante o processo de criação:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Para saber mais, confira [Personalizar clusters HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Próximas etapas

* [Solucionando falhas de criação de clusters com o Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [O que são o HDInsight, o ecossistema do Apache Hadoop e os clusters Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Introdução ao uso do Apache Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Trabalhar no Apache Hadoop no HDInsight em um computador Windows](hdinsight-hadoop-windows-tools.md)
