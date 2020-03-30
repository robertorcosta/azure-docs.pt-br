---
title: Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho no Visual Studio
description: Este artigo descreve como depurar consultas localmente usando o diagrama de trabalho no Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847193"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho no Visual Studio

Trabalhos que não resultam em resultados ou resultados inesperados são cenários comuns de solução de problemas para consultas de streaming. Você pode usar o diagrama de trabalho enquanto testa sua consulta localmente no Visual Studio para examinar o conjunto de resultados intermediário e as métricas para cada etapa. Diagramas de trabalho podem ajudá-lo a isolar rapidamente a fonte de um problema quando você soluciona problemas.

## <a name="debug-a-query-using-job-diagram"></a>Depurar uma consulta usando o diagrama de trabalho

Um script Do Azure Stream Analytics é usado para transformar dados de entrada em dados de saída. O diagrama de trabalho mostra como os dados fluem de fontes de entrada (Event Hub, IoT Hub, etc.) através de várias etapas de consulta e, finalmente, para sinks de saída. Cada etapa de consulta é mapeada para um `WITH` conjunto de resultados temporário definido no script usando uma declaração. Você pode visualizar os dados, bem como as métricas de cada etapa de consulta em cada resultado intermediário definido para encontrar a fonte de um problema.

> [!NOTE]
> Este diagrama de trabalho mostra apenas os dados e métricas para testes locais em um único nó. Não deve ser usado para ajuste de desempenho e solução de problemas.

### <a name="start-local-testing"></a>Inicie os testes locais

Use este [Quickstart](stream-analytics-quick-create-vs.md) para aprender como criar um trabalho de Stream Analytics usando o Visual Studio ou [exportar um trabalho existente para um projeto local](stream-analytics-vs-tools.md#export-jobs-to-a-project). Se você quiser testar a consulta com dados de entrada locais, siga [estas instruções](stream-analytics-live-data-local-testing.md). Se você quiser testar com entrada ao vivo, passe para o próximo passo.

> [!NOTE]
> Se você exportar um trabalho para o projeto local e quiser testar contra uma transmissão de entrada ao vivo, você precisa especificar as credenciais para todas as entradas novamente.  

Escolha a fonte de entrada e saída do editor de script e **selecione Executar localmente**. O diagrama de trabalho aparece no lado direito.

### <a name="view-the-intermediate-result-set"></a>Ver o conjunto de resultados intermediários  

1. Selecione a etapa de consulta para navegar até o script. Você é automaticamente direcionado para o script correspondente no editor à esquerda.

   ![Script de navegação de diagrama de trabalho](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selecione a etapa de consulta e selecione **Visualizar 'Visualizar'** na caixa de diálogo apareceu. O conjunto de resultados é mostrado em uma guia na janela de resultado inferior.

   ![Resultado da visualização do diagrama do trabalho](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Ver métricas de etapas

Nesta seção, você explora as métricas disponíveis para cada parte do diagrama.

#### <a name="input-sources-live-stream"></a>Fontes de entrada (transmissão ao vivo)

![Fontes de entrada ao vivo do diagrama de trabalho](./media/debug-locally-using-job-diagram/live-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| Nome da entrada.|
|**Hub de Eventos** | Tipo de fonte de entrada.|
|**Eventos**|O número de eventos lidos.|
|**Fontes de eventos com atraso**|Quantas mensagens mais precisam ser lidas para hubs de eventos e entradas do IoT Hub.|
|**Eventos em Bytes**|O número de bytes lidos.|
| **Eventos degradados**|A contagem de eventos que tiveram um problema além da desserialização.|
|**Eventos Iniciais**| O número de eventos que possuem um carimbo de tempo de aplicação antes da marca d'água alta.|
|**Eventos Tardios**| O número de eventos que possuem um carimbo de tempo de aplicação após a marca d'água alta.|
|**Origens de eventos**| O número de unidades de dados lidas. Por exemplo, o número de blobs.|

#### <a name="input-sources-local-input"></a>Fontes de entrada (entrada local)

![Fontes de entrada locais do diagrama de trabalho](./media/debug-locally-using-job-diagram/local-input.png)

|Métrica|Descrição|
|-|-|
|**TaxiRide**| Nome da entrada.|
|**Contagem de Linhas**| O número de linhas geradas a partir da etapa.|
|**Tamanho dos dados**| O tamanho dos dados gerados a partir desta etapa.|
|**Entrada local**| Use os dados locais como entrada.|

#### <a name="query-steps"></a>Etapas de consulta

![Etapa de consulta de diagrama de trabalho](./media/debug-locally-using-job-diagram/query-step.png)

|Métrica|Descrição|
|-|-|
|**TripData**|O nome do conjunto de resultados temporários.|
|**Contagem de Linhas**| O número de linhas geradas a partir da etapa.|
|**Tamanho dos dados**| O tamanho dos dados gerados a partir desta etapa.|
  
#### <a name="output-sinks-live-output"></a>Afundos de saída (saída ao vivo)

![A saída local do diagrama de trabalho afunda](./media/debug-locally-using-job-diagram/live-output.png)

|Métrica|Descrição|
|-|-|
|**regionaggEH**|Nome da saída.|
|**Eventos**|O número de eventos que saem para afundar.|

#### <a name="output-sinks-local-output"></a>Afundos de saída (saída local)

![A saída local do diagrama de trabalho afunda](./media/debug-locally-using-job-diagram/local-output.png)

|Métrica|Descrição|
|-|-|
|**regionaggEH**|Nome da saída.|
|**Saída Local**| Saída de resultado para um arquivo local.|
|**Contagem de Linhas**| O número de linhas de saída para o arquivo local.|
|**Tamanho dos dados**| O tamanho da saída de dados para o arquivo local.|

### <a name="close-job-diagram"></a>Diagrama de trabalho próximo

Se você não precisar mais do diagrama de trabalho, selecione **Fechar** no canto superior direito. Depois de fechar a janela do diagrama, você precisa iniciar os testes locais novamente para vê-la.

### <a name="view-job-level-metrics-and-stop-running"></a>Veja as métricas do nível de trabalho e pare de funcionar

Outras métricas de nível de trabalho aparecem no console pop-up. Pressione **Ctrl+C** no console se quiser parar o trabalho.

![Trabalho de parada de diagrama de trabalho](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Limitações

* Os dissipadores de saída Power BI e Azure Data Lake Storage Gen1 não são suportados devido a limitações do modelo de autenticação.

* Somente opções de entrada de nuvem têm suporte para [políticas de tempo](stream-analytics-out-of-order-and-late-events.md), as opções de entrada locais, não.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: criar um trabalho do Stream Analytics usando o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md)
