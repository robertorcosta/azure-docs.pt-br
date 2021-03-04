---
title: Modelos de ia do Azure Percept
description: Saiba mais sobre os modelos de ia disponíveis para protótipos e implantação
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041579"
---
# <a name="azure-percept-ai-models"></a>Modelos de ia do Azure Percept

O Azure Percept permite que você desenvolva e implante modelos de ia diretamente em seu Percept do Azure DK no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). A implantação de modelo utiliza o [Hub IOT do Azure](https://azure.microsoft.com/services/iot-hub/) e [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Modelos de ia de exemplo

O Azure Percept Studio contém modelos de exemplo para os seguintes aplicativos:

- detecção de pessoas
- detecção de veículo
- detecção geral de objeto
- produtos – detecção na prateleira

Com modelos pré-treinados, nenhuma coleta de dados de codificação ou treinamento é necessária. Basta implantar o modelo desejado no Azure Percept DK do portal e abrir o fluxo de vídeo de seu devkit para ver o modelo inferência em ação. A telemetria do modelo inferência também pode ser acessada por meio da ferramenta do [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="pre-built-solutions"></a>Soluções predefinidas

Uma [solução de análise espacial criada previamente para detecção de pessoas](https://github.com/george-moore/Santa-Cruz-AI-App) também está disponível. A solução predefinida é um aplicativo de ia de software livre que fornece às pessoas baseadas em borda a contagem de eventos de entrada/saída de zona definida pelo usuário. A saída de vídeo e ia do dispositivo de borda local é proveniente de [Azure data Lake](https://azure.microsoft.com/solutions/data-lake/), com a interface do usuário em execução como um site do Azure. O ia inferência é fornecido por um modelo de ia de software livre para detecção de pessoas.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Gif da solução de análise espacial criada previamente.":::

## <a name="custom-no-code-solutions"></a>Soluções personalizadas sem código

Por meio do Azure Percept Studio, você pode desenvolver soluções personalizadas de [visão](./tutorial-nocode-vision.md) e fala, sem necessidade de codificação.

Para soluções de visão personalizada, os modelos de ia de detecção e classificação de objetos estão disponíveis. Basta carregar e marcar suas imagens de treinamento, que podem ser tomadas diretamente com o SoM de visão do Azure Percept do Azure Percept DK, se desejado. O treinamento e a avaliação do modelo são facilmente executados em [visão personalizada](https://www.customvision.ai/), que faz parte dos [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/#overview).

Para soluções de fala personalizadas, os modelos de assistente de voz estão disponíveis atualmente para os seguintes aplicativos:

- Hotelaria: sala de Hotel equipado com dispositivos inteligentes controlados por voz.
- Saúde: recursos de atendimento equipados com dispositivos inteligentes controlados por voz.
- Inventário: Hub de inventário equipado com dispositivos inteligentes controlados por voz.
- Automotivo: Hub automotivado equipado com dispositivos inteligentes controlados por voz.

Os comandos e palavras-chave de assistente de voz pré-criados estão disponíveis diretamente por meio do Portal. Palavras-chave e comandos personalizados podem ser criados e treinados no [Speech Studio](https://speech.microsoft.com/), que também faz parte dos serviços cognitivas do Azure.

## <a name="advanced-development"></a>Desenvolvimento avançado

Para desenvolvedores avançados, o [Notebook Jupyter](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) disponível executa o aprendizado de transferência usando um MobileNetSSDV2Lite (modelo de TensorFlow pré-treinado) no Python com um conjunto de um DataSet personalizado para detecção de objetos. O notebook utiliza instâncias de computação remota por meio de [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview) e pode ser executado na nuvem usando o portal do AzureML ou localmente no [Visual Studio Code](https://code.visualstudio.com/).

Também estão incluídos alguns [scripts](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) Python úteis para o gerenciamento de conjuntos de e o [instalador do pacote de ferramentas de desenvolvimento](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md), que instala e configura todas as ferramentas necessárias para desenvolver uma solução de ia avançada.
