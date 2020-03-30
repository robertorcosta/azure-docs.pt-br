---
title: Criar um dispositivo de gateway transparente - Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo Azure IoT Edge como gateway transparente que possa processar informações de dispositivos downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6069e0782f69d0dfb73d9be2998cbb11d59d7d22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529162"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para agir como gateway transparente

Este artigo fornece instruções detalhadas para configurar um dispositivo IoT Edge para funcionar como um gateway transparente para que outros dispositivos se comuniquem com o IoT Hub. Este artigo usa o termo *gateway IoT Edge* para se referir a um dispositivo IoT Edge configurado como um gateway transparente. Para obter mais informações, consulte [Como um dispositivo IoT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>No momento:
>
> * Os dispositivos habilitados para o Edge não podem se conectar com gateways do IoT Edge.
> * Dispositivos downstream não podem usar o upload de arquivo.

Existem três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo abrange o primeiro passo:

1. **O dispositivo gateway precisa ser capaz de se conectar com segurança a dispositivos a jusante, receber comunicações de dispositivos a jusante e encaminhar mensagens para o destino adequado.**
2. O dispositivo a jusante precisa ter uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub, e saber se comunicar através de seu dispositivo gateway. Para obter mais informações, consulte [Authenticate um dispositivo downstream no Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. O dispositivo a jusante precisa se conectar ao dispositivo gateway com segurança. Para obter mais informações, consulte [Cnectar um dispositivo downstream a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).

Para que um dispositivo funcione como um gateway, ele precisa ser capaz de se conectar com segurança aos seus dispositivos a jusante. Azure IoT Edge permite que você use a infraestrutura de chave pública (PKI) para configurar conexões seguras entre dispositivos. Neste caso, nós estamos permitindo um dispositivo downstream conectar-se a um dispositivo IoT Edge atuando como um gateway transparente. Para manter a segurança razoável, o dispositivo a jusante deve confirmar a identidade do dispositivo gateway. Essa verificação de identidade impede que seus dispositivos se conectem a gateways potencialmente maliciosos.

Um dispositivo downstream em um cenário de gateway transparente pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) Em muitos casos, esses aplicativos usam o [SDK do dispositivo IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo downstream pode até ser um aplicativo em execução no próprio dispositivo de gateway IoT Edge. No entanto, um dispositivo IoT Edge não pode ser a jusante de um gateway IoT Edge.

Você pode criar qualquer infraestrutura de certificado que permite a relação de confiança necessária para sua topologia de dispositivo/gateway. Neste artigo, assumimos a mesma configuração de certificado que você usaria para habilitar a [segurança da CA X.509](../iot-hub/iot-hub-x509ca-overview.md) no IoT Hub, que envolve um certificado De CA X.509 associado a um hub específico de IoT (o HUB IoT root CA), uma série de certificados assinados com esta CA e uma CA para o dispositivo IoT Edge.

![Configuração do certificado de gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>O termo "CA raiz" utilizado ao longo deste artigo refere-se ao certificado público de maior autoridade da cadeia de certificados PKI, e não necessariamente a raiz do certificado de uma autoridade de certificado sindicalizado. Em muitos casos, é na verdade um certificado público de AC intermediário.

O gateway apresenta seu certificado CA do dispositivo IoT Edge para o dispositivo a jusante durante o início da conexão. O dispositivo a jusante verifica se o certificado CA do dispositivo IoT Edge é assinado pelo certificado DE CA raiz. Esse processo permite que o dispositivo downstream confirme que o gateway vem de uma fonte confiável.

As etapas a seguir acompanham o processo de criação dos certificados e instalá-los nos lugares certos no gateway. Você pode usar qualquer computador para gerar os certificados e, em seguida, copiá-los para seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge, configurado com [certificados de produção](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Implantar edgeHub no gateway

Quando você instala o IoT Edge pela primeira vez em um dispositivo, apenas um módulo de sistema é iniciado automaticamente: o agente IoT Edge. Uma vez que você crie a primeira implantação mais um dispositivo, o segundo módulo do sistema, o hub IoT Edge, também é iniciado.

O hub IoT Edge é responsável por receber mensagens recebidas de dispositivos a jusante e encaminhá-las para o próximo destino. Se o módulo **edgeHub** não estiver sendo executado no seu dispositivo, crie uma implantação inicial para o seu dispositivo. A implantação parecerá vazia porque você não adiciona nenhum módulo, mas garantirá que ambos os módulos do sistema estejam em execução.

Você pode verificar quais módulos estão sendo executados em um dispositivo verificando os detalhes de seu dispositivo no portal `iotedge list` Azure, visualizando o status do dispositivo no Visual Studio ou Visual Studio Code, ou executando o comando no próprio dispositivo.

Se o módulo **edgeAgent** estiver em execução sem o módulo **edgeHub,** use as seguintes etapas:

1. No portal do Azure, navegue para o hub IoT.

2. Vá para **IoT Edge** e selecione o dispositivo Edge IoT que você deseja usar como um gateway.

3. Selecione **Módulos de conjunto**.

4. Selecione **Avançar**.

5. Na página **Especificar rotas**, você deve ter uma rota padrão que envie todas as mensagens de todos os módulos para o Hub IoT. Caso contrário, adicione o código a seguir e selecione **Avançar**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Na página **Revisar modelo**, selecione **Enviar**.

## <a name="open-ports-on-gateway-device"></a>Abrir portas no dispositivo gateway

Os dispositivos Padrão IoT Edge não precisam de nenhuma conectividade de entrada para funcionar, porque toda a comunicação com o IoT Hub é feita através de conexões de saída. Os dispositivos gateway são diferentes porque precisam receber mensagens de seus dispositivos a jusante. Se um firewall estiver entre os dispositivos downstream e o dispositivo gateway, então a comunicação também precisa ser possível através do firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos suportados pelo hub IoT Edge deve estar aberto para tráfego de entrada a partir de dispositivos a jusante. Os protocolos suportados são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets.

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Rotear de mensagens de dispositivos downstream

O runtime do IoT Edge pode rotear as mensagens enviadas dos dispositivos downstream assim como as mensagens enviadas pelos módulos. Esse recurso permite que você execute análises em um módulo em execução no gateway antes de enviar qualquer dado para a nuvem.

Atualmente, a maneira como você roteia as mensagens enviadas pelos dispositivos downstream é diferenciando-as de mensagens enviadas pelos módulos. Todas as mensagens enviadas por módulos contêm uma propriedade de sistema chamada **connectionModuleId**, mas as mensagens enviadas pelos dispositivos downstream não. Você pode usar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade do sistema.

A rota abaixo é um exemplo que enviaria mensagens de `ai_insights`qualquer dispositivo `ai_insights` a jusante para um módulo chamado e, em seguida, do IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Para saber mais sobre o roteamento de mensagens, consulte [Implantar módulos e estabelecer rotas](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Habilite a operação off-line estendida

A partir da [versão v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de execução do IoT Edge, o dispositivo gateway e os dispositivos downstream que se conectam a ele podem ser configurados para uma operação offline estendida.

Com esse recurso, os módulos locais ou dispositivos a jusante podem reautenticar com o dispositivo IoT Edge conforme necessário e se comunicar uns com os outros usando mensagens e métodos mesmo quando desconectados do hub IoT. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge dispositivos, módulos e dispositivos filho](offline-capabilities.md).

Para habilitar recursos off-line estendidos, você estabelece uma relação pai-filho entre um dispositivo gateway IoT Edge e dispositivos a jusante que se conectarão a ele. Essas etapas são explicadas com mais detalhes no [Authenticate um dispositivo downstream para o Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge funcionando como gateway transparente, você precisará configurar seus dispositivos downstream para confiar no gateway e enviar mensagens para ele. Continue a [autenticar um dispositivo a jusante no Azure IoT Hub](how-to-authenticate-downstream-device.md) para os próximos passos na configuração do seu cenário de gateway transparente.
