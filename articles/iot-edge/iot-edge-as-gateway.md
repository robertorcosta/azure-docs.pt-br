---
title: Gateways para dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: Use o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou de proxy que envia dados de vários dispositivos downstream para a nuvem ou os processa localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017015"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Como um dispositivo IoT Edge pode ser usado como um gateway

Os gateways em soluções IoT Edge fornecem conectividade de dispositivo e análise de borda para dispositivos IoT que, caso contrário, não teriam esses recursos. Azure IoT Edge pode ser usado para atender a qualquer necessidade de um gateway de IoT, seja relacionado à conectividade, identidade ou análise de borda. Padrões de gateway, neste artigo, referem-se apenas às características de conectividade de dispositivo downstream e identidade de dispositivo, não como dados de dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões

Há três padrões para usar um dispositivo IoT Edge como um gateway: transparente, conversão de protocolo e conversão de identidade.

Uma diferença importante entre os padrões é que um gateway transparente passa mensagens entre dispositivos downstream e o Hub IoT sem precisar de nenhum processamento adicional. A conversão de protocolo e a conversão de identidades, no entanto, exigem processamento no gateway para habilitar a comunicação.

Qualquer gateway pode usar módulos IoT Edge para executar a análise ou pré-processamento na borda antes de passar mensagens de dispositivos downstream para o Hub IoT.

![Diagrama - transparente, protocolo e padrões de gateway de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Padrão transparente

Em um padrão de gateway *transparente* , os dispositivos que teoricamente podiam se conectar ao Hub IOT podem se conectar a um dispositivo de gateway em vez disso. Os dispositivos downstream têm as próprias identidades de Hub IoT e usam qualquer um dos protocolos MQTT, AMQP ou HTTP. O gateway simplesmente passa as comunicações entre os dispositivos e o Hub IoT. Os dispositivos e os usuários que interagem com eles por meio do Hub IoT não sabem que um gateway está mediando suas comunicações. Essa falta de conscientização significa que o gateway é considerado *transparente*.

O tempo de execução do IoT Edge inclui recursos de gateway transparentes. Para obter mais informações, consulte [Configurar um dispositivo de IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).

### <a name="protocol-translation-pattern"></a>Padrão de conversão de protocolo

Um gateway de *conversão de protocolo* também é conhecido como um gateway *opaco* , em contraste com o padrão de gateway transparente. Nesse padrão, os dispositivos que não dão suporte a MQTT, AMQP ou HTTP podem usar um dispositivo de gateway para enviar dados ao Hub IoT em seu nome. O gateway compreende o protocolo usado pelos dispositivos downstream e é o único dispositivo que tem uma identidade no Hub IoT. Todas as informações parecem estar vindo de um dispositivo, o gateway. Dispositivos downstream deverão inserir informações de identificação adicionais em suas mensagens se os aplicativos de nuvem quiserem analisar os dados por dispositivo. Além disso, os primitivos do Hub IoT como gêmeos e métodos só estão disponíveis para o dispositivo de gateway, não para dispositivos downstream.

O tempo de execução de IoT Edge não inclui recursos de conversão de protocolo. Esse padrão requer módulos personalizados ou de terceiros que geralmente são específicos para o hardware e o protocolo usados. O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contém vários módulos de conversão de protocolo para escolher.

### <a name="identity-translation-pattern"></a>Padrão de conversão de identidade

Em um padrão de gateway de *conversão de identidades* , os dispositivos que não podem se conectar ao Hub IOT podem se conectar a um dispositivo de gateway, em vez disso. O gateway fornece a conversão de protocolo e a identidade do Hub IoT em nome dos dispositivos downstream. O gateway é inteligente o suficiente para entender o protocolo usado pelos dispositivos downstream, fornecer identidade e converter primitivos do Hub IoT. Dispositivos downstream aparecem no Hub IoT como dispositivos de primeira classe com gêmeos e métodos. Um usuário pode interagir com os dispositivos no Hub IoT e não estar ciente do dispositivo de gateway intermediário.

O tempo de execução de IoT Edge não inclui recursos de conversão de identidade. Esse padrão requer módulos personalizados ou de terceiros que geralmente são específicos para o hardware e o protocolo usados. Para obter um exemplo que usa o padrão de conversão de identidade, consulte [Azure IOT Edge Kit do iniciante do LoRaWAN](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="use-cases"></a>Casos de uso

Todos padrões de gateway fornecem os seguintes benefícios:

* **Análise na borda** – use os serviços de ia localmente para processar dados provenientes de dispositivos downstream sem enviar telemetria de fidelidade total à nuvem. Encontre e reaja a insights localmente e só envie um subconjunto de dados para o Hub IoT.
* **Isolamento de dispositivo downstream** – o dispositivo de gateway pode proteger todos os dispositivos downstream da exposição à internet. Ele pode ficar entre uma rede OT que não tenha conectividade e uma rede de TI que forneça acesso à web.
* **Multiplexação de Conexão** – todos os dispositivos conectados ao Hub IoT por meio de um uso de gateway do IoT Edge a mesma conexão subjacente.
* **Suavização de tráfego** -dispositivo do IoT Edge implementará automaticamente a retirada exponencial se o IoT Hub limitar o tráfego, enquanto as mensagens persistem localmente. Esse benefício torna sua solução resiliente a picos no tráfego.
* **Suporte offline** -o dispositivo de gateway armazena mensagens e atualizações de atualização de bits que não podem ser entregues ao Hub IOT.

Um gateway que faz a conversão de protocolo pode dar suporte a dispositivos existentes e novos dispositivos com restrição de recursos. Muitos dispositivos existentes estão produzindo dados que podem fomentar insights de negócios, porém, não foram projetados com a conectividade de nuvem em mente. Os gateways opacos permitem que esses dados sejam desbloqueados e usados em uma solução de IoT.

Um gateway que faz a tradução de identidade fornece os benefícios da conversão de protocolo e, além disso, permite a capacidade de gerenciamento completa de dispositivos de recebimento de dados a partir da nuvem. Todos os dispositivos da sua solução IoT são exibidos no Hub IoT, independentemente do protocolo usado.

## <a name="cheat-sheet"></a>Roteiro

Aqui está um roteiro rápido que compara os Hub IoT primitivos ao usarem gateways transparentes, opacos (protocolo) e de proxy.

| Primitiva | Gateway transparente | Conversão de protocolo | Conversão de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registro de identidade do Hub IoT | Identidades de todos os dispositivos conectados | Somente a identidade do dispositivo de gateway | Identidades de todos os dispositivos conectados |
| Dispositivo gêmeo | Cada dispositivo conectado tem seu próprio dispositivo gêmeo | Somente o gateway tem um dispositivo e módulo gêmeos | Cada dispositivo conectado tem seu próprio dispositivo gêmeo |
| Métodos diretos e mensagens da nuvem para o dispositivo | A nuvem pode lidar com cada dispositivo conectado individualmente | A nuvem pode lidar somente com o dispositivo de gateway | A nuvem pode lidar com cada dispositivo conectado individualmente |
| [Cotas e limitações do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplica-se a cada dispositivo | Aplica-se ao dispositivo de gateway | Aplica-se a cada dispositivo |

Ao usar um padrão de gateway opaco (conversão de protocolo), todos os dispositivos que se conectam por meio do gateway compartilham a mesma fila da nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Segue-se que o padrão de gateway opaco deve ser usado somente quando poucos dispositivos estão se conectando através de cada gateway de campo e seu tráfego de nuvem para dispositivo está baixo.

## <a name="next-steps"></a>Próximas etapas

Conheça as três etapas para configurar um gateway transparente:

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conectar um dispositivo downstream para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
