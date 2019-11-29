---
title: Servidor de histórico do Spark estendido para depurar aplicativos – Azure HDInsight
description: Uso estendido de servidor de histórico do Spark para depurar e diagnosticar aplicativos do Spark – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561822"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Use o Apache Spark History Server estendido para depurar e diagnosticar aplicativos do Apache Spark

Este artigo fornece orientação sobre como usar o Apache Spark History Server estendido para depurar e diagnosticar aplicativos Spark concluídos e em execução. A extensão inclui a guia de dados e a guia gráfico e a guia diagnóstico. Na guia **dados** , os usuários podem verificar os dados de entrada e saída do trabalho do Spark. Na guia **Gráfico**, os usuários podem verificar o fluxo de dados e repetir o gráfico de trabalho. Na guia **diagnóstico** , o usuário pode referir-se à **distorção de dados**, à **distorção de tempo**e à **análise de uso do executor**.

## <a name="get-access-to-apache-spark-history-server"></a>Obtenha acesso ao Apache Spark History Server

O Apache Spark History Server é a interface do usuário da web para aplicativos Spark concluídos e em execução.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Abra a interface da Web do Apache Spark History Server no portal do Azure

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Em **painéis de cluster**, selecione **servidor de histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

    ![Portal de inicialização do Spark servidor de histórico](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Servidor de Histórico do Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Abrir a Interface do Usuário da Web do Servidor de Histórico do Spark por URL

Abra o servidor de histórico do Spark navegando até `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` em que CLUSTERname é o nome do cluster Spark.

A interface do usuário da Web do servidor de histórico do Spark pode ser semelhante a:

![Servidor de Histórico do HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Guia Dados no Servidor de Histórico do Spark

Selecione ID do trabalho e, em seguida, selecione **dados** no menu ferramenta para obter a exibição de dados.

+ Examine as **entradas**, as **saídas**e **as operações de tabela** selecionando as guias separadamente.

    ![Dados para guias do aplicativo Spark](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copie todas as linhas selecionando **copiar**botão.

    ![Dados para cópia do aplicativo Spark](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salve todos os dados como arquivo CSV selecionando o botão **CSV**.

    ![Dados para salvar aplicativo Spark](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Pesquisa digitando palavras-chave no campo **pesquisa**, o resultado da pesquisa será exibida imediatamente.

    ![Dados para pesquisa de aplicativo Spark](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selecione o cabeçalho de coluna para classificar a tabela, selecione o sinal de adição para expandir uma linha para mostrar mais detalhes ou selecione o sinal de menos para recolher uma linha.

    ![Dados para a tabela de aplicativos Spark](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Baixar arquivo único selecionando o botão **Download parcial** no local, o arquivo selecionado será baixado para local, se o arquivo não existir mais, será aberta uma nova guia para mostrar as mensagens de erro.

    ![Dados para a linha de download do aplicativo Spark](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiar caminho completo ou relativo, selecionando o **Copiar caminho completo**, **Copiar caminho relativo** que se expande de menu download. Para arquivos de armazenamento do Azure data Lake, **abra o Gerenciador de armazenamento do Azure** será iniciado Gerenciador de armazenamento do Azure e localize a pasta ao entrar.

    ![Dados para o caminho de cópia do aplicativo Spark](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Selecione o número abaixo da tabela para navegar pelas páginas quando muitas linhas forem exibidas em uma página.

    ![Dados para a página do aplicativo Spark](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Focalize o ponto de interrogação ao lado dos dados para mostrar a dica de ferramenta ou selecione o ponto de interrogação para obter mais informações.

    ![Dados para o aplicativo Spark mais informações](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Enviar comentários com problemas clicando **fornecer comentários**.

    ![O grafo do Spark nos fornece comentários novamente](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Guia Graph no Apache Spark History Server

Selecione o ID do trabalho e clique em **dados** no menu de ferramenta para obter a exibição de dados.

+ Examine a visão geral de seu trabalho pelo grafo de trabalho gerado.

+ Por padrão, ele mostrará todos os trabalhos, e ele pode ser filtrado por **ID do trabalho**.

    ![Aplicativo Spark e ID do trabalho do grafo do trabalho](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Por padrão, **Progresso** é selecionado, o usuário pode verificar o fluxo de dados selecionando **Leitura / Escrita** na lista suspensa de **Exibição**.

    ![Vídeo do aplicativo Spark e do grafo de trabalho](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    A exibição do nó de gráfico na cor que mostra o mapa de calor.

    ![Aplicativo Spark e grafo de trabalho calor](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Reproduza o trabalho selecionando o botão de **reprodução** e pare a qualquer momento selecionando o botão parar. A exibição de tarefas na cor para mostrar o status diferentes quando a reprodução:

    |Cor |Descrição |
    |---|---|
    |Verde|O trabalho foi concluído com sucesso.|
    |Orange|Instâncias de tarefas que falharam, mas não afetam o resultado final do trabalho. Essas tarefas tinham duplicar ou repita instâncias que podem ter êxito posteriormente.|
    |Azul|A tarefa está em execução.|
    |Branco|A tarefa está aguardando para ser executada ou o estágio foi ignorado.|
    |Vermelho|Falha da tarefa.|

    ![Exemplo de cor do grafo de aplicativo e trabalho do Spark, executando](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    O estágio ignorado é exibido em branco.
    ![amostra de cor do grafo de aplicativo e trabalho do Spark, ignorar](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Exemplo de cor de grafo de trabalho e aplicativo Spark, com falha](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > Reprodução para cada trabalho é permitida. Para o trabalho incompleto, não há suporte para reprodução.

+ Rola o mouse para aplicar zoom de entrada/saída grafo do trabalho, ou clique em **Aplicar Zoom para ajustar** para torná-lo a ajustar à tela.

    ![Ajuste de zoom do grafo de trabalho e aplicativo Spark](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Passe o mouse no nó de gráfico para ver a dica de ferramenta quando há falha de tarefas e clique no Palco para abrir a página de estágio.

    ![Dica de ferramenta de aplicativo Spark e grafo de trabalho](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na guia de gráfico de trabalho, estágios terão dicas de ferramenta e um ícone pequeno exibidos se tiverem tarefas que atendam às condições abaixo:
  + Distorção de dados: tamanho de dados lidos > tamanho médio de dados lidos de todas as tarefas dentro deste estágio * 2 e tamanho de dados lidos > 10 MB.
  + Distorção de tempo: tempo de execução > tempo médio de execução de todas as tarefas dentro deste estágio * 2 e tempo de execução > 2 min.

    ![Ícone de distorção do grafo de trabalho e aplicativo Spark](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ O nó de gráfico do trabalho exibirá as informações a seguir de cada estágio:
  + ID.
  + Nome ou descrição.
  + Número total de tarefas.
  + Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.
  + Gravação de dados: a soma do tamanho de saída e em ordem aleatória de tamanho de gravação.
  + Tempo de execução: o tempo entre a hora de início da primeira tentativa e tempo de conclusão da última tentativa.
  + Contagem de linhas: a soma dos registros de entrada, registros de saída, em ordem aleatória de registros de leitura e gravação registros em ordem aleatória.
  + Progresso.

    > [!NOTE]  
    > Por padrão, o nó de gráfico do trabalho exibirá informações da última tentativa de cada estágio (com exceção de tempo de execução do estágio), mas durante o gráfico de reprodução de nó mostrará informações de cada tentativa.

    > [!NOTE]  
    > Para o tamanho dos dados de leitura e gravação que podemos usar 1 MB = 1000 KB = 1000 * 1000 Bytes.

+ Envie comentários com problemas selecionando **fornecer comentários**.

    ![Comentários do grafo de trabalho e aplicativo Spark](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Guia Diagnóstico no Apache Spark History Server

Selecione ID do trabalho e, em seguida, selecione **diagnóstico** no menu ferramenta para obter a exibição de diagnóstico do trabalho. A guia Diagnóstico inclui **Distorção de Dados**, **Distorção de Tempo** e **Análise de Uso do Executor**.

+ Examine a **distorção de dados**, a **distorção de tempo**e a análise de uso do **executor** selecionando as guias respectivamente.

    ![Guia de distorção de dados de diagnóstico SparkUI novamente](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados

Selecione a guia **distorção de dados** , as tarefas distorcidas correspondentes são exibidas com base nos parâmetros especificados.

+ **Especificar parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a distorção de dados. A regra interna é: a leitura de dados da tarefa é maior que três vezes a média de dados da tarefa lida e a leitura dos dados da tarefa é maior que 10 MB. Se você quiser definir sua própria regra para tarefas distorcidas, poderá escolher seus parâmetros, o **Estágio Distorcido** e a seção **Char de Distorção** será atualizada adequadamente.

+ **Estágio distorcido** – a segunda seção exibe os estágios, que têm tarefas distorcidas que atendem aos critérios especificados acima. Se houver mais de uma tarefa distorcida em um estágio, a tabela de estágio distorcido exibirá apenas a tarefa mais distorcida (por exemplo, os maiores dados para distorção de dados).

    ![Guia de distorção de dados de diagnóstico sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Gráfico de Distorção** – quando uma linha na tabela de estágio de distorção é selecionada, o gráfico de distorção exibe mais detalhes de distribuição de tarefa com base nos dados lidos e no tempo de execução. As tarefas distorcidas são marcadas em vermelho e as tarefas normais são marcadas em azul. Para considerações sobre desempenho, o gráfico exibe apenas até 100 tarefas de exemplo. Os detalhes da tarefa são exibidos no painel inferior direito.

    ![gráfico de distorção sparkui para o estágio 10](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo

A guia **Distorção de Tempo** exibe tarefas distorcidas com base em tempo de execução de tarefa.

+ **Especificar parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a distorção de tempo. Os critérios padrão para detectar a distorção de tempo são: o tempo de execução da tarefa é maior do que três vezes o tempo médio de execução e o tempo de execução da tarefa é maior que 30 segundos. Você pode alterar os parâmetros de acordo com suas necessidades. **Estágio Distorcido** e **Gráfico de Distorção** exibem as informações de tarefas e estágios correspondentes exatamente como na guia **Distorção de Dados** acima.

+ Selecione **distorção de tempo**e o resultado filtrado é exibido na seção de **estágio distorcido** de acordo com os parâmetros definidos na seção **especificar parâmetros**. Selecione um item na seção **estágio inclinado** , o gráfico correspondente é exibido em section3 e os detalhes da tarefa são exibidos no painel inferior direito.

    ![seção de distorção de tempo de diagnóstico sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de uso do executor

O Gráfico de Uso do Executor visualiza o status de execução e alocação do executor real do trabalho do Spark.  

+ Selecione **análise de uso de executor**, quatro tipos de curvas de uso de executor são rascunhos, incluindo **executores alocados**, **execuções em execução**, **executores ociosos**e **instâncias de executor máximo**. Com relação a executores alocados, cada evento de "Executor adicionado" ou "Executor removido" aumentará ou diminuirá os executores alocados, você pode verificar "Linha do Tempo do Evento" na guia "Trabalhos" para mais comparação.

    ![guia executores de diagnóstico do sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

    ![gráfico de seleção de diagnóstico sparkui](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Perguntas Frequentes

### <a name="1-revert-to-community-version"></a>1. reverter para a versão da Comunidade

Para reverter para a versão de comunidade, execute as seguintes etapas:

1. Cluster aberto no Ambari.
1. Navegue até **Spark2** > **configurações** > **Spark2 personalizadas-padrões**.
1. Selecione **Adicionar Propriedade...** , adicionar **Spark. UI. Enhancement. Enabled = False**, salvar.
1. A propriedade define **falsos** agora.
1. Selecione **Salvar** para salvar a configuração.

    ![O recurso Apache Ambari desliga](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selecione **Spark2** no painel esquerdo, na guia **Resumo** , selecione **servidor de histórico Spark2**.

    ![Exibição de resumo do Apache Ambari Spark2](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Reinicie o servidor de histórico selecionando **reiniciar** o **servidor de histórico Spark2**.

    ![Reinicialização do histórico de Spark2 do Apache Ambari](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Atualizar a web de servidor de histórico do Spark da interface do usuário, ele será revertido para a versão da comunidade.

### <a name="2-upload-history-server-event"></a>2. carregar evento do servidor de histórico

Se você encontrar erros de servidor de histórico, siga as etapas para fornecer o evento:

1. Baixe o evento selecionando **baixar** na interface do usuário da Web do servidor de histórico.

    ![Download do servidor de histórico do Spark2](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selecione **fornecer comentários dos EUA** na guia dados/grafo.

    ![O grafo do Spark fornece comentários](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Forneça o título e descrição do erro, arraste o arquivo zip para o campo de edição, e clique em **enviar novo problema**.

    ![exemplo de problema de arquivo do Apache Spark](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. atualizar o arquivo JAR para o cenário de hotfix

Se você quiser atualizar com o hotfix, use o script a seguir, que atualizará enhancement.jar* spark.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Uso**:

`upgrade_spark_enhancement.sh https://${jar_path}`

**Exemplo**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Para usar o arquivo de bash do portal do Azure**

1. Inicie o [portal do Azure](https://ms.portal.azure.com)e selecione o cluster.
2. Conclua uma [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) com os seguintes parâmetros:

    |Propriedade |Value |
    |---|---|
    |Tipo de script|- Personalizado|
    |name|UpgradeJar|
    |URI do script Bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo(s) de nó|Cabeçalho, trabalhador|
    |parâmetros|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Ação de enviar script portal do Azure](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemas conhecidos

+ Atualmente, ele funciona apenas para o cluster Spark 2,3 e 2,4.

+ Os dados de entrada/saída usando RDD não serão mostrados na guia dados.

## <a name="next-steps"></a>Próximos passos

+ [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
+ [Definir as configurações do Apache Spark](apache-spark-settings.md)

## <a name="contact-us"></a>Contate-nos

Se você tiver algum comentário ou se surgir algum problema ao usar essa ferramenta, envie um email em ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).
