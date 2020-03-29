---
title: Instalar aplicativos de terceiros no HDInsight do Azure
description: Saiba como instalar aplicativos Apache Hadoop de terceiros no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366335"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalar aplicativos Apache Hadoop de terceiros no Azure HDInsight

Saiba como instalar um aplicativo [Apache Hadoop](https://hadoop.apache.org/) de terceiros no Azure HDInsight. Para obter instruções sobre como instalar seu próprio aplicativo, confira [Instalar aplicativos personalizados do HDInsight](hdinsight-apps-install-custom-applications.md).

Um aplicativo do HDInsight é um aplicativo que os usuários podem instalar em um cluster do HDInsight. Esses aplicativos podem ser desenvolvidos pela Microsoft, por ISVs (fornecedores independentes de software) ou por conta própria.  

A lista a seguir mostra os aplicativos publicados:

|Aplicativo |Tipo de cluster(s) | Descrição |
|---|---|---|
|[Plataforma de inteligência atscale](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |O Hadoop |O AtScale transforma seu cluster HDInsight em um servidor OLAP em escala, permitindo que você consulte bilhões de linhas de dados interativamente usando as ferramentas de BI que você já conhece, possui e ama – do Microsoft Excel, Power BI, Tableau Software ao QlikView. |
|[CDAP para HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |O CDAP é a primeira plataforma de integração unificada para big data que acelera o tempo de valorização do Hadoop e permite que a TI forneça dados de autoatendimento. De código aberto e extensível, o CDAP remove barreiras à inovação. Requisitos: 4 Nó súbicos da região, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |O Hadoop |A plataforma escalável de autoatendimento da Datameer para preparar, explorar e governar seus dados para análise acelera a transformação de dados multifonte complexos em informações valiosas e prontas para os negócios, fornecendo insights mais rápidos e inteligentes em uma escala corporativa. |
|[Dataiku DSS no HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS em uma plataforma de ciência de dados corporativa que permite que cientistas de dados e analistas de dados colaborem para projetar e executar novos produtos e serviços de dados de forma mais eficiente, transformando dados brutos em previsões impactantes. |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark,HBase,Tempestade, Kafka |Manter os dados consistentes em um ambiente distribuído é um desafio maciço de operações de dados. A WANdisco Fusion, uma plataforma de software de classe empresarial, resolve esse problema permitindo consistência de dados não estruturados em qualquer ambiente. |
|[H2O SparklingWater para HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water suporta os seguintes algoritmos distribuídos: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim for Real-Time Data Integration to HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Tempestade, Faísca, Kafka |O Striim (pronuncia-se "fluxo") é uma integração de dados de fluxo contínuo de ponta a ponta + plataforma de inteligência, permitindo a ingestão, o processamento e a análise contínuos de fluxos de dados diferentes. |
|[Jumbune Enterprise-Acelerando BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Em alto nível, Jumbune auxilia empresas por, 1. Acelerando o desempenho da carga de trabalho tez, mapreduce & spark baseado em colmeia, Java, scala. 2. Monitoramento proativo de cluster hadoop, 3. Estabelecendo o gerenciamento da qualidade dos dados no sistema de arquivos distribuídos. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Faísca |Alimentada por Apache Kylin, a Kyligence Enterprise permite bi em Big Data. Como um motor OLAP corporativo em Hadoop, a Kyligence Enterprise capacita o analista de negócios a arquitetar bi em Hadoop com data warehouse padrão do setor e metodologia de BI. |
|[Presto Starburst para Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |O Hadoop |Presto é um mecanismo de consulta SQL distribuído rápido e escalável. Arquitetado para a separação de armazenamento e computação, o Presto é perfeito para consultar dados no Azure Data Lake Storage, no Armazenamento de Blobs do Azure, em bancos de dados SQL e NoSQL e em outras fontes de dados. |
|[Coletor de dados StreamSets para HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector é um motor leve e poderoso que transmite dados em tempo real. Use o Data Collector para rodar e processar dados em seus fluxos de dados. Vem com uma licença de teste de 30 dias. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise for HDInsight suporta disputas de dados em toda a empresa para qualquer escala de dados. O custo de execução do Trifacta no Azure é uma combinação de custos de assinatura trifacta mais os custos de infra-estrutura do Azure para as máquinas virtuais. |
|[Plataforma de Dados Unifi](https://unifisoftware.com/platform/) |Hadoop,HBase,Tempestade, Faísca |A Unifi Data Platform é um conjunto perfeitamente integrado de ferramentas de dados de autoatendimento projetado para capacitar o usuário de negócios a enfrentar desafios de dados que impulsionam a receita incremental, reduzem custos ou complexidade operacional. |
|[Desvendar dados APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Aplicativo Unravel Data para o cluster HDInsight Spark. |
|[Catálogo de dados orientados por IA da Waterline](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |A Waterline cataloga, organiza e rege dados usando IA para marcar automaticamente dados com termos de negócios. O catálogo de negócios da Waterline é um componente crítico e de sucesso para análise de autoatendimento, conformidade e governança e iniciativas de gerenciamento de TI. |

As instruções fornecidas neste artigo usam o portal do Azure. Também é possível exportar o modelo do Azure Resource Manager do portal ou obter uma cópia do modelo do Resource Manager de fornecedores e usar o Azure PowerShell e a CLI clássica do Azure para implantar o modelo.  Consulte [Criar clusters do Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Pré-requisitos
Se você quiser instalar aplicativos do HDInsight em um cluster HDInsight existente, precisará ter um cluster HDInsight. Para criar um, confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md). Você também pode instalar aplicativos do HDInsight quando cria um cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalar aplicativos em clusters existentes
O procedimento a seguir mostra como instalar aplicativos do HDInsight em um cluster HDInsight existente.

**Instalar um aplicativo do HDInsight**

1. Faça login no [portal Azure](https://portal.azure.com).
2. No menu esquerdo, navegue até **todos os serviços** > **de clusters** > HDInsight do**Analytics**.
3. Selecione um cluster HDInsight na lista.  Se não tiver um, você deverá criá-lo primeiro.  Confira [Criar clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Na categoria **Configurações,** selecione **Aplicativos**. Você pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![Menu do portal de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecione **+Adicionar** no menu. Você pode ver uma lista de aplicativos disponíveis.  Se **+Add** estiver acinzentado, isso significa que não há aplicativos para esta versão do cluster HDInsight.
   
    ![Aplicativos disponíveis de aplicativos HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Selecione um dos aplicativos disponíveis e siga as instruções para aceitar os termos legais.

Você pode ver o status de instalação das notificações do portal (selecione o ícone do sino na parte superior do portal). Depois que o aplicativo é instalado, ele é exibido na folha Aplicativos Instalados.

## <a name="install-applications-during-cluster-creation"></a>Instalar aplicativos durante a criação do cluster

Você tem a opção de instalar aplicativos do HDInsight ao criar um cluster. Durante o processo, os aplicativos do HDInsight são instalados depois que o cluster é criado e está no estado de execução. Para instalar aplicativos durante a criação de clusters usando o portal Azure, a partir da guia **Configuração + preços,** selecione **+ Adicionar aplicativo**.

![Aplicativos de configuração de cluster do portal Do Zure](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Listar os aplicativos do HDInsight instalados e as propriedades
O portal mostra uma lista dos aplicativos do HDInsight instalados para um cluster e as propriedades de cada aplicativo instalado.

**Listar o aplicativo do HDInsight e exibir as propriedades**

1. Faça login no [portal Azure](https://portal.azure.com).
2. No menu esquerdo, navegue até **todos os serviços** > **de clusters** > HDInsight do**Analytics**.
3. Selecione um cluster HDInsight na lista.
4. Na categoria **Configurações,** selecione **Aplicativos**. Você pode ver uma lista de aplicativos instalados na janela principal. 
   
    ![Aplicativos instalados de aplicativos do HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecione um dos aplicativos instalados para mostrar a propriedade. A propriedades lista:

    |Propriedade | Descrição |
    |---|---|
    |Nome do aplicativo |Nome do aplicativo. |
    |Status |Status da aplicação. |
    |Página da web |A URL do aplicativo Web que você implantou no nó de borda. A credencial é igual às credenciais de usuário HTTP que você configurou para o cluster. |
    |Ponto final do SSH |Você pode usar o SSH para conectar-se ao nó de borda. As credenciais SSH são iguais às credenciais de usuário SSH que você configurou para o cluster. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Descrição | Descrição da aplicação. |

6. Para excluir um aplicativo, clique nele com o botão direito do mouse e clique em **Excluir** no menu de contexto.

## <a name="connect-to-the-edge-node"></a>Conectar-se ao nó de borda
Você pode se conectar ao nó de borda usando HTTP e SSH. As informações de ponto de extremidade podem ser encontradas no [portal](#list-installed-hdinsight-apps-and-properties). Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

As credenciais de ponto de extremidade HTTP são as credenciais de usuário HTTP que você configurou para o cluster HDInsight. As credenciais de ponto de extremidade do SSH são as credenciais SSH que você configurou para o cluster HDInsight.

## <a name="troubleshoot"></a>Solução de problemas
Confira [Solucionar problemas de instalação](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Próximas etapas
* [Instale aplicativos personalizados do HDInsight:](hdinsight-apps-install-custom-applications.md)saiba como implantar um aplicativo HDInsight inédito no HDInsight.
* [Publicar aplicativos do HDInsight](hdinsight-apps-publish-applications.md): saiba como publicar seus aplicativos personalizados do HDInsight no Azure Marketplace.
* [MSDN: instalar um aplicativo do HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): saiba como definir aplicativos do HDInsight.
* [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md): saiba como usar a Ação de Script para instalar aplicativos adicionais.
* [Personalizar clusters Apache Hadoop baseados em Linux no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): saiba como chamar modelos do Resource Manager para criar clusters HDInsight.
* [Usar nós de borda vazios no HDInsight](hdinsight-apps-use-edge-node.md): saiba como usar um nó de borda vazio para acessar o cluster HDInsight, testar e hospedar aplicativos HDInsight.

