---
title: Use o servidor de histórico spark estendido para depurar aplicativos - Apache Spark no Azure Synapse
description: Use o servidor de histórico spark estendido para depurar e diagnosticar aplicativos Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4f03033942517f4778192e0b12f84610df8fd469
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429207"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Use o servidor de histórico Apache Spark estendido para depurar e diagnosticar aplicativos Apache Spark

Este artigo fornece orientações sobre como usar o servidor de histórico Apache Spark estendido para depurar e diagnosticar aplicativos completos e executados spark.

A extensão inclui uma guia de dados, uma guia de gráficos e uma guia de diagnóstico. Use a guia **Dados** para verificar os dados de entrada e saída do trabalho do Spark. A guia **Gráfico** mostra o fluxo de dados e o replay do gráfico de trabalho. A guia **Diagnóstico** mostra a sua **distorção de dados,** **distorção de tempo**e análise de uso do **executor.**

## <a name="access-the-apache-spark-history-server"></a>Acesse o servidor de histórico do Apache Spark

O servidor de histórico do Apache Spark é a interface de usuário da Web para aplicativos Spark concluídos e executados. Você pode abrir a interface web do servidor de histórico do Apache Spark a partir do Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Abra a interface do servidor de histórico spark do nó de aplicativos de ignição Apache

1. Abra [o Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Clique **em Monitorar**e selecione Apache Spark **Applications**.

    ![Clique em monitorar e selecione o aplicativo de ignição.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecione um aplicativo e abra **a consulta Log** clicando nele.

    ![Abra a janela de consulta de log.](./media/apache-spark-history-server/open-application-window.png)

4. Selecione **o servidor de histórico spark,** em seguida, a ui web Spark History Server aparecerá.

    ![Abra o servidor de histórico de faíscas.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Abra a ui web do Spark History Server do nó data

1. No seu notebook Azure Synapse Studio, selecione o **servidor de histórico Spark** na célula de saída de execução de emprego ou no painel de status na parte inferior do documento do notebook. Selecione **Detalhes da sessão**.

   ![Inicie o servidor de histórico spark](./media/apache-spark-history-server/launch-history-server2.png "Inicie o servidor de histórico spark")

2. Selecione **o servidor de histórico Spark** no painel de saída do slide.

   ![Inicie o servidor de histórico spark](./media/apache-spark-history-server/launch-history-server.png "Inicie o servidor de histórico spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Explorar a guia Dados no servidor de histórico spark

Selecione o ID de trabalho para o trabalho que deseja visualizar. Em seguida, selecione **Dados** no menu da ferramenta para obter a exibição de dados. Esta seção mostra como fazer várias tarefas na guia Dados.

* Confira as **Entradas**, as **Saídas** e as **Operações de Tabelas** selecionando as guias separadamente.

    ![Dados para guias de aplicativos Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copie todas as linhas selecionando **Copiar**.

    ![Dados para cópia do aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Salve todos os dados como arquivo CSV selecionando **csv**.

    ![Dados para salvar o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Pesquise inserindo palavras-chave no campo **de pesquisa**. Os resultados da pesquisa são exibidos imediatamente.

    ![Dados para pesquisa de aplicativos Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selecione o cabeçalho da coluna para classificar a tabela, selecione o sinal de acrescido para expandir uma linha para mostrar mais detalhes ou selecione o sinal de menos para colapsar uma linha.

    ![Dados para tabela de aplicação spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Baixe um único arquivo selecionando **Download Parcial**. O arquivo selecionado é baixado no local. Se o arquivo não existir mais, uma nova guia será exibida com uma mensagem de erro.

    ![Linha de download de dados para aplicativo Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Para copiar um caminho completo ou um caminho relativo, selecione as opções **Copiar caminho completo** ou copiar caminho **relativo** que se expandem a partir do menu suspenso. Para arquivos Azure Data Lake Storage, **o Open in Azure Storage Explorer** lança o Azure Storage Explorer e localiza a pasta quando você está conectado.

    ![Data s ida para o caminho da cópia do aplicativo Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecione os números da página abaixo da tabela para navegar nas páginas quando houver muitas linhas para exibir em uma página.

    ![Página de aplicação Dados for Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Passar o tempo no ponto de interrogação ao lado **de Dados** para mostrar a dica de ferramenta ou selecionar o ponto de interrogação para obter mais informações.

    ![Dados para o aplicativo Spark mais informações](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Envie feedback com problemas selecionando **Forneça-nos feedback**.

    ![O gráfico spark nos fornece feedback novamente](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Guia de gráficos no servidor de histórico do Apache Spark

Selecione o ID de trabalho para o trabalho que deseja visualizar. Em seguida, **selecione Gráfico** no menu da ferramenta para obter a exibição do gráfico de trabalho.

### <a name="overview"></a>Visão geral

Você pode ver uma visão geral do seu trabalho no gráfico de emprego gerado. Por padrão, o gráfico mostra todos os trabalhos. Você pode filtrar esta exibição por **Job ID**.

![DI do trabalho de aplicação de faísca suscitou e o trabalho gráfico](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Exibição

Por padrão, a exibição **Progresso** é selecionada. Você pode verificar o fluxo de dados selecionando **Leitura** ou **Escrito** na lista de isto **exibição.**

![Exibição de gráfico de aplicação de faíscas e de trabalho](./media/apache-spark-history-server/sparkui-graph-display.png)

O nó gráfico exibe as cores mostradas na legenda do mapa de calor.

![Mapa de calor de aplicação de faíscas e gráficos de trabalho](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Reprodução

Para reproduzir o trabalho, selecione **Reprodução**. Você pode selecionar **Parar** a qualquer momento para parar. As cores da tarefa mostram diferentes status ao reproduzir:

|Color|Significado|
|-|-|
|Verde|Bem sucedido: O trabalho foi concluído com sucesso.|
|Laranja|Repetido: Casos de tarefas que falharam, mas não afetam o resultado final do trabalho. Essas tarefas tiveram instâncias duplicadas ou repetidas que poderão ser bem-sucedidas mais tarde.|
|Azul|Execução: A tarefa está em execução.|
|Branco|Esperando ou pulando: A tarefa está esperando para correr, ou o palco pulou.|
|Vermelho|Falhou: a tarefa falhou.|

A imagem a seguir mostra as cores de status verde, laranja e azul.

![Amostra de cores de gráfico de faísca e de gráfico de trabalho, em execução](./media/apache-spark-history-server/sparkui-graph-color-running.png)

A imagem a seguir mostra as cores de status verde e branco.

![Amostra de cores de gráfico de faísca e de gráfico de trabalho, pule](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

A imagem a seguir mostra cores de status vermelho e verde.

![Amostra de cores de gráfico de faísca e de gráfico de trabalho, falha](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> É permitida a reprodução de cada trabalho. Para trabalhos incompletos, a reprodução não é suportada.

### <a name="zoom"></a>Zoom

Use o rol do mouse para aumentar e diminuir o zoom no gráfico de trabalho ou selecionar **Zoom para se encaixar** para torná-lo adequado à tela.

![Aplicativo de faísca e zoom de gráfico de trabalho para caber](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Dicas de Ferramenta

Hover no nó gráfico para ver a dica de ferramenta quando houver tarefas fracassadas e selecione um estágio para abrir sua página de estágio.

![Dica de ferramenta de aplicação de faísca suscitar e trabalho gráfico](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na guia gráfico de trabalho, os estágios têm uma dica de ferramenta e um pequeno ícone exibido se tiverem tarefas que atendam às seguintes condições:

|Condição|Descrição|
|-|-|
|Distorção de dados|tamanho da leitura dos dados > tamanho médio de leitura de dados de todas as tarefas dentro deste estágio * 2 e tamanho de leitura de dados > 10 MB|
|Distorção do tempo|tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 e tempo de execução > 2 minutos|
   
![Ícone de distorção de gráfico skew de aplicativo de faísca e de trabalho](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrição do nó gráfico

O nó gráfico de trabalho exibe as seguintes informações de cada etapa:

  * ID.
  * Nome ou descrição.
  * Número total de tarefas.
  * Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.
  * Gravação de dados: a soma do tamanho da saída e do tamanho das gravações de embaralhar.
  * Tempo de execução: o tempo entre a hora de início da primeira tentativa e a hora de conclusão da última tentativa.
  * Contagem de linhas: a soma dos registros de entrada, registros de saída, registros de leitura aleatória e registros de gravação aleatória.
  * Progresso.

    > [!NOTE]  
    > Por padrão, o nó gráfico de trabalho exibe informações da última tentativa de cada etapa (exceto o tempo de execução do estágio). No entanto, durante a reprodução, o nó gráfico mostra informações de cada tentativa.
    >  
    > O tamanho dos dados de leitura e gravação é de 1MB = 1000 KB = 1000 * 1000 Bytes.

### <a name="provide-feedback"></a>Fornecer comentários

Envie feedback com problemas selecionando **Forneça-nos feedback**.

![Feedback de aplicação de faíscas e gráficos de emprego](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Explorar a guia Diagnóstico no servidor de histórico do Apache Spark

Para acessar a guia Diagnóstico, selecione um ID de trabalho. Em seguida, **selecione Diagnóstico** no menu da ferramenta para obter a visualização de diagnóstico de trabalho. A guia de diagnóstico inclui **Distorção de dados**, **Distorção de tempo** e **Análise de uso do executor**.

Confira as informações de **Distorção de dados**, **Distorção de tempo** e **Análise de uso do executor** selecionando as respectivas guias.

![SparkUI diagnóstico dados inclinar guia novamente](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados

Quando você seleciona a guia **'Distorcer dados',** as tarefas enviesadas correspondentes são exibidas com base nos parâmetros especificados.

* **Especificar Parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a Distorção de dados. A regra padrão é: A leitura de dados de tarefaé maior que três vezes a média dos dados da tarefa lida, e os dados de tarefa lidos são mais de 10 MB. Se você quiser definir sua própria regra para tarefas distorcidas, você pode escolher seus parâmetros, as seções **Skewed Stage** e **Skew Char** são atualizadas de acordo.

* **Fase Distorcida** – a segunda seção exibe as fases que têm tarefas distorcidas que atendem aos critérios especificados acima. Se houver mais de uma tarefa distorcida em uma fase, a tabela de fase distorcida exibirá apenas a tarefa mais distorcida (por exemplo, com os maiores dados para distorção de dados).

    ![sparkui diagnóstico dados inclinar guia](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Gráfico de distorção** – Quando uma linha na tabela de estágio skew é selecionada, o gráfico de distorção exibe mais detalhes de distribuição de tarefas com base no tempo de leitura e execução dos dados. As tarefas distorcidas são marcadas em vermelho e as normais são marcadas em azul. O gráfico exibe até 100 tarefas de amostra, e os detalhes da tarefa são exibidos no painel inferior direito.

    ![gráfico de skew sparkui para o estágio 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo

A guia **Distorção de Tempo** exibe tarefas distorcidas com base no tempo de execução da tarefa.

* **Especificar parâmetros** - A primeira seção exibe os parâmetros, que são usados para detectar distorção de tempo. Os critérios padrão para detectar a distorção de tempo são: o tempo de execução da tarefa é maior do que três vezes o tempo médio de execução e o tempo de execução da tarefa é maior que 30 segundos. Você pode alterar os parâmetros com base em suas necessidades. A **Fase Distorcida** e o **Gráfico de Distorção** exibem as informações sobre as fases e as tarefas correspondentes, assim como a guia **Distorção de Dados** acima.

* Selecione **'Distorcer tempo'** e, em seguida, o resultado filtrado é exibido na seção **Estágio distorcido de** acordo com os parâmetros definidos na seção **Especificar parâmetros**. Selecione um item na seção **Estágio distorcido,** em seguida, o gráfico correspondente é redigido na seção3, e os detalhes da tarefa são exibidos no painel inferior direito.

    ![sparkui diagnóstico tempo skew seção](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de uso do executor

O Gráfico de Uso do Executor visualiza a alocação e o status de execução do executor de trabalho Spark.  

1. Selecione **Análise de uso do executor,** em seguida, quatro tipos de curvas sobre o uso do executor são redigidas, incluindo **Executores alocados,** **executores em execução,** **executores ociosos**e **instâncias máximas executoras**. Em relação aos executores alocados, cada evento "Executor adicionado" ou "Executor removido" aumenta ou diminui os executores alocados. Você pode verificar "Linha do tempo do evento" na guia "Trabalhos" para fazer mais comparações.

   ![guia de executores de diagnóstico sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

    ![sparkui diagnósticos selecionar gráfico](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemas conhecidos

Os dados de entrada/saída usando RDDs (Resilient Distributed Datasets, conjuntos de dados distribuídos) não são exibidos na guia de dados.

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

