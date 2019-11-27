---
title: Gateways para dispositivos downstream – Azure IoT Edge | Microsoft Docs
description: Use o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou de proxy que envia dados de vários dispositivos downstream para a nuvem ou os processa localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ec8b6cf61f9fb92f888642d1de7d4d1b9b7ac3df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456646"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Como um dispositivo IoT Edge pode ser usado como um gateway

Os gateways em soluções IoT Edge fornecem conectividade de dispositivo e análise de borda para dispositivos IoT que, caso contrário, não teriam esses recursos. O Azure IoT Edge pode ser usado para atender a todas as necessidades de um gateway de IoT, independentemente de se elas estão relacionadas à conectividade, identidade ou análise de borda. Padrões de gateway, neste artigo, referem-se apenas às características de conectividade de dispositivo downstream e identidade de dispositivo, não como dados de dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões

Há três padrões de uso de um dispositivo IoT Edge como um gateway: transparente, conversão de protocolo e tradução de identidade:
* **Transparente** – dispositivos que teoricamente podem se conectar ao Hub IoT podem, em vez disso, conectar-se a um dispositivo de gateway. Os dispositivos downstream possuem suas próprias identidades Hub IoT e estão usando qualquer um dos protocolos MQTT, AMQP ou HTTP. O gateway simplesmente passa as comunicações entre os dispositivos e o Hub IoT. Os dispositivos não sabem que estão se comunicando com a nuvem por meio de um gateway, e um usuário que interage com os dispositivos no Hub IoT não está ciente do dispositivo de gateway intermediário. Assim, o gateway é transparente. Consulte [Criar um gateway transparente](how-to-create-transparent-gateway.md) para obter detalhes sobre como usar um dispositivo IoT Edge como um gateway transparente.
* **Tradução de protocolo** - Também conhecido como padrão de gateway opaco, os dispositivos que não suportam MQTT, AMQP ou HTTP podem usar um dispositivo de gateway para enviar dados para o Hub IoT em seu nome. O gateway compreende o protocolo usado pelos dispositivos downstream e é o único dispositivo que tem uma identidade no Hub IoT. Todas as informações parecem estar vindo de um dispositivo, o gateway. Os dispositivos de downstream devem incorporar informações adicionais de identificação em suas mensagens, se os aplicativos em nuvem quiserem analisar os dados em uma base por dispositivo. Além disso, as primitivas do Hub IoT, como gêmeos e métodos, estão disponíveis apenas para o dispositivo de gateway, não para dispositivos de recebimento de dados.
* **Tradução de identidade** - Dispositivos que não podem se conectar ao Hub IoT podem se conectar a um dispositivo de gateway. O gateway fornece a identidade do Hub IoT e a conversão de protocolo em nome dos dispositivos downstream. O gateway é inteligente o suficiente para entender o protocolo usado por dispositivos downstream, fornecer identidade a eles e converter Hub IoT primitivos. Dispositivos downstream aparecem no Hub IoT como dispositivos de primeira classe com gêmeos e métodos. Um usuário pode interagir com os dispositivos do Hub IoT e não tem ciência do dispositivo de gateway intermediário.

![Diagrama - transparente, protocolo e padrões de gateway de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Casos de uso
Todos padrões de gateway fornecem os seguintes benefícios:
* **Análise na borda** – use os serviços de ia localmente para processar dados provenientes de dispositivos downstream sem enviar telemetria de fidelidade total à nuvem. Encontre e reaja a insights localmente e só envie um subconjunto de dados para o Hub IoT. 
* **Isolamento de dispositivo downstream** – o dispositivo de gateway pode proteger todos os dispositivos downstream da exposição à internet. Ele pode ficar entre uma rede OT que não tenha conectividade e uma rede de TI que forneça acesso à web. 
* **Multiplexação de Conexão** – todos os dispositivos conectados ao Hub IoT por meio de um uso de gateway do IoT Edge a mesma conexão subjacente.
* **Suavização de tráfego** -dispositivo do IoT Edge implementará automaticamente a retirada exponencial se o IoT Hub limitar o tráfego, enquanto as mensagens persistem localmente. Esse benefício torna sua solução resiliente a picos no tráfego.
* **Suporte offline** -o dispositivo de gateway armazena mensagens e atualizações de atualização de bits que não podem ser entregues ao Hub IOT.

Um gateway que faz a conversão de protocolo também pode executar análise de borda, isolamento de dispositivo, suavização de tráfego e suporte off-line para dispositivos existentes e novos dispositivos restritos por recursos. Muitos dispositivos existentes estão produzindo dados que podem fomentar insights de negócios, porém, não foram projetados com a conectividade de nuvem em mente. Os gateways opacos permitem que esses dados sejam desbloqueados e usados em uma solução de IoT.

Um gateway que faz a tradução de identidade fornece os benefícios da conversão de protocolo e, além disso, permite a capacidade de gerenciamento completa de dispositivos de recebimento de dados a partir da nuvem. Todos os dispositivos da sua solução IoT são exibidos no Hub IoT, independentemente do protocolo usado.

## <a name="cheat-sheet"></a>Roteiro
Aqui está um roteiro rápido que compara os Hub IoT primitivos ao usarem gateways transparentes, opacos (protocolo) e de proxy.

| &nbsp; | Gateway transparente | Conversão de protocolo | Conversão de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registro de identidade do Hub IoT | Identidades de todos os dispositivos conectados | Somente a identidade do dispositivo de gateway | Identidades de todos os dispositivos conectados |
| Dispositivo gêmeo | Cada dispositivo conectado tem seu próprio dispositivo gêmeo | Somente o gateway tem um dispositivo e módulo gêmeos | Cada dispositivo conectado tem seu próprio dispositivo gêmeo |
| Métodos diretos e mensagens da nuvem para o dispositivo | A nuvem pode lidar com cada dispositivo conectado individualmente | A nuvem pode lidar somente com o dispositivo de gateway | A nuvem pode lidar com cada dispositivo conectado individualmente |
| [Cotas e limitações do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplica-se a cada dispositivo | Aplica-se ao dispositivo de gateway | Aplica-se a cada dispositivo |

Ao usar um padrão de gateway opaco (conversão de protocolo), todos os dispositivos que se conectam por meio do gateway compartilham a mesma fila da nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Segue-se que o padrão de gateway opaco deve ser usado somente quando poucos dispositivos estão se conectando através de cada gateway de campo e seu tráfego de nuvem para dispositivo está baixo.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar um gateway transparente: 

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conectar um dispositivo downstream para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)