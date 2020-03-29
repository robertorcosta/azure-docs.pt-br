---
title: Use os recursos estendidos no Apache Spark History Server para depurar aplicativos - Azure HDInsight
description: Use os recursos estendidos no Apache Spark History Server para depurar e diagnosticar aplicativos Spark - Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 5cf1986711479f7330b0cd477744d9f4e2ac6459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548927"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Use os recursos estendidos do Apache Spark History Server para depurar e diagnosticar aplicativos Spark

Este artigo mostra como usar os recursos estendidos do Apache Spark History Server para depurar e diagnosticar aplicativos completos ou executados spark. A extensão inclui uma guia **Dados,** uma guia **Gráfico** e uma guia **Diagnóstico.** Na guia **Dados,** você pode verificar os dados de entrada e saída do trabalho Spark. Na guia **Gráfico,** você pode verificar o fluxo de dados e repetir o gráfico de trabalho. Na guia **Diagnóstico,** você pode consultar os recursos **Data Skew,** **Time Skew**e **Executor Use Analysis.**

## <a name="get-access-to-the-spark-history-server"></a>Acesse o Servidor de Histórico de Faíscas

O Spark History Server é a interface do usuário da Web para aplicativos Spark concluídos e executados. Você pode abri-lo no portal Azure ou em uma URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Abra a ui web do Spark History Server a partir do portal Azure

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Em **cluster dashboards,** selecione **Servidor de histórico spark**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

    ![Inicie o Spark History Server do portal Azure.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Servidor de Histórico do Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Abra a ui web do Spark History Server por URL

Abra o Servidor de `https://CLUSTERNAME.azurehdinsight.net/sparkhistory`Histórico de Faíscas navegando para , onde **CLUSTERNAME** é o nome do seu cluster Spark.

A ui web do Spark History Server pode ser semelhante a esta imagem:

![A página do Servidor de Histórico de Faíscas.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Use a guia Dados no Servidor histórico de faíscas

Selecione o ID de trabalho e selecione **Dados** no menu da ferramenta para ver a exibição de dados.

+ Revisar **entradas,** **saídas**e **operações de tabela** selecionando as guias individuais.

    ![Guias de dados na página Dados para Aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copie todas as linhas selecionando o botão **Copiar.**

    ![Copiar dados na página do aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salve todos os dados como . Arquivo CSV selecionando o botão **csv.**

    ![Salvar dados como um . Arquivo CSV da página Data for Spark Application.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Pesquise os dados inserindo palavras-chave no campo **Pesquisar.** Os resultados da pesquisa serão exibidos imediatamente.

    ![Pesquise dados na página Dados para Aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selecione o cabeçalho da coluna para classificar a tabela. Selecione o sinal de acrescido para expandir uma linha para mostrar mais detalhes. Selecione o sinal de menos para colapsar uma linha.

    ![A tabela de dados na página Dados para Aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Baixe um único arquivo selecionando o botão **Download Parcial** à direita. O arquivo selecionado será baixado localmente. Se o arquivo não existir mais, isso abrirá uma nova guia para mostrar as mensagens de erro.

    ![A linha de download de dados na página Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copie um caminho completo ou um caminho relativo selecionando a opção **Copiar caminho completo** ou copiar caminho **relativo,** que se expande a partir do menu de download. Para os arquivos Azure Data Lake Storage, selecione **Abrir no Azure Storage Explorer** para iniciar o Azure Storage Explorer e localize a pasta após o login.

    ![Copie as opções caminho completo e caminho relativo de cópia na página Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Se houver muitas linhas para exibir em uma única página, selecione os números de página na parte inferior da tabela para navegar.

    ![Números de página na página Dados para Aplicativo Desmembrá.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Para obter mais informações, rodopor ou selecionar o ponto de interrogação ao lado **do Aplicativo Data for Spark** para mostrar a dica de ferramenta.

    ![Obtenha mais informações na página Data for Spark Application.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Para enviar feedback sobre os problemas, selecione **Fornecer-nos feedback**.

    ![Forneça feedback da página Dados para O Aplicativo Spark.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Use a guia Gráfico no Servidor histórico de faíscas

+ Selecione o ID de trabalho e, em seguida, selecione **Gráfico** no menu da ferramenta para ver o gráfico de trabalho. Por padrão, o gráfico mostrará todos os trabalhos. Filtre os resultados usando o menu suspenso **do Job ID.**

    ![O menu suspenso do Job ID na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ **O progresso** é selecionado por padrão. Verifique o fluxo de dados selecionando **Ler** ou **Escrever** no menu suspenso **Exibir.**

    ![Verifique o fluxo de dados na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ A cor de fundo de cada tarefa corresponde a um mapa de calor.

   ![O mapa de calor na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |Descrição |
    |---|---|
    |Verde|o trabalho foi concluído com êxito.|
    |Laranja|A tarefa falhou, mas isso não afeta o resultado final do trabalho. Essas tarefas têm instâncias duplicadas ou de repetição que podem ter sucesso mais tarde.|
    |Azul|a tarefa está em execução.|
    |Branco|a tarefa está aguardando para ser executada ou a fase foi ignorada.|
    |Vermelho|a tarefa falhou.|

     ![Executando uma tarefa na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Os estágios pulados são exibidos em branco.
    ![Uma tarefa ignorada na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Uma tarefa falhada na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > A reprodução está disponível para trabalhos concluídos. Selecione o botão **Reprodução** para reproduzir o trabalho. Interrompa o trabalho a qualquer momento, selecionando o botão stop. Quando um trabalho é reproduzido, cada tarefa exibirá seu status por cor. A reprodução não é suportada para trabalhos incompletos.

+ Role para aumentar ou diminuir o zoom no gráfico de trabalho ou selecione **Zoom para que** ele se encaixe na tela.

    ![Selecione Zoom para caber na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Quando as tarefas falham, deslize o nó do gráfico para ver a dica de ferramenta e selecione o estágio para abri-la em uma nova página.

    ![Exibir a dica de ferramenta na página do Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Na página Spark Application & Job Graph, as etapas exibirão dicas de ferramentas e pequenos ícones se as tarefas atenderem a essas condições:
  + Distorção de dados: Tamanho da leitura de dados > tamanho médio de leitura de todos os trabalhos dentro deste estágio * 2 *e* tamanho de leitura de dados > 10 MB.
  + Distorção de tempo: Tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 *e* tempo de execução > 2 min.

    ![O ícone de tarefa distorcido na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ O nó gráfico de trabalho exibirá as seguintes informações sobre cada etapa:
  + ID
  + Nome ou descrição
  + Número total da tarefa
  + Dados lidos: a soma do tamanho da entrada e do tamanho da leitura embaralhada
  + Gravação de dados: a soma do tamanho da saída e do tamanho da gravação embaralhada
  + Tempo de execução: o tempo entre o tempo de início da primeira tentativa e o tempo de conclusão da última tentativa
  + Contagem de linhas: a soma dos registros de entrada, registros de saída, embaralhar registros de leitura e embaralhar registros de gravação
  + Andamento

    > [!NOTE]  
    > Por padrão, o nó gráfico de trabalho exibirá informações da última tentativa de cada etapa (exceto o tempo de execução do estágio). Mas durante a reprodução, o nó gráfico de trabalho mostrará informações sobre cada tentativa.

    > [!NOTE]  
    > Para os tamanhos de gravação de dados e de dados, usamos 1MB = 1000 KB = 1000 * 1000 bytes.

+ Envie comentários sobre problemas selecionando **Forneça-nos feedback**.

    ![A opção de feedback na página Spark Application & Job Graph.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Use a guia Diagnóstico no Servidor histórico de faíscas

Selecione o ID de trabalho e selecione **Diagnóstico** no menu da ferramenta para ver a visualização do diagnóstico de trabalho. A guia **Diagnóstico** inclui **distorção de dados,** **distorção de tempo**e análise de uso do **executor.**

+ Revisar **dados Skew,** **Time Skew**e **Executor Use Use Analysis** selecionando as guias, respectivamente.

    ![A guia 'Distorcer dados' na guia Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados

Selecione a guia **'Distorcer dados'.** As tarefas distorcidas correspondentes são exibidas com base nos parâmetros especificados.

#### <a name="specify-parameters"></a>Especificar parâmetros

A **seção Especificar parâmetros** exibe os parâmetros, que são usados para detectar distorção de dados. A regra padrão é: A leitura de dados da tarefa é maior do que três vezes acima da média dos dados da tarefa lida, e os dados da tarefa lidos são mais de 10 MB. Se você quiser definir sua própria regra para tarefas distorcidas, você pode escolher seus parâmetros. As seções **Skewed Stage** e **Skew Chart** serão atualizadas de acordo.

#### <a name="skewed-stage"></a>Palco distorcido

A **seção Fase Enviesada** exibe etapas que distorceram tarefas que atendem aos critérios especificados. Se houver mais de uma tarefa enviesada em um estágio, a seção **Estágio distorcido** exibirá apenas a tarefa mais enviesada (ou seja, os maiores dados para distorção de dados).

![Visão maior da guia 'Distorcer dados' na guia Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Gráfico de distorção

Quando você seleciona uma linha na tabela **'Distorcer estágio',** o **Gráfico de distorção** exibe mais detalhes de distribuição de tarefas com base no tempo de leitura e execução dos dados. As tarefas distorcidas são marcadas em vermelho, e as tarefas normais são marcadas em azul. Para consideração de desempenho, o gráfico exibe até 100 tarefas amostrais. Os detalhes da tarefa são exibidos no painel inferior direito.

![O Gráfico de Inclinação para o Estágio 10 na UI Spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo

A guia **Distorção de Tempo** exibe tarefas distorcidas com base no tempo de execução da tarefa.

#### <a name="specify-parameters"></a>Especificar parâmetros

A **seção Especificar parâmetros** exibe os parâmetros, que são usados para detectar distorção de tempo. A regra padrão é: O tempo de execução da tarefa é maior que três vezes o tempo médio de execução, e o tempo de execução da tarefa é superior a 30 segundos. Você pode alterar os parâmetros com base em suas necessidades. O **Gráfico de fase e** **inclinação** distorcido exibe as informações de etapas e tarefas correspondentes, assim como na guia **Distorcer dados.**

Quando você seleciona **'Distorcer tempo',** o resultado filtrado é exibido na seção **Estágio distorcido, de** acordo com os parâmetros definidos na seção **Especificar parâmetros.** Quando você seleciona um item na seção **Estágio distorcido,** o gráfico correspondente é redigido na terceira seção e os detalhes da tarefa são exibidos no painel inferior direito.

![A guia 'Tempo de desvio' dentro da guia Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Gráficos de análise de uso do executor

O **Gráfico de Uso do Executor** exibe a alocação real do executor e o status de execução do executor do trabalho.  

Quando você seleciona **Análise de Uso do Executor,** quatro curvas diferentes sobre o uso do executor são redigidas: **Executores alocados,** **executores em execução,** **executores ociosos**e **instâncias máximas de executor.** Cada **evento adicionado ou** **executor removido** aumentará ou diminuirá os executores alocados. Você pode verificar **o Cronograma de Eventos** na guia **Empregos** para obter mais comparações.

![A guia Análise de Uso do Executor está na guia Diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

 ![Selecione o gráfico na guia Análise de uso do executor.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-revert-to-the-community-version"></a>Como faço para reverter para a versão comunitária?

Para reverter para a versão da comunidade, faça as seguintes etapas.

1. Abra o cluster em Ambari.
1. Navegue até **Spark2** > **Configs**.
1. Selecione **padrões personalizados de spark2**.
1. Selecione **Adicionar propriedade ...**.
1. Adicione **spark.ui.enhancement.enabled=false**e, em seguida, salve-o.
1. A propriedade define **falsos** agora.
1. Selecione **Salvar** para salvar a configuração.

    ![Desligue um recurso no Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selecione **Spark2** no painel esquerdo. Em seguida, na guia **Resumo,** selecione **Servidor de histórico spark2**.

    ![A vista sumária em Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Para reiniciar o Servidor de Histórico de Faíscas, selecione o botão **Iniciado** à direita do **Servidor de Histórico Spark2**e selecione **Reiniciar** no menu suspenso.

    ![Reinicie o Servidor de Histórico de Faíscas no Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Atualize a ui web do Spark History Server. Ele vai reverter para a versão da comunidade.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Como faço para carregar um evento do Spark History Server para reportá-lo como um problema?

Se você encontrar um erro no Spark History Server, faça as seguintes etapas para relatar o evento.

1. Baixe o evento selecionando **Download** na ui web Do Spark History Server.

    ![Baixe o evento na UI do Spark History Server.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selecione **Forneça-nos feedback** da página **Spark Application & Job Graph.**

    ![Forneça feedback na página do Spark Application & Job Graph](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Forneça o título e uma descrição do erro. Em seguida, arraste o arquivo .zip para o campo de edição e **selecione Enviar novo problema**.

    ![Faça upload e envie uma nova edição.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Como atualizo um arquivo .jar em um cenário hotfix?

Se você quiser atualizar com um hotfix, use `spark-enhancement.jar*`o script a seguir, que será atualizado .

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

#### <a name="usage"></a>Uso

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Exemplo

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Use o arquivo bash do portal Azure

1. Inicie o [portal Azure](https://ms.portal.azure.com)e selecione seu cluster.
2. Complete uma [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) com os seguintes parâmetros.

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|UpgradeJar|
    |URI do script Bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo(s) de nó|Chefe, Operário|
    |Parâmetros|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Portal Azure envia ação de script](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemas conhecidos

+ Atualmente, o Spark History Server só funciona para o Spark 2.3 e 2.4.

+ Os dados de entrada e saída que usam RDD não serão exibidos na guia **Dados.**

## <a name="next-steps"></a>Próximas etapas

+ [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
+ [Definir configurações do Apache Spark](apache-spark-settings.md)

## <a name="feedback"></a>Comentários

Se você tiver algum feedback ou se deparar com qualquer[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)problema ao usar esta ferramenta, envie um e-mail para ( ).
