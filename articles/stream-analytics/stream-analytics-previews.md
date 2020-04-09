---
title: Recursos de visualização do Azure Stream Analytics
description: Este artigo lista os recursos do Azure Stream Analytics que estão atualmente na visualização.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878299"
---
# <a name="azure-stream-analytics-preview-features"></a>Recursos de visualização do Azure Stream Analytics

Este artigo resume todos os recursos atualmente em visualização do Azure Stream Analytics. O uso de recursos de visualização em um ambiente de produção não é recomendado.

## <a name="public-previews"></a>Visualizações públicas

Os seguintes recursos estão em pré-visualização pública. Você pode aproveitar esses recursos hoje, mas não os use em seu ambiente de produção.

### <a name="online-scaling"></a>Dimensionamento on-line

Com o dimensionamento on-line, você não é obrigado a parar seu trabalho se precisar alterar a alocação de SU. Você pode aumentar ou diminuir a capacidade de SU de um trabalho em execução sem ter que pará-lo. Isso se baseia na promessa do cliente de pipelines de missão crítica de longa duração que o Stream Analytics oferece hoje. Para obter mais informações, consulte [Configure Azure Stream Analytics Streaming Units](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C# desserializadores personalizados
Os desenvolvedores podem aproveitar o poder do Azure Stream Analytics para processar dados em Protobuf, XML ou qualquer formato personalizado. Você pode implementar [desserializadores personalizados](custom-deserializer-examples.md) em C#, que podem ser usados para desserializar eventos recebidos pelo Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibilidade com código personalizado C#

Os desenvolvedores que criam módulos de Stream Analytics na nuvem ou no IoT Edge podem escrever ou reutilizar funções C# personalizadas e invocá-las diretamente na consulta através [de funções definidas pelo usuário.](stream-analytics-edge-csharp-udf-methods.md)


### <a name="debug-query-steps-in-visual-studio"></a>Etapas de consulta de depuração no Visual Studio

Você pode visualizar facilmente a linha intermediária definida em um diagrama de dados ao fazer testes locais nas ferramentas do Azure Stream Analytics para o Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testes locais com dados ao vivo no Visual Studio Code

Você pode testar suas consultas contra dados ao vivo em sua máquina local antes de enviar o trabalho para o Azure. Cada iteração de teste leva em média de dois a três segundos, resultando em um processo de desenvolvimento muito eficiente.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser criados no Visual Studio Code. Veja nosso [tutorial de início do VS Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos ML personalizados gerenciados pelo Azure Machine Learning

O Azure Stream Analytics suporta pontuação de alto desempenho e em tempo real, aproveitando modelos personalizados de Machine Learning pré-treinados gerenciados pelo Azure Machine Learning e hospedados no Azure Kubernetes Service (AKS) ou no Azure Container Instances (ACI), usando um fluxo de trabalho que não exige que você escreva código. [Inscreva-se](https://aka.ms/asapreview1) para pré-visualização


### <a name="live-data-testing-in-visual-studio"></a>Em tempo real de dados de teste no Visual Studio

As ferramentas do Visual Studio para o Azure Stream Analytics aprimoram o recurso de teste local que permite testar suas consultas em fluxos de eventos ao vivo de fontes de nuvem, como Hub de Eventos ou Hub de IoT. Saiba como [Testar dados ao vivo localmente usando as ferramentas do Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo usuário .NET no IoT Edge

Com as funções definidas pelo usuário padrão do .NET, você pode executar o código do .NET Standard como parte de seu fluxo de pipeline. Você pode criar classes C# simples ou importar projetos e bibliotecas completos. Experiência completa de autoria e depuração é suportada no Visual Studio. Para obter mais informações, visite [Desenvolver funções definidas pelo usuário do .NET Standard para tarefas do Edge Analytics Broadcast do Azure](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Outras pré-visualizações

Os recursos a seguir também estão disponíveis na pré-visualização por solicitação.

### <a name="support-for-azure-stack"></a>Suporte para Azure Stack
Esse recurso habilitado no tempo de execução do Azure IoT Edge aproveita os recursos personalizados do Azure Stack, como suporte nativo para entradas e saídas locais em execução no Azure Stack (por exemplo, Hubs de Eventos, Hub IoT, Blob Storage). Essa nova integração permite que você construa arquiteturas híbridas que possam analisar seus dados perto de onde são gerados, diminuindo a latência e maximizando insights.
Esse recurso permite que você construa arquiteturas híbridas que possam analisar seus dados perto de onde são gerados, diminuindo a latência e maximizando insights. Você deve [se inscrever](https://aka.ms/asapreview1) para esta pré-visualização.
