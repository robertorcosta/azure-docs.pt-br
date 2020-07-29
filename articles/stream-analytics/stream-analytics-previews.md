---
title: Recursos de visualização do Azure Stream Analytics
description: Este artigo lista os recursos do Azure Stream Analytics que estão atualmente na visualização.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7391fbccaf7983a070d80da64a2908333280420b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83608994"
---
# <a name="azure-stream-analytics-preview-features"></a>Recursos de visualização do Azure Stream Analytics

Este artigo resume todos os recursos atualmente em visualização do Azure Stream Analytics. O uso de recursos de visualização em um ambiente de produção não é recomendado.

## <a name="public-previews"></a>Visualizações públicas

Os seguintes recursos estão em pré-visualização pública. Você pode aproveitar esses recursos hoje, mas não os use em seu ambiente de produção.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Autenticar na saída do Banco de Dados SQL com identidades gerenciadas

O Azure Stream Analytics dá suporte à [Autenticação de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para os coletores de saída do Banco de Dados SQL do Azure. Identidades gerenciadas eliminam as limitações dos métodos de autenticação baseados em usuário, como a necessidade de autenticar novamente devido a alterações de senha ou expirações de token de usuário que ocorrem a cada 90 dias. Quando você remove a necessidade de autenticar manualmente, suas implantações do Stream Analytics podem ser totalmente automatizadas.

### <a name="output-to-azure-synapse-analytics"></a>Saída para Azure Synapse Analytics

Os trabalhos do Azure Stream Analytics podem gerar uma saída para uma tabela do pool de SQL no [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) e podem processar taxas de transferência de até 200 MB/s, o que dá suporte às mais exigentes análises em tempo real e necessidades de processamento de dados de caminho crítico para cargas de trabalho, como relatórios e painéis.  


### <a name="online-scaling"></a>Dimensionamento online

Com o dimensionamento online, não é necessário interromper seu trabalho caso precise alterar a alocação de SU. Você pode aumentar ou diminuir a capacidade de SU de um trabalho em execução sem precisar interrompê-lo. Isso se baseia na promessa do cliente de pipelines críticos de longa execução que o Stream Analytics oferece hoje. Para obter mais informações, confira [Configurar unidades de streaming do Azure Stream Analytics](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Desserializadores personalizados de C#
Os desenvolvedores podem aproveitar o poder do Azure Stream Analytics para processar dados em Protobuf, XML ou qualquer formato personalizado. Você pode implementar [desserializadores personalizados](custom-deserializer-examples.md) em C# e usá-los para desserializar eventos recebidos pelo Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibilidade com código personalizado de C#

Os desenvolvedores que criam módulos do Stream Analytics na nuvem ou no IoT Edge podem gravar ou reutilizar funções C# personalizadas e chamá-las diretamente na consulta por meio de [funções definidas pelo usuário](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Depurar etapas de consulta no Visual Studio

Você pode visualizar facilmente o conjunto de linhas intermediárias em um diagrama de dados ao fazer testes locais nas ferramentas do Azure Stream Analytics para Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Teste local com dados dinâmicos no Visual Studio Code

Você pode testar suas consultas com dados dinâmicos em seu computador local antes de enviar o trabalho para o Azure. Cada iteração de teste leva menos de dois a três segundos em média, resultando em um processo de desenvolvimento muito eficiente.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser criados no Visual Studio Code. Confira nosso [tutorial de introdução ao VS Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos de ML personalizados gerenciados pelo Azure Machine Learning

O Azure Stream Analytics dá suporte para pontuação em tempo real de alto desempenho, aproveitando modelos personalizados de aprendizado de máquina pré-treinados gerenciados pelo Azure Machine Learning e hospedados no AKS (Serviço de Kubernetes do Azure) ou na ACI (Instâncias de Contêiner do Azure), usando um fluxo de trabalho que não exige que você escreva código. [Inscreva-se](https://aka.ms/asapreview1) para obter a versão prévia


### <a name="live-data-testing-in-visual-studio"></a>Em tempo real de dados de teste no Visual Studio

As ferramentas do Visual Studio para o Azure Stream Analytics aprimoram o recurso de teste local que permite testar suas consultas em fluxos de eventos ao vivo de fontes de nuvem, como Hub de Eventos ou Hub de IoT. Saiba como [Testar dados ao vivo localmente usando as ferramentas do Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo usuário .NET no IoT Edge

Com as funções definidas pelo usuário padrão do .NET, você pode executar o código do .NET Standard como parte de seu fluxo de pipeline. Você pode criar classes C# simples ou importar projetos e bibliotecas completos. Experiência completa de autoria e depuração é suportada no Visual Studio. Para obter mais informações, visite [Desenvolver funções definidas pelo usuário do .NET Standard para tarefas do Edge Analytics Broadcast do Azure](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Outras versões prévias

Os recursos a seguir também estão disponíveis em versão prévia mediante solicitação.

### <a name="support-for-azure-stack"></a>Suporte para Azure Stack
Esse recurso habilitado no runtime do Azure IoT Edge, aproveita os recursos personalizados do Azure Stack, como suporte nativo para entradas e saídas locais em execução no Azure Stack (por exemplo, hubs de eventos, Hub IoT, Armazenamento de Blobs). Essa nova integração permite que você crie arquiteturas híbridas que podem analisar seus dados perto de onde são gerados, reduzindo a latência e maximizando os insights.
Esse recurso permite que você crie arquiteturas híbridas que podem analisar seus dados perto de onde são gerados, reduzindo a latência e maximizando os insights. [Inscreva-se](https://aka.ms/asapreview1) para obter esta versão prévia.
