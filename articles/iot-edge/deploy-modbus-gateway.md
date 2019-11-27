---
title: Converter os protocolos modbus com gateways – Azure IoT Edge | Microsoft Docs
description: Permitir que os dispositivos que usam o Modbus TCP se comuniquem com o Hub IoT do Azure por meio da criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457205"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Conectar dispositivos Modbus TCP por meio de um gateway de dispositivo do IoT Edge

Se você quiser conectar dispositivos IoT que usam os protocolos Modbus TCP ou RTU a um hub IoT do Azure, use um dispositivo IoT Edge como um gateway. O dispositivo de gateway lê os dados dos dispositivos Modbus e comunica esses dados à nuvem usando um protocolo com suporte.

![Dispositivos Modbus se conectam ao Hub IoT por meio do gateway de IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo trata de como criar sua própria imagem de contêiner para um módulo Modbus (ou você pode usar um exemplo pré-compilado) e, em seguida, implantá-lo no dispositivo IoT Edge que atuará como seu gateway.

Este artigo pressupõe que você está usando o protocolo TCP Modbus. Para obter mais informações sobre como configurar o módulo para compatibilidade com o Modbus RTU, confira o projeto do [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no GitHub.

## <a name="prerequisites"></a>pré-requisitos
* Um dispositivo do Azure IoT Edge. Para obter instruções sobre como configurar um, consulte [implantar Azure IOT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de caracteres de conexão de chave primária para o dispositivo IoT Edge.
* Um dispositivo Modbus simulado ou físico que oferece suporte ao Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparar um contêiner Modbus

Se você quiser testar a funcionalidade de gateway Modbus, a Microsoft tem um exemplo de módulo que pode usar. Você pode acessar o módulo no Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)ou com o URI da imagem, **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Se quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, haverá um projeto de software livre do [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no GitHub. Siga as orientações no projeto para criar sua própria imagem de contêiner. Para criar uma imagem de contêiner, consulte [desenvolver C# módulos no Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou [desenvolver módulos no Visual Studio Code](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre como criar novos módulos e publicar imagens de contêiner em um registro.

## <a name="try-the-solution"></a>Experimente a solução

Esta seção percorre a implantação do módulo Modbus de exemplo da Microsoft em seu dispositivo IoT Edge.

1. No [Portal do Azure](https://portal.azure.com/), vá ao hub IoT.

2. Acesse **IoT Edge** e clique no dispositivo IoT Edge.

3. Selecione **Definir módulos**.

4. Adicione o módulo Modbus:

   1. Clique em **Adicionar** e selecione **módulo do IoT Edge**.

   2. No campo **Nome**, digite "modbus".

   3. No campo **Imagem**, digite o URI de imagem do exemplo de contêiner: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Marque a caixa **Habilitar** para atualizar as propriedades desejadas do gêmeo do módulo.

   5. Copie o JSON a seguir para a caixa de texto. Altere o valor de **SlaveConnection** para o endereço IPv4 do seu dispositivo Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Selecione **Salvar**.

5. De volta à etapa **Adicionar módulos**, clique em **Avançar**.

7. Na etapa **Especificar Rotas**, copie o JSON seguinte para a caixa de texto. Essa rota envia todas as mensagens coletadas pelo módulo Modbus ao Hub IoT. Nessa rota, **modbusOutput** é o ponto de extremidade que o módulo Modbus usa para dados de saída e **$upstream** é um destino especial que informa IOT Edge Hub para enviar mensagens ao Hub IOT.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Selecione **Avançar**.

9. Na etapa **Revisar Implantação**, selecione **Enviar**.

10. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Você deverá ver o novo módulo **modbus** em execução junto com o runtime do IoT Edge.

## <a name="view-data"></a>Exibir dados
Exiba os dados provenientes do módulo modbus:
```cmd/sh
iotedge logs modbus
```

Também é possível exibir a telemetria que o dispositivo está enviando usando a [Extensão do Kit de ferramentas do Hub IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente do Kit de Ferramentas do Azure IoT).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como IoT Edge dispositivos podem atuar como gateways, consulte [criar um dispositivo de IOT Edge que atue como um gateway transparente](./how-to-create-transparent-gateway.md).
- Para obter mais informações sobre como IoT Edge módulos funcionam, consulte [entender módulos Azure IOT Edge](iot-edge-modules.md).
