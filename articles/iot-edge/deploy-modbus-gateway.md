---
title: Converter os protocolos modbus com gateways – Azure IoT Edge | Microsoft Docs
description: Permitir que os dispositivos que usam o Modbus TCP se comuniquem com o Hub IoT do Azure por meio da criação de um dispositivo de gateway do IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511137"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Conectar dispositivos Modbus TCP por meio de um gateway de dispositivo do IoT Edge

Se você quiser conectar dispositivos IoT que usam protocolos Modbus TCP ou RTU a um hub Azure IoT, você pode usar um dispositivo IoT Edge como um gateway. O dispositivo de gateway lê os dados dos dispositivos Modbus e comunica esses dados à nuvem usando um protocolo com suporte.

![Dispositivos Modbus se conectam ao IoT Hub através do gateway IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo trata de como criar sua própria imagem de contêiner para um módulo Modbus (ou você pode usar um exemplo pré-compilado) e, em seguida, implantá-lo no dispositivo IoT Edge que atuará como seu gateway.

Este artigo pressupõe que você está usando o protocolo TCP Modbus. Para obter mais informações sobre como configurar o módulo para compatibilidade com o Modbus RTU, confira o projeto do [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo do Azure IoT Edge. Para obter um passo a passo sobre como configurar um, consulte [Implantar Azure IoT Edge no Windows](quickstart.md) ou [Linux](quickstart-linux.md).
* A cadeia de caracteres de conexão de chave primária para o dispositivo IoT Edge.
* Um dispositivo Modbus simulado ou físico que oferece suporte ao Modbus TCP. Você precisará saber seu endereço IPv4.

## <a name="prepare-a-modbus-container"></a>Preparar um contêiner Modbus

Se você quiser testar a funcionalidade de gateway Modbus, a Microsoft tem um exemplo de módulo que pode usar. Você pode acessar o módulo a partir do Azure Marketplace, [Modbus,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)ou com a imagem URI, `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Se quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, haverá um projeto de software livre do [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no GitHub. Siga as orientações no projeto para criar sua própria imagem de contêiner. Para criar uma imagem de contêiner, consulte [desenvolver módulos C# no Visual Studio](how-to-visual-studio-develop-csharp-module.md) ou Desenvolver [módulos em Visual Studio Code](how-to-vs-code-develop-module.md). Esses artigos fornecem instruções sobre a criação de novos módulos e a publicação de imagens de contêineres para um registro.

## <a name="try-the-solution"></a>Experimente a solução

Esta seção acompanha a implantação do módulo Modbus de amostra da Microsoft para o seu dispositivo IoT Edge.

1. No [Portal do Azure](https://portal.azure.com/), vá ao hub IoT.

2. Acesse **IoT Edge** e clique no dispositivo IoT Edge.

3. Selecione **Definir módulos**.

4. Na seção **Módulos de Borda IoT,** adicione o módulo Modbus:

   1. Clique no **Add** dropdown e selecione **Módulo de Marketplace**.
   2. Procure `Modbus` e selecione o **Módulo TCP modbus** pela Microsoft.
   3. O módulo é configurado automaticamente para o seu Hub IoT e aparece na lista de Módulos de Borda IoT. As rotas também são configuradas automaticamente. Selecione **Revisão + criar**.
   4. Revise o manifesto de implantação e selecione **Criar**.

5. Selecione o módulo `ModbusTCPModule`Modbus, na lista e selecione a guia **Configurações duplas** do módulo. O JSON necessário para as propriedades desejadas do módulo twin é preenchido automaticamente.

6. Procure a propriedade **SlaveConnection** no JSON e defina seu valor para o endereço IPv4 do seu dispositivo Modbus.

7. Selecione **Atualização**.

8. Selecione **'Revisar + criar',** revisar a implantação e, em seguida, selecione **Criar**.

9. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Você deve ver `ModbusTCPModule` o novo módulo funcionando junto com o tempo de execução do IoT Edge.

## <a name="view-data"></a>Exibir dados

Veja os dados que vêm através do módulo Modbus:

```cmd/sh
iotedge logs modbus
```

Você também pode visualizar a telemetria que o dispositivo está enviando usando a [extensão Azure IoT Hub para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (antiga extensão Azure IoT Toolkit).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como os dispositivos IoT Edge podem atuar como gateways, consulte [Criar um dispositivo IoT Edge que age como um gateway transparente](./how-to-create-transparent-gateway.md).
* Para obter mais informações sobre como os módulos IoT Edge funcionam, consulte [Entenda módulos De Borda IoT do Azure](iot-edge-modules.md).
