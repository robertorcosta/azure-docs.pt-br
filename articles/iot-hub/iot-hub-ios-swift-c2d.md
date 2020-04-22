---
title: Mensagens de nuvem para dispositivo com Hub IoT (iOS) | Microsoft Docs
description: Como enviar mensagens de nuvem para dispositivo a um dispositivo de um Hub IoT usando os SDKs do dispositivo IoT do Azure para iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: d8552391e8e8c389a44174595305b8f28224a833
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732531"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Enviar mensagens de nuvem para dispositivo com Hub IoT (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. A [telemetria Send de um dispositivo para um hub de IoT](quickstart-send-telemetry-ios.md) mostra como criar um hub de IoT, provisionar uma identidade de dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens dispositivo-nuvem.

Este tutorial mostra como:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* A partir do back-end da solução, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas a um dispositivo do IoT Hub.

Você pode encontrar mais informações sobre mensagens da nuvem para dispositivo na [seção de mensagens do guia para desenvolvedores do Hub IoT](iot-hub-devguide-messaging.md).

No final deste artigo, você executa dois projetos do iOS Swift:

* **dispositivo de exemplo**, o mesmo aplicativo criado em [Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-ios.md), que conecta ao Hub IoT e recebe mensagens de nuvem para dispositivo.

* **serviço de amostra**, que envia uma mensagem nuvem-para-dispositivo para o aplicativo de dispositivo simulado através do IoT Hub, e, em seguida, recebe seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte a SDK para muitas plataformas de dispositivos e idiomas (incluindo C, Java, Python e Javascript) através de SDKs de dispositivos Azure IoT. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure](https://www.azure.com/develop/iot).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Um Hub IoT ativo no Azure.

* A amostra de código de [amostras do Azure.](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

* A última versão do [XCode](https://developer.apple.com/xcode/) executando a última versão do SDK do iOS. Este início rápido foi testado com o XCode 9.3 e o iOS 11.3.

* A última versão do [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="simulate-an-iot-device"></a>Simular um dispositivo IoT

Nesta seção, você simula um dispositivo iOS executando um aplicativo Swift para receber mensagens de nuvem para dispositivo do Hub IoT. 

Este é o dispositivo de amostra criado no artigo [Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-ios.md). Se você já tiver essa execução, poderá pular esta seção.

### <a name="install-cocoapods"></a>Instalar CocoaPods

Os CocoaPods gerenciam dependências para projetos do iOS que usam bibliotecas de terceiros.

Em uma janela de terminal, navegue até a pasta Azure-IoT-Samples-iOS que você baixou nos pré-requisitos. Em seguida, navegue até o projeto de exemplo:

```sh
cd quickstart/sample-device
```

Verifique se o XCode está fechado e execute o comando abaixo para instalar os CocoaPods declarados no arquivo **podfile**:

```sh
pod install
```

Além de instalar os pods necessários para o projeto, o comando de instalação também criou um arquivo de workspace do XCode que já está configurado para usar os pods para dependências.

### <a name="run-the-sample-device-application"></a>Executar o aplicativo de dispositivo de exemplo

1. Recupere a cadeia de caracteres de conexão para o dispositivo. Você pode copiar esta seqüência do [portal Azure](https://portal.azure.com) no blade de detalhes do dispositivo ou recuperá-la com o seguinte comando CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Abra o workspace de exemplo no XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expanda o projeto **Exemplo do Cliente MQTT** e, em seguida, a pasta com o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Pesquise a variável **connectionString** e atualize o valor com a cadeia de conexão do dispositivo copiada na primeira etapa.

5. Salve suas alterações. 

6. Execute o projeto no emulador do dispositivo com o botão **Build and run** ou o comando key combo + **r**.

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens nuvem-dispositivo através do hub ioT que você criou no [Enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-ios.md). Para enviar mensagens em nuvem para dispositivo, seu serviço precisa da permissão de conexão de **serviço.** Por padrão, todo IoT Hub é criado com uma política de acesso compartilhado chamada **service** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Simular um dispositivo de serviço

Nesta seção, você simula um segundo dispositivo iOS com um aplicativo Swift que envia mensagens de nuvem para dispositivo através do Hub IoT. Essa configuração é útil para cenários de IoT em que há um iPhone ou iPad funcionando como um controlador para outros dispositivos iOS conectados a um Hub IoT.

### <a name="install-cocoapods"></a>Instalar CocoaPods

Os CocoaPods gerenciam dependências para projetos do iOS que usam bibliotecas de terceiros.

Navegue até a pasta Azure-IoT-Samples-iOS que você baixou nos pré-requisitos. Em seguida, navegue até o projeto de serviço de exemplo:

```sh
cd quickstart/sample-service
```

Verifique se o XCode está fechado e execute o comando abaixo para instalar os CocoaPods declarados no arquivo **podfile**:

```sh
pod install
```

Além de instalar os pods necessários para o projeto, o comando de instalação também criou um arquivo de workspace do XCode que já está configurado para usar os pods para dependências.

### <a name="run-the-sample-service-application"></a>Executar o aplicativo de serviço de exemplo

1. Abra o workspace de exemplo no XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Expanda o projeto **AzureIoTServiceSample** e, em seguida, expanda a pasta com o mesmo nome.  

3. Abra **ViewController.swift** para edição no XCode. 

4. Procure a variável **connectionString** e atualize o valor com a seqüência de conexão de serviço que você copiou anteriormente em [Obter a seqüência de conexão de hub IoT](#get-the-iot-hub-connection-string).

5. Salve suas alterações.

6. No Xcode, altere as configurações do emulador para um dispositivo iOS diferente do usado para executar o dispositivo IoT. O XCode não pode executar vários emuladores do mesmo tipo.

   ![Alterar o dispositivo de emulador](media/iot-hub-ios-swift-c2d/change-device.png)

7. Execute o projeto no emulador de dispositivo com o botão **Compilar e executar** ou a combinação de teclas **Comando + r**.

   ![Executar o projeto](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo

Agora você está pronto para usar os dois aplicativos para enviar e receber mensagens de nuvem para dispositivo.

1. No aplicativo de **Exemplo de aplicativo iOS** em execução no dispositivo IoT simulado, clique em **Iniciar**. O aplicativo começa a enviar mensagens de dispositivo para nuvem, mas também começa a escutar mensagens de nuvem para dispositivo.

   ![Exibir o aplicativo de dispositivo IoT de exemplo](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. No aplicativo de **Exemplo do Cliente de Serviço do Hub IoT** em execução no dispositivo de serviço simulado, insira a ID do dispositivo IoT para qual deseja enviar uma mensagem. 

3. Grave uma mensagem de texto não criptografado e clique em **Enviar**.

    Várias ações ocorrerão assim que você clicar em enviar. O exemplo de serviço envia a mensagem para o Hub IoT, ao qual o aplicativo tem acesso, devido à cadeia de conexão de serviço que você forneceu. O Hub do IoT verifica a ID do dispositivo, envia a mensagem ao dispositivo de destino e envia uma confirmação de recebimento ao dispositivo de origem. O aplicativo em execução no dispositivo IoT simulado verifica as mensagens do Hub IoT e imprime o texto do mais recente na tela.

    A saída deve ser semelhante ao exemplo a seguir:

   ![Exibir mensagens da nuvem para dispositivo](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas de ponta a ponta que usam o IoT Hub, confira a documentação de [Aceleradores de Solução do IoT do Azure](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvedores do [IoT Hub](iot-hub-devguide.md).
