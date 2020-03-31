---
title: Processe fluxos de dados de IoT em tempo real com o Azure Stream Analytics
description: Marcas e dados de sensor de fluxos IoT com o processamento de dados em tempo real e Stream Analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426238"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Processe fluxos de dados de IoT em tempo real com o Azure Stream Analytics

Neste artigo, você aprende a criar lógica de processamento de fluxo para coletar dados de dispositivos de Internet das Coisas (IoT). Você usa um caso de uso de Internet das Coisas (IoT) do mundo real para demonstrar como construir sua solução de forma rápida e econômica.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma assinatura gratuita [do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Baixe a consulta de exemplo e os arquivos de dados do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Cenário

Contoso, que é uma empresa no espaço de automação industrial, automatizou completamente seu processo de fabricação. O maquinário desta fábrica tem sensores capazes de emitir fluxos de dados em tempo real. Nesse cenário, um gerente de chão de fábrica quer ter informações em tempo real dos dados de sensor para procurar por padrões e tomar ações com relação a eles. Você pode usar o SAQL (Stream Analytics Query Language) sobre os dados do sensor para encontrar padrões interessantes a partir do fluxo de dados de entrada.

Neste exemplo, os dados são gerados a partir de um dispositivo de tag de sensor texas instruments. A carga de dados está no formato JSON e é semelhante ao seguinte:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

Em um cenário real, você poderia ter centenas desses sensores gerando eventos como um fluxo. De forma ideal, um dispositivo de gateway executaria um código para enviar por push esses eventos aos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou aos [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/). Seu trabalho do Stream Analytics receberia esses eventos dos Hubs de Eventos e executaria consultas de análise em tempo real nos fluxos. Então, você pode enviar os resultados para uma das [saídas suportadas](stream-analytics-define-outputs.md).

Para facilitar o uso, este guia de Introdução fornece um arquivo de dados de exemplo, capturado de dispositivos de tag de sensor reais. Você pode executar consultas nos dados de exemplo e ver os resultados. Nos tutoriais subsequentes, você aprenderá a conectar seu trabalho a entradas e saídas e implantá-lo para o serviço do Azure.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No [portal Azure,](https://portal.azure.com)selecione **+ Crie um recurso** no menu de navegação à esquerda. Em seguida, selecione **o trabalho do Stream Analytics** no **Analytics**.
   
    ![Criar um novo trabalho do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Insira um nome exclusivo de trabalho e verifique se a assinatura é a correta para seu trabalho. Crie um novo grupo de recursos ou selecione um existente a partir de sua assinatura.

1. Selecione um local para o seu trabalho. Use o mesmo local para seu grupo de recursos e todos os recursos para aumentar a velocidade de processamento e reduzir os custos. Depois de fazer as configurações, selecione **Criar**.
   
    ![Detalhes de Como criar um novo trabalho do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Criar uma instância do Azure Stream Analytics
O próximo passo após a criação do seu trabalho é escrever uma consulta. Você pode testar consultas contra dados de amostra sem conectar uma entrada ou saída ao seu trabalho.

Baixe o [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) do GitHub. Em seguida, navegue até o seu trabalho do Azure Stream Analytics no portal Azure.

Selecione **Consulta em** **Topologia de Trabalho** no menu esquerdo. Em seguida, selecione **Enviar entrada de amostra**. Faça `HelloWorldASA-InputStream.json` upload do arquivo e selecione **Ok**.

![Bloco de consulta de painel do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Observe que uma visualização dos dados é preenchida automaticamente na tabela **de visualização de entrada.**

![Visualização dos dados de entrada da amostra](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Consulta: arquivar seus dados brutos

A forma mais simples de consulta é uma consulta passagem que arquiva todos os dados de entrada em suas saídas indicadas. Essa consulta é a consulta padrão preenchida em um novo trabalho do Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Selecione **a consulta de teste** e visualize os resultados na tabela de resultados do **teste.**

![Resultados do teste para consulta do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: filtrar os dados com base em uma condição

Vamos tentar filtrar os resultados com base em uma condição. Gostaríamos de mostrar resultados apenas dos eventos provenientes de "sensorA."

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Cole a consulta no editor e selecione testar a **consulta** para revisar os resultados.

![Filtrar um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: alerta para disparar o fluxo de trabalho de negócios

Vamos tornar nossa consulta mais detalhada. Para cada tipo de sensor, se você deseja monitorar a temperatura média por 30 segundos e exibir os resultados somente se a temperatura média estiver acima de 100 graus.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Você deve ver resultados que contêm apenas 245 linhas e nomes de sensores onde o tempero médio é superior a 100. Esta consulta agrupa o fluxo de eventos por **dspl**, que é o nome de sensor, sobre uma **Janela em Cascata** de 30 segundos. As consultas temporais devem declarar como você deseja que o tempo progrida. Usando a cláusula **TIMESTAMP BY,** você especificou a coluna **OUTPUTTIME** para associar os tempos com todos os cálculos temporais. Para obter informações [detalhadas,](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)leia sobre as funções [de Gerenciamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) e Janelas .

### <a name="query-detect-absence-of-events"></a>Consulta: detectar ausência de eventos

Como podemos escrever uma consulta para localizar a falta de eventos de entrada? Vamos encontrar a última vez que um sensor enviou dados e, em seguida, não enviou eventos para os próximos 5 segundos.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Detectar ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aqui, usamos uma junção **EXTERNA ESQUERDA** no mesmo fluxo de dados (autojunção). Para uma junção **INTERNA**, um resultado retorna somente quando uma correspondência é encontrada.  Mas para uma junção **EXTERNA ESQUERDA**, se um evento do lado esquerdo da junção for incomparável, uma linha com NULL para todas as colunas do lado direito retornará. Essa técnica é muito útil para localizar a ausência de eventos. Para obter mais informações, consulte [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Conclusão

O objetivo deste artigo é demonstrar como escrever diferentes consultas de linguagem de consulta de stream analytics e ver resultados no navegador. No entanto, isso é apenas para começar. O Stream Analytics dá suporte a várias entradas e saídas e ainda pode usar as funções no Azure Machine Learning para torná-la uma ferramenta robusta de análise de fluxos de dados. Para saber mais sobre como escrever consultas, leia o artigo sobre [padrões comuns de consulta](stream-analytics-stream-analytics-query-patterns.md).

