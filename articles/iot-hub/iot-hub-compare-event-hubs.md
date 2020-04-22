---
title: Comparar o Hub IoT do Azure ao Hubs de Eventos do Azure | Microsoft Docs
description: Uma comparação dos serviços do Hub IoT e de Eventos do Azure destacando diferenças funcionais e casos de uso. A comparação inclui protocolos com suporte, gerenciamento de dispositivos, monitoramento e uploads de arquivos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7aa59f8181ad60b3d43846a3f4f1f471a050b238
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733433"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Conectar Dispositivos IoT ao Azure: Hub IoT e Hubs de Eventos

O Azure fornece serviços desenvolvidos especificamente para diferentes tipos de conectividade e comunicação para ajudá-lo a conectar seus dados ao poder da nuvem. Tanto o Hub IoT como os Hubs de Eventos do Azure são serviços de nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para informações de negócios. Os dois serviços são semelhantes, pois ambos dão suporte à ingestão de dados com baixa latência e alta confiabilidade, mas eles são projetados para finalidades diferentes. O IoT Hub foi desenvolvido para atender aos requisitos exclusivos de conexão de dispositivos IoT à nuvem Dozure, enquanto o Event Hubs foi projetado para streaming de big data. Microsoft recomenda usar o Azure IoT Hub para conectar dispositivos IoT ao Azure

O Azure IoT Hub é o gateway de nuvem que conecta dispositivos IoT para coletar dados e impulsionar insights de negócios e automação. Além disso, o Hub IoT inclui recursos que enriquecem a relação entre os dispositivos e seus sistemas de back-end. Os recursos de comunicação bidirecional significam que, enquanto você recebe dados de dispositivos, você também pode enviar comandos e políticas de volta aos dispositivos. Por exemplo, use mensagens nuvem-dispositivo para atualizar propriedades ou invocar ações de gerenciamento de dispositivos. A comunicação nuvem-para-dispositivo também permite que você envie inteligência em nuvem para seus dispositivos de borda com o Azure IoT Edge. A identidade exclusiva no nível de dispositivo fornecida pelo Hub IoT ajuda a proteger melhor a sua solução de IoT contra ataques potenciais. 

[Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) é o serviço de streaming de Big Data do Azure. Ele foi projetado para cenários de streaming de dados de alta taxa de transferência em que os clientes podem enviar bilhões de solicitações por dia. Os Hubs de Eventos usam um modelo de consumidor particionado para expandir sua transmissão e estão integrados aos serviços de big data e análise do Azure, incluindo Databricks, Stream Analytics, ADLS e HDInsight. Com recursos como a Captura e Aumento Automático de Hubs de Eventos, este serviço é criado para dar suporte a aplicativos e soluções de big data. Além disso, o IoT Hub usa hubs de eventos para seu caminho de fluxo de telemetria, de modo que sua solução de IoT também se beneficia do tremendo poder dos Event Hubs.

Resumindo, ambas as soluções são projetadas para a ingestão de dados em grande escala. Apenas o IoT Hub fornece os recursos específicos de IoT ricos que são projetados para você maximizar o valor de negócios de conectar seus dispositivos IoT à nuvem Azure.  Se sua jornada IoT está apenas começando, começar com o Hub IoT para dar suporte a cenários de ingestão de dados garantirá que você tenha acesso instantâneo aos recursos de IoT completos quando suas necessidades comerciais e técnicas o exigir.

A tabela a seguir fornece detalhes sobre como as duas camadas do Hub IoT comparam-se aos Hubs de Eventos quando avaliá-las para os recursos de IoT. Para obter mais informações sobre as camadas Standard e Básica do Hub IoT, consulte [Como escolher a camada certa do Hub IoT](iot-hub-scaling.md).

| Funcionalidade do IoT | Camada Standard do Hub IoT | Camada Básica do Hub IoT | Hubs de Eventos |
| --- | --- | --- | --- |
| Mensagens do dispositivo para a nuvem | ![Verificação][checkmark] | ![Verificação][checkmark] | ![Verificação][checkmark] |
| Protocolos: HTTPS, AMQP, AMQP sobre webSockets | ![Verificação][checkmark] | ![Verificação][checkmark] | ![Verificação][checkmark] |
| Protocolos: MQTT, MQTT sobre webSockets | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Identidade por dispositivo | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Upload de arquivo de dispositivos | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Serviço de provisionamento de dispositivos | ![Verificação][checkmark] | ![Verificação][checkmark] |  |
| Mensagens da nuvem para o dispositivo | ![Verificação][checkmark] |  |  |
| Gerenciamento de dispositivo, dispositivo gêmeo | ![Verificação][checkmark] |  |  |
| Fluxos de dispositivo (versão prévia) | ![Verificação][checkmark] |  |  |
| IoT Edge | ![Verificação][checkmark] |  |  |

Mesmo se o único caso de uso for ingestão de dados de dispositivo para nuvem, é altamente recomendável usar o Hub IoT, pois ele fornece um serviço projetado para conectividade do dispositivo do IoT. 

### <a name="next-steps"></a>Próximas etapas

Para explorar ainda mais as funcionalidade do Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
