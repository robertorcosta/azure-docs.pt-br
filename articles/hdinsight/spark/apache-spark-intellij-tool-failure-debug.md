---
title: Debug Spark trabalho com IntelliJ Azure Toolkit (pré-visualização) - HDInsight
description: Orientação usando ferramentas HDInsight no Azure Toolkit para intelliJ para depurar aplicativos
keywords: depurar o intellij remotamente, depuração remota do intellij, ssh, intellij, hdinsight, depurar o intellij, depuração
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494604"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Falha na depuração do trabalho com o Azure Toolkit para IntelliJ (visualização)

Este artigo fornece orientações passo a passo sobre como usar as ferramentas HDInsight no [Azure Toolkit para a IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para executar aplicativos **Spark Failure Debug.**

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de desenvolvimento Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Este tutorial usa o Java versão 8.0.202.
  
* IntelliJ IDEA. Este artigo usa [intelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Confira [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalação do Azure Toolkit for IntelliJ).

* Conecte-se ao seu cluster HDInsight. Consulte [Conecte-se ao seu cluster HDInsight](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Veja [o download do Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Crie um projeto com modelo de depuração

Crie um projeto spark2.3.2 para continuar a depuração de falhas, pegue o arquivo de amostra de depuração de tarefa sumida neste documento.

1. Abra o IntelliJ IDEA. Abra a janela do **Novo Projeto.**

   a. Selecione **Azure Spark/HDInsight** no painel esquerdo.

   b. Selecione **Projeto Spark com Amostra de depuração de tarefas de falha (Scala)** da janela principal.

     ![Intellij Crie um projeto de depuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selecione **Avançar**.

2. Na janela **Novo Projeto**, execute as seguintes etapas:

   ![Intellij New Project seleciona versão Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Insira um nome de projeto e o local do projeto.

   b. Na lista de baixa do **Project SDK,** selecione **Java 1.8** para **cluster Spark 2.3.2.**

   c. Na lista de isto **Spark Version,** selecione **Spark 2.3.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

3. Selecione scala**principal** >  **do src** > para abrir seu código no projeto.**scala** Este exemplo usa o **script AgeMean_Div()**

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Execute um aplicativo Spark Scala/Java em um cluster HDInsight

Crie um aplicativo Scala/Java de faísca e execute o aplicativo em um cluster Spark fazendo as seguintes etapas:

1. Clique **em Adicionar configuração** para abrir a janela **Executar/Depurar configurações.**

   ![Configuração de Adicionar HDI Intellij](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Na caixa de diálogo **Configurações de Execução/Depuração**, selecione o sinal de mais (**+**). Em seguida, selecione a opção **Apache Spark no HDInsight.**

   ![Intellij Adicionar nova configuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Mude para **executar remotamente na** guia Cluster. Insira informações para **Nome,** **Cluster de faísca**e nome da classe **principal**. Nossas ferramentas oferecem suporte à depuração com **Executores**. Os **numExectors**, o valor padrão é 5, e é melhor você não definir acima de 3. Para reduzir o tempo de execução, você pode adicionar **spark.yarn.maxAppAttempts** em **configurações de trabalho** e definir o valor como 1. Clique no botão **OK** para salvar a configuração.

   ![Configurações de depuração intellij Run novas](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Agora, a configuração está salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**.

5. Depois de concluir as configurações, você pode executar o projeto contra o cluster remoto.

   ![Botão de execução remota do trabalho de desoneração da Intellij](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Você pode verificar o ID do aplicativo na janela de saída.

   ![Resultado remoto de execução de trabalho de ignição remota da Intellij Debug](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Baixar perfil de trabalho falhado

Se o envio de trabalho falhar, você pode baixar o perfil de trabalho falhado para a máquina local para depuração adicional.

1. Abra **o Microsoft Azure Storage Explorer**, localize a conta HDInsight do cluster para o trabalho com falha, baixe os recursos de trabalho com falha do local correspondente: **\hdp\spark2-events\\.spark-failures\\\<application ID>** a uma pasta local. A janela **de atividades** mostrará o progresso do download.

   ![Falha no download do Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Sucesso de download do Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configure o ambiente de depuração local e depurar a falha

1. Abra o projeto original ou crie um novo projeto e associe-o ao código-fonte original. A tualmente, apenas a versão spark2.3.2 é suportada para depuração por falhas.

1. Em IntelliJ IDEA, crie um arquivo de configuração **de depuração de falha de ignição,** selecione o arquivo FTD dos recursos de trabalho com falha anteriormente baixados para o campo **de localização de contexto de falha do trabalho spark.**

   ![configuração de falha de creta](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Clique no botão de execução local na barra de ferramentas, o erro será exibido na janela Executar.

   ![configuração de falha de execução1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![configuração de falha de execução2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Defina o ponto de ruptura como o log indica e clique no botão de depuração local para fazer a depuração local assim como seus projetos normais de Scala / Java no IntelliJ.

1. Após a depuração, se o projeto for concluído com sucesso, você poderá reenviar o trabalho com falha para o seu cluster HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Próximas etapas

* [Visão geral: Depurar aplicativos Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstração

* Crie o projeto Scala (vídeo): [Crie aplicativos Scala do Spark do Apache](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: faça análise interativa de dados usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit for IntelliJ para criar aplicativos do Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar ferramentas de HDInsight para IntelliJ com a área restrita do Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
