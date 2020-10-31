---
title: Exibir e configurar o conector do Azure IoT para métricas de FHIR (versão prévia)
description: Este artigo explica a exibição e a configuração do conector do Azure IoT para métricas FHIR (versão prévia)
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133474"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Exibir e configurar o conector do Azure IoT para métricas de FHIR (versão prévia) 

Neste artigo, você aprenderá a exibir e configurar o conector do Azure IoT para métricas FHIR *. 

> [!TIP]
> Siga as orientações em [exportar o conector do Azure IOT para métricas de FHIR (versão prévia) por meio das configurações de diagnóstico](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) para saber como configurar a exportação de dados de métricas.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Exibir métricas para o conector do Azure IoT para FHIR (versão prévia)
1. Para exibir as métricas para conectores IoT, selecione sua API do Azure para o serviço FHIR no portal do Azure. 

2. Navegar até **métricas** 

3. Selecione a guia **conector IOT** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selecione um conector IoT para exibir suas métricas (por exemplo: há (4) conectores IoT associados a essa API do Azure para serviço FHIR).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> A guia **personalizado** permite criar combinações de data/hora específicas para exibir as métricas do conector IOT.

5. Selecione o período de métrica do conector IoT a ser exibido (por exemplo: 1 hora, 24 horas, 7 dias ou personalizado).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Tipos de métricas para o conector do Azure IoT para FHIR (versão prévia) 
As métricas de conector IoT exibidas são as seguintes:

|Tipo de métrica|Finalidade das métricas| 
|-----------|--------------|
|Número de mensagens de entrada|O número de mensagens de entrada brutas recebidas (por exemplo: os eventos de dispositivo).|
|Número de mensagens normalizadas|O número de mensagens normalizadas.|
|Número de grupos de mensagens|O número de grupos que têm mensagens agregadas na janela de tempo designada.|
|Latência média de estágio normalizada|Latência média do estágio normalizar. A fase normalizar é executar a normalização em mensagens de entrada brutas.|
|Latência média de estágio de grupo|Latência média do estágio do grupo. O estágio do grupo é executar o armazenamento em buffer, agregação e agrupamento em mensagens normalizadas.| 
|Contagem Total de Erros|Número total de erros.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Concentrando e Configurando o conector do Azure IoT para métricas de FHIR (versão prévia)
Neste exemplo, vamos nos concentrar no **número de métricas de mensagens de entrada** .

1. Selecione um ponto no tempo no qual você deseja se concentrar.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Nessa tela, você pode **Adicionar métrica** , **Adicionar filtro** e **aplicar divisão** para outras personalizações. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Connector1 IoT" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusão 
Ter acesso às métricas do plano de dados é essencial para o monitoramento e a solução de problemas.  O conector do Azure IoT para FHIR ajuda você a fazer essas ações por meio de métricas. 

## <a name="next-steps"></a>Próximas etapas

Confira as perguntas frequentes sobre o conector do Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector para FHIR FAQs](fhir-faq.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia).

FHIR é uma marca registrada da HL7, usada com permissão da HL7.