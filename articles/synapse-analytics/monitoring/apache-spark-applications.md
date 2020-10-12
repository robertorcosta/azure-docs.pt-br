---
title: Monitorar Apache Spark aplicativos usando o Synapse Studio
description: Use o Synapse Studio para monitorar seus aplicativos Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 4d6c50436ddf68e2610aeb10ddfaaab0a5d060f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387346"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Use o Synapse Studio para monitorar seus aplicativos Apache Spark

Com o Azure Synapse Analytics, você pode usar Apache Spark para executar blocos de anotações, trabalhos e outros tipos de aplicativos em seus pools de Apache Spark em seu espaço de trabalho.

Este artigo explica como monitorar seus aplicativos Apache Spark, permitindo que você fique atento ao status, aos problemas e ao progresso mais recentes.

Este tutorial cobre as seguintes tarefas:

* Monitorar a execução do aplicativo Apache Spark
* Exibir Apache Spark aplicativo concluído
* Exibir Apache Spark aplicativo cancelado
* Falha na depuração Apache Spark aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se você atende aos seguintes requisitos:

- Um espaço de trabalho do Synapse Studio. Para obter instruções, consulte [criar um espaço de trabalho do Synapse Studio](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Um pool do Apache Spark.

## <a name="monitor-running-apache-spark-application"></a>Monitorar a execução do aplicativo Apache Spark

Abra o **Monitor**e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os Apache Spark aplicativos que estão em execução, selecione o Apache Spark aplicativo de envio e exiba os detalhes. Se o aplicativo Apache Spark ainda estiver em execução, você poderá monitorar o progresso.

  ![selecionar trabalho em execução](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Verifique as **tarefas concluídas**, o **status**e a **duração total**.

2. Cancele o aplicativo Apache Spark.

3. Atualizar consulta de log.

4. Exiba o grafo.

5. Verifique as informações de **Resumo** .

6. Verifique os **logs**. As informações de log estão vazias durante a execução.

    ![Exibir trabalho em execução](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Exibir Apache Spark aplicativo concluído

Abra o **Monitor**e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os aplicativos Apache Spark concluídos, selecione o aplicativo Apache Spark e exiba os detalhes.

  ![selecionar trabalho concluído](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Verifique as **tarefas concluídas**, o **status**e a **duração total**.

2. Atualizar consulta de log.

3. Abra Apache Spark link do servidor de histórico clicando em **servidor de histórico do Spark**.

4. Verifique as informações de **Resumo** clicando no ícone no grafo.

5. Verifique os **logs**. Você pode selecionar diferentes tipos de logs na lista suspensa e pode baixar as informações de log clicando em **baixar logs**.

6. Você pode ver uma visão geral do seu trabalho no grafo de trabalho gerado. Por padrão, o grafo mostra todos os trabalhos. Você pode filtrar essa exibição por **ID do trabalho**.

7. Por padrão, a exibição de **progresso** é selecionada. Você pode verificar o fluxo de dados selecionando **ler** ou **gravado** na lista suspensa **Exibir** .

8. Para reproduzir o trabalho, selecione **reprodução**. Você pode selecionar **parar** a qualquer momento para parar.

9. Use a rolagem do mouse para ampliar e reduzir no grafo do trabalho ou selecione **aplicar zoom para ajustá** -lo à tela.

10. O nó do gráfico de trabalho exibe as seguintes informações de cada estágio:

    * ID.

    * Nome ou descrição.

    * Número total de tarefas.

    * Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.

    * Gravação de dados: a soma do tamanho da saída e das gravações de ordem aleatória.

    * Tempo de execução: o tempo entre a hora de início da primeira tentativa e a hora de conclusão da última tentativa.

    * Contagem de linhas: a soma dos registros de entrada, registros de saída, registros de leitura aleatória e registros de gravação aleatória.

    * Progresso.

     ![Exibir trabalho concluído](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Clique no grafo, então os detalhes do estágio serão mostrados.

   ![detalhes do estágio](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Exibir Apache Spark aplicativo cancelado

Abra o **Monitor**e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os aplicativos Apache Spark cancelados, selecione o aplicativo Apache Spark e exiba os detalhes.

 ![selecionar trabalho cancelado](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Verifique as **tarefas concluídas**, o **status**e a **duração total**.

2. Atualize a consulta de log.

3. Abra o link do servidor de histórico do Apache clicando em **servidor de histórico do Spark**.

4. Exiba o grafo.

5. Verifique as informações de **Resumo** .

6. Verifique os **logs**. Você pode selecionar diferentes tipos de logs na lista suspensa e pode baixar as informações de log clicando em **baixar logs**.

   ![Exibir trabalho cancelado](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Falha na depuração Apache Spark aplicativo

Abra o **Monitor**e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os aplicativos Apache Spark com falha, selecione o aplicativo Apache Spark e exiba os detalhes.

![selecionar trabalho com falha](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Verifique as **tarefas concluídas**, o **status**e a **duração total**.

2. Atualizar consulta de log.

3. Abra Apache Spark link do servidor de histórico clicando em **servidor de histórico do Spark**.

4. Exiba o grafo.

5. Verifique as informações de **Resumo** .

6. Verifique as informações do erro.

   ![informações do trabalho com falha](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar execuções de pipeline usando o Synapse Studio](how-to-monitor-pipeline-runs.md) .  
