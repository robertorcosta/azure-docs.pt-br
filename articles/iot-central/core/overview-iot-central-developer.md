---
title: Desenvolvimento de dispositivos para o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral do desenvolvimento de dispositivos para se conectar ao seu aplicativo do IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: aa442e15dbc95709ecf3c818f69301d2f02e9b5b
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417014"
---
# <a name="iot-central-device-development-overview"></a>Visão geral do desenvolvimento de dispositivos para o IoT Central

*Este artigo se aplica a desenvolvedores de dispositivos.*

Um aplicativo do IoT Central permite que você monitore e gerencie milhões de dispositivos ao longo de seu ciclo de vida. Esta visão geral destina-se a desenvolvedores de dispositivos que implementam código para execução em dispositivos que se conectam ao IoT Central.

Os dispositivos interagem com um aplicativo do IoT Central usando os seguintes primitivos:

- _Telemetria_ são dados que um dispositivo envia para o IoT Central. Por exemplo, um fluxo de valores de temperatura de um sensor integrado.
- _Propriedades_ são valores de estado que um dispositivo relata para o IoT Central. Por exemplo, a versão atual do firmware do dispositivo. Você também pode ter propriedades graváveis que o IoT Central pode atualizar no dispositivo.
- _Comandos_ são chamados do IoT Central para controlar o comportamento de um dispositivo. Por exemplo, seu aplicativo do IoT Central pode chamar um comando para reinicializar um dispositivo.

Um construtor de soluções é responsável por configurar painéis e exibições na interface do usuário Web do IoT Central para visualizar a telemetria, gerenciar propriedades e chamar comandos.

## <a name="types-of-device"></a>Tipos de dispositivo

As seções a seguir descrevem os principais tipos de dispositivo que você pode conectar a um aplicativo do IoT Central:

### <a name="standalone-device"></a>Dispositivo autônomo

Um dispositivo autônomo se conecta diretamente ao IoT Central. Um dispositivo autônomo normalmente envia telemetria de sensores integrados ou conectados a ele para o seu aplicativo do IoT Central. Dispositivos autônomos também podem relatar valores de propriedade, receber valores de propriedade graváveis e responder a comandos.

### <a name="gateway-device"></a>Dispositivos de gateway

Um dispositivo de gateway gerencia um ou mais dispositivos downstream que se conectam ao seu aplicativo do IoT Central. Você usa o IoT Central para configurar as relações entre os dispositivos downstream e o dispositivo de gateway. Para saber mais, confira [Definir um novo tipo de dispositivo de gateway de IoT no aplicativo Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Dispositivo de borda

Um dispositivo de borda se conecta diretamente ao IoT Central, mas atua como um intermediário para outros dispositivos conhecidos como _dispositivos folha_. Um dispositivo de borda normalmente fica localizado próximo dos dispositivos folha para os quais está atuando como intermediário. Cenários que usam dispositivos de borda incluem:

- Habilitar dispositivos que não podem se conectar diretamente ao IoT Central para se conectarem por meio do dispositivo de borda. Por exemplo, um dispositivo folha pode usar Bluetooth para se conectar ao dispositivo de borda, que por sua vez se conecta pela Internet ao IoT Central.
- Agregar a telemetria antes do envio para o IoT Central. Essa abordagem pode ajudar a reduzir os custos de envio de dados ao IoT Central.
- Controlar dispositivos folha localmente para evitar a latência associada à conexão com o IoT Central pela Internet.

Um dispositivo de borda também pode enviar a própria telemetria, relatar suas propriedades e responder a atualizações de propriedade e comandos graváveis.

O IoT Central vê apenas o dispositivo de borda, não os dispositivos folha conectados a ele.

Para saber mais, confira [Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Conectar um dispositivo

O Azure IoT Central usa o [DPS (Serviço de Provisionamento de Dispositivos no Hub IoT do Azure)](../../iot-dps/about-iot-dps.md) para gerenciar todo o registro e a conexão do dispositivo.

O uso do DPS permite que:

- O IoT Central dê suporte à integração e à conexão de dispositivos em escala.
- Você gere credenciais de dispositivo e configure os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Você use as próprias identificações do dispositivo para registrar dispositivos no IoT Central. O uso das próprias identificações do dispositivo simplifica a integração aos sistemas de back-office existentes.
- Uma só maneira consistente de conectar dispositivos ao IoT Central.

Para saber mais, confira [Conectar-se ao Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Segurança

A conexão entre um dispositivo e seu aplicativo do IoT Central é protegida usando [assinaturas de acesso compartilhado](./concepts-get-connected.md#connect-devices-at-scale-using-sas) ou [certificados X.509](./concepts-get-connected.md#connect-devices-using-x509-certificates) padrão do setor.

### <a name="communication-protocols"></a>Protocolos de comunicação

Os protocolos de comunicação que um dispositivo pode usar para se conectar ao IoT Central incluem MQTT, AMQP e HTTPS. Internamente, o IoT Central usa um hub IoT para habilitar a conectividade do dispositivo. Para obter mais informações sobre os protocolos de comunicação a que o Hub IoT dá suporte para conectividade de dispositivo, confira [Escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementar o dispositivo

Use um dos [SDKs do dispositivo IoT do Azure](#languages-and-sdks) para implementar o comportamento de seu dispositivo. O código deve:

- Registrar o dispositivo com o DPS e usar as informações do DPS para se conectar ao Hub IoT interno em seu aplicativo do IoT Central.
- Enviar telemetria no formato que o modelo de dispositivo no IoT Central especifica. O IoT Central usa o modelo de dispositivo para determinar como usar a telemetria para visualizações e análises.
- Sincronizar valores de propriedade entre o dispositivo e o IoT Central. O modelo de dispositivo especifica os nomes de propriedade e os tipos de dados para que o IoT Central possa exibir as informações.
- Implementar manipuladores de comandos para os comandos especificados no modelo de dispositivo. O modelo de dispositivo especifica os nomes de comando e parâmetros que o dispositivo deve usar.

Para obter mais informações sobre a função de modelos de dispositivo, confira [O que são modelos de dispositivo?](./concepts-device-templates.md).

Para algum código de exemplo, confira [Criar e conectar um aplicativo cliente Node.js](./tutorial-connect-device-nodejs.md) ou [Criar e conectar um aplicativo cliente Python](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Linguagens e SDKs

Para obter mais informações sobre os idiomas e SDKs com suporte, confira [Entender e usar os SDKs de dispositivo do Hub IoT do Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor de dispositivos e deseja se aprofundar em algum código, a próxima etapa sugerida é [Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central](./tutorial-connect-device-nodejs.md).

Se quiser saber mais sobre como usar o IoT Central, as próximas etapas sugeridas são experimentar os guias de início rápido, começando em [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).
