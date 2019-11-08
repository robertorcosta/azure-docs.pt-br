---
title: Conecte seu aplicativo do Azure IoT Central com os Serviços de Campo do Dynamics 365 | Microsoft Docs
description: Saiba como criar uma solução de ponta a ponta com o Azure IoT Central e o Serviço de Campo do Dynamics 365
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498766"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Criar uma solução de ponta a ponta com o Azure IoT Central e o Serviço de Campo do Dynamics 365 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Como um construtor, você pode habilitar a integração do seu aplicativo Azure IoT Central a outros sistemas de negócios. 


Por exemplo, em uma solução de gerenciamento de lixo conectada, você pode otimizar o envio de caminhões de coleta de lixo com base em dados de sensores de IoT de lixeiras conectadas. Em seu [aplicativo de gerenciamento de lixo conectado do IoT Central](./tutorial-connected-waste-management.md), você pode configurar regras e ações e defini-las para disparar a criação de alertas no Serviço de Campo do Dynamics. Esse cenário é realizado usando o Microsoft Flow, que você pode configurar diretamente no IoT Central para automatizar fluxos de trabalho entre aplicativos e serviços. Além disso, com base nas atividades de serviço no Serviço de Campo, as informações podem ser enviadas de volta ao Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Como conectar seu aplicativo do Azure IoT Central com os Serviços de Campo do Dynamics 365 

Os processos de integração de ponta a ponta abaixo podem ser facilmente implementados com base em uma experiência de configuração pura:
* O Azure IoT Central pode enviar informações sobre anomalias de dispositivo para o serviço de campo conectado (como um alerta de IoT) para diagnóstico.
* O serviço de campo conectado pode criar casos ou ordens de serviço disparadas de anomalias de dispositivo.
* O serviço de campo conectado pode agendar técnicos para inspeção para evitar incidentes que causem tempo de inatividade.
* O painel do dispositivo do Azure IoT Central pode ser atualizado com informações relevantes sobre serviço e agendamento.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os [modelos de governo do IoT Central](./overview-iot-central-government.md)
* Saiba mais sobre o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Saiba mais sobre os [Serviços de Campo do Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
