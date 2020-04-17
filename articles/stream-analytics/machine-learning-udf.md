---
title: Integre o Azure Stream Analytics com o Azure Machine Learning
description: Este artigo descreve como integrar um trabalho do Azure Stream Analytics com os modelos de Machine Learning do Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481981"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integre o Azure Stream Analytics com o Azure Machine Learning (Preview)

Você pode implementar modelos de aprendizado de máquina como uma função definida pelo usuário (UDF) em seus trabalhos do Azure Stream Analytics para fazer pontuação e previsões em tempo real sobre seus dados de entrada de streaming. [O Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) permite que você use qualquer ferramenta de código aberto popular, como Tensorflow, scikit-learn ou PyTorch, para preparar, treinar e implantar modelos.

> [!NOTE]
> Esta funcionalidade está em visualização pública. Você pode acessar esse recurso no portal Azure apenas usando o link de visualização do [portal Stream Analytics](https://aka.ms/asaportalpreview). Essa funcionalidade também está disponível na versão mais recente das [ferramentas Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

## <a name="prerequisites"></a>Pré-requisitos

Complete as seguintes etapas antes de adicionar um modelo de aprendizado de máquina como função ao seu trabalho de Stream Analytics:

1. Use o Azure Machine Learning para [implantar seu modelo como um serviço web](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Seu script de pontuação deve ter [entradas e saídas de exemplo](../machine-learning/how-to-deploy-and-where.md#example-entry-script) que é usada pelo Azure Machine Learning para gerar uma especificação de esquema. O Stream Analytics usa o esquema para entender a assinatura de função do seu serviço web.

3. Certifique-se de que seu serviço web aceita e retorna dados serializados JSON.

4. Implante seu modelo no [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) para implantações de produção em alta escala. Se o serviço web não for capaz de lidar com o número de solicitações provenientes do seu trabalho, o desempenho do seu trabalho de Stream Analytics será degradado, o que impacta a latência. Os modelos implantados no Azure Container Instances não são suportados hoje, mas serão disponibilizados nos próximos meses.

## <a name="add-a-machine-learning-model-to-your-job"></a>Adicione um modelo de aprendizado de máquina ao seu trabalho

Você pode adicionar funções de Aprendizado de Máquina do Azure ao seu trabalho de Stream Analytics diretamente do portal Azure.

1. Navegue até o trabalho de Stream Analytics no portal Azure e selecione **Funções** em **Topologia de Trabalho**. Em seguida, selecione **Azure ML Service** no menu **+ Adicionar** suspenso.

   ![Adicionar Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. Preencha o **formulário de função Azure Machine Learning Service** com os seguintes valores de propriedade:

   ![Configurar O Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

A tabela a seguir descreve cada propriedade das funções do Azure ML Service no Stream Analytics.

|Propriedade|Descrição|
|--------|-----------|
|Alias da função|Digite um nome para invocar a função em sua consulta.|
|Subscription|Sua assinatura do Azure..|
|Espaço de trabalho Azure ML|O espaço de trabalho azure Machine Learning que você usou para implantar seu modelo como um serviço web.|
|Implantações|O serviço web hospedando seu modelo.|
|Assinatura de função|A assinatura do seu serviço web inferida a partir da especificação do esquema da API. Se sua assinatura não for carregada, verifique se você forneceu entrada e saída de amostra em seu script de pontuação para gerar automaticamente o esquema.|
|Número de solicitações paralelas por partição|Esta é uma configuração avançada para otimizar o throughput de alta escala. Esse número representa as solicitações simultâneas enviadas de cada partição do seu trabalho para o serviço web. Empregos com seis unidades de streaming (SU) e inferiores têm uma partição. Empregos com 12 SUs têm duas partições, 18 SUs têm três partições e assim por diante.<br><br> Por exemplo, se o seu trabalho tiver duas partições e você definir este parâmetro para quatro, haverá oito solicitações simultâneas do seu trabalho para o seu serviço web. Neste momento de visualização pública, esse valor é padrão para 20 e não pode ser atualizado.|
|Contagem máxima do lote|Esta é uma configuração avançada para otimizar o throughput de alta escala. Esse número representa o número máximo de eventos que serão emparcelados em uma única solicitação enviada ao seu serviço web.|

## <a name="supported-input-parameters"></a>Parâmetros de entrada suportados

Quando a consulta do Stream Analytics invoca um UDF de aprendizado de máquina do Azure, o trabalho cria uma solicitação serializada JSON para o serviço web. A solicitação é baseada em um esquema específico do modelo. Você tem que fornecer uma entrada e saída de amostra em seu script de pontuação para [gerar automaticamente um esquema](../machine-learning/how-to-deploy-and-where.md). O esquema permite que o Stream Analytics construa a solicitação serializada JSON para qualquer um dos tipos de dados suportados, como numpy, pandas e PySpark. Vários eventos de entrada podem ser parcelados em uma única solicitação.

A seguinte consulta do Stream Analytics é um exemplo de como invocar um UDF de aprendizado de máquina do Azure:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

O Stream Analytics suporta apenas passar um parâmetro para funções de Aprendizado de Máquina do Azure. Você pode precisar preparar seus dados antes de passá-los como uma entrada para o UDF de aprendizado de máquina.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passe vários parâmetros de entrada para o UDF

Os exemplos mais comuns de entradas para modelos de aprendizado de máquina são matrizes numpy e DataFrames. Você pode criar uma matriz usando um JavaScript UDF e criar um `WITH` DataFrame serializado em JSON usando a cláusula.

### <a name="create-an-input-array"></a>Criar uma matriz de entrada

Você pode criar um JavaScript UDF que aceita *n* número de entradas e cria uma matriz que pode ser usada como entrada para o seu Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Depois de adicionar o JavaScript UDF ao seu trabalho, você pode invocar seu UDF de aprendizado de máquina do Azure usando a seguinte consulta:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

O JSON a seguir é um exemplo de solicitação:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Crie um Pandas ou PySpark DataFrame

Você pode `WITH` usar a cláusula para criar um DataFrame serializado JSON que pode ser passado como entrada para o Seu Azure Machine Learning UDF, conforme mostrado abaixo.

A consulta a seguir cria um DataFrame selecionando os campos necessários e usa o DataFrame como entrada para o UDF de aprendizado de máquina do Azure.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

O JSON a seguir é um exemplo de solicitação da consulta anterior:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Otimizar o desempenho para UDFs de aprendizado de máquina do Azure

Ao implantar seu modelo no Azure Kubernetes Service, você pode [traçar o perfil do seu modelo para determinar a utilização de recursos](../machine-learning/how-to-deploy-and-where.md#profilemodel). Você também pode [habilitar o App Insights para suas implantações](../machine-learning/how-to-enable-app-insights.md) para entender as taxas de solicitação, os tempos de resposta e as taxas de falha.

Se você tem um cenário com alto rendimento de evento, talvez seja necessário alterar os seguintes parâmetros no Stream Analytics para obter o desempenho ideal com latências de ponta a ponta:

1. Contagem máxima de lotes.
2. Número de solicitações paralelas por partição.

### <a name="determine-the-right-batch-size"></a>Determine o tamanho do lote certo

Depois de implantar seu serviço web, você envia solicitação de amostra com tamanhos de lote variados a partir de 50 e aumentando-o em ordem de centenas. Por exemplo, 200, 500, 1000, 2000 e assim por diante. Você notará que depois de um certo tamanho de lote, a latência da resposta aumenta. O ponto após o qual a latência de aumentos de resposta deve ser a contagem máxima de lotes para o seu trabalho.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determine o número de solicitações paralelas por partição

No dimensionamento ideal, seu trabalho de Stream Analytics deve ser capaz de enviar várias solicitações paralelas ao seu serviço web e obter uma resposta em poucos milissegundos. A latência da resposta do serviço web pode afetar diretamente a latência e o desempenho do seu trabalho no Stream Analytics. Se a chamada do seu trabalho para o serviço web demorar muito tempo, você provavelmente verá um aumento no atraso da marca d'água e também poderá ver um aumento no número de eventos de entrada com atraso.

Para evitar tal latência, certifique-se de que o cluster Azure Kubernetes Service (AKS) tenha sido provisionado com o [número certo de nódulos e réplicas](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). É fundamental que seu serviço web esteja altamente disponível e retorne respostas bem sucedidas. Se o seu trabalho receber uma resposta indisponível de serviço (503) do seu serviço web, ele tentará continuamente com o recuo exponencial. Qualquer resposta que não seja sucesso (200) e serviço indisponível (503) fará com que seu trabalho vá para um estado falido.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure](stream-analytics-javascript-user-defined-functions.md)
* [Dimensione seu trabalho de Stream Analytics com a função Azure Machine Learning Studio (clássico)](stream-analytics-scale-with-machine-learning-functions.md)

