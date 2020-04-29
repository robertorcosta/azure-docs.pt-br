---
title: Integrar Azure Stream Analytics com Azure Machine Learning
description: Este artigo descreve como integrar um trabalho de Azure Stream Analytics com modelos de Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481981"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrar Azure Stream Analytics com Azure Machine Learning (versão prévia)

Você pode implementar modelos de aprendizado de máquina como uma UDF (função definida pelo usuário) em seus trabalhos de Azure Stream Analytics para fazer previsões e estimativas em tempo real sobre seus dados de entrada de streaming. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) permite que você use qualquer ferramenta de software livre popular, como Tensorflow, scikit-Learn ou PyTorch, para preparar, treinar e implantar modelos.

> [!NOTE]
> Essa funcionalidade está em visualização pública. Você pode acessar esse recurso somente no portal do Azure usando o link de [visualização do portal Stream Analytics](https://aka.ms/asaportalpreview). Essa funcionalidade também está disponível na versão mais recente do [Stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas a seguir antes de adicionar um modelo de aprendizado de máquina como uma função ao seu trabalho de Stream Analytics:

1. Use Azure Machine Learning para [implantar seu modelo como um serviço Web](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Seu script de Pontuação deve ter [entradas de exemplo e saídas](../machine-learning/how-to-deploy-and-where.md#example-entry-script) que são usadas pelo Azure Machine Learning para gerar uma especificação de esquema. Stream Analytics usa o esquema para entender a assinatura de função do serviço Web.

3. Verifique se o serviço Web aceita e retorna dados serializados JSON.

4. Implante seu modelo no [serviço kubernetes do Azure](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) para implantações de produção de grande escala. Se o serviço Web não puder lidar com o número de solicitações provenientes de seu trabalho, o desempenho do seu trabalho de Stream Analytics será degradado, o que afeta a latência. Modelos implantados em instâncias de contêiner do Azure não são suportados hoje, mas estarão disponíveis nos próximos meses.

## <a name="add-a-machine-learning-model-to-your-job"></a>Adicionar um modelo de aprendizado de máquina ao seu trabalho

Você pode adicionar Azure Machine Learning funções ao seu trabalho de Stream Analytics diretamente do portal do Azure.

1. Navegue até o trabalho do Stream Analytics na portal do Azure e selecione **funções** em **topologia do trabalho**. Em seguida, selecione **serviço do Azure ml** no menu suspenso **+ Adicionar** .

   ![Adicionar UDF do Azure ML](./media/machine-learning-udf/add-azureml-udf.png)

2. Preencha o formulário **função de serviço Azure Machine Learning** com os seguintes valores de propriedade:

   ![Configurar UDF do Azure ML](./media/machine-learning-udf/configure-azureml-udf.png)

A tabela a seguir descreve cada propriedade das funções de serviço do Azure ML no Stream Analytics.

|Propriedade|Descrição|
|--------|-----------|
|Alias da função|Insira um nome para invocar a função em sua consulta.|
|Assinatura|Sua assinatura do Azure..|
|Espaço de trabalho do Azure ML|O espaço de trabalho Azure Machine Learning usado para implantar seu modelo como um serviço Web.|
|Implantações|O serviço Web que hospeda seu modelo.|
|Assinatura de função|A assinatura do serviço Web inferida da especificação de esquema da API. Se a sua assinatura não for carregada, verifique se você forneceu entrada e saída de exemplo em seu script de Pontuação para gerar automaticamente o esquema.|
|Número de solicitações paralelas por partição|Essa é uma configuração avançada para otimizar a taxa de transferência de alta escala. Esse número representa as solicitações simultâneas enviadas de cada partição do seu trabalho para o serviço Web. Trabalhos com seis unidades de streaming (SU) e inferior têm uma partição. Trabalhos com 12 SUs têm duas partições, 18 o SUs tem três partições e assim por diante.<br><br> Por exemplo, se seu trabalho tiver duas partições e você definir esse parâmetro como quatro, haverá oito solicitações simultâneas de seu trabalho para o serviço Web. Neste momento da visualização pública, esse valor usa como padrão 20 e não pode ser atualizado.|
|Contagem máxima do lote|Esta é uma configuração avançada para otimizar a taxa de transferência de alta escala. Esse número representa o número máximo de eventos que serão agrupados em lote em uma única solicitação enviada ao serviço Web.|

## <a name="supported-input-parameters"></a>Parâmetros de entrada com suporte

Quando a consulta de Stream Analytics invoca um UDF Azure Machine Learning, o trabalho cria uma solicitação JSON serializada para o serviço Web. A solicitação é baseada em um esquema específico do modelo. Você precisa fornecer um exemplo de entrada e saída em seu script de Pontuação para [gerar automaticamente um esquema](../machine-learning/how-to-deploy-and-where.md). O esquema permite que Stream Analytics Construa a solicitação serializada JSON para qualquer um dos tipos de dados com suporte, como numpy, pandas e PySpark. Vários eventos de entrada podem ser agrupados em lote em uma única solicitação.

A consulta Stream Analytics a seguir é um exemplo de como invocar um UDF Azure Machine Learning:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics só dá suporte à passagem de um parâmetro para funções Azure Machine Learning. Talvez seja necessário preparar seus dados antes de passá-los como uma entrada para o UDF do Machine Learning.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passar vários parâmetros de entrada para o UDF

Os exemplos mais comuns de entradas para modelos de aprendizado de máquina são matrizes e quadros de numpy. Você pode criar uma matriz usando um UDF JavaScript e criar um dataframe serializado em JSON usando a `WITH` cláusula.

### <a name="create-an-input-array"></a>Criar uma matriz de entrada

Você pode criar um UDF JavaScript que aceita *N* número de entradas e cria uma matriz que pode ser usada como entrada para o Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Depois de adicionar o UDF do JavaScript ao seu trabalho, você pode invocar seu Azure Machine Learning UDF usando a seguinte consulta:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

O JSON a seguir é uma solicitação de exemplo:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Criar um dataframe do pandas ou PySpark

Você pode usar a `WITH` cláusula para criar um dataframe serializado JSON que pode ser passado como entrada para o Azure Machine Learning UDF, conforme mostrado abaixo.

A consulta a seguir cria um dataframe selecionando os campos necessários e usa o dataframe como entrada para o Azure Machine Learning UDF.

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

O JSON a seguir é uma solicitação de exemplo da consulta anterior:

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Otimizar o desempenho de Azure Machine Learning UDFs

Ao implantar seu modelo no serviço kubernetes do Azure, você pode [criar o perfil de seu modelo para determinar a utilização de recursos](../machine-learning/how-to-deploy-and-where.md#profilemodel). Você também pode [habilitar o Application insights para suas implantações](../machine-learning/how-to-enable-app-insights.md) para entender as taxas de solicitação, os tempos de resposta e as tarifas de falha.

Se você tiver um cenário com alta taxa de transferência de eventos, talvez seja necessário alterar os seguintes parâmetros no Stream Analytics para obter o desempenho ideal com latências de ponta a ponta baixas:

1. Contagem máxima de lotes.
2. Número de solicitações paralelas por partição.

### <a name="determine-the-right-batch-size"></a>Determinar o tamanho do lote certo

Depois de implantar seu serviço Web, envie uma solicitação de exemplo com tamanhos de lote variados a partir de 50 e aumentando-o na ordem de centenas. Por exemplo, 200, 500, 1000, 2000 e assim por diante. Você observará que, após um determinado tamanho de lote, a latência da resposta aumenta. O ponto após o qual a latência de resposta aumenta deve ser a contagem máxima de lotes para seu trabalho.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinar o número de solicitações paralelas por partição

No dimensionamento ideal, seu trabalho de Stream Analytics deve ser capaz de enviar várias solicitações paralelas para o serviço Web e obter uma resposta em poucos milissegundos. A latência da resposta do serviço Web pode afetar diretamente a latência e o desempenho de seu trabalho de Stream Analytics. Se a chamada do seu trabalho para o serviço Web demorar muito tempo, você provavelmente verá um aumento no atraso da marca d' água e também poderá ver um aumento no número de eventos de entrada de registro posterior.

Para evitar essa latência, verifique se o cluster AKS (serviço kubernetes do Azure) foi provisionado com o [número correto de nós e réplicas](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). É fundamental que seu serviço Web esteja altamente disponível e retorne respostas bem-sucedidas. Se seu trabalho receber uma resposta de serviço indisponível (503) de seu serviço Web, ele será repetido continuamente com a retirada exponencial. Qualquer resposta diferente de Success (200) e serviço indisponível (503) fará com que seu trabalho vá para um estado de falha.

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Funções definidas pelo usuário do JavaScript do Stream Analytics do Azure](stream-analytics-javascript-user-defined-functions.md)
* [Dimensionar seu trabalho de Stream Analytics com a função Azure Machine Learning Studio (clássica)](stream-analytics-scale-with-machine-learning-functions.md)

