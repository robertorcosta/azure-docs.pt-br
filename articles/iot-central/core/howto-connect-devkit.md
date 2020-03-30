---
title: Conectar um dispositivo DevKit ao aplicativo Azure IoT Central | Microsoft Docs
description: Como desenvolvedor de dispositivos, aprenda a conectar um dispositivo MXChip IoT DevKit ao seu aplicativo Azure IoT Central usando ioT Plug and Play (visualização).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 0a393ae8629f1742002344ee717a6719269a6722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158521"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central

Este artigo mostra como conectar um dispositivo MXChip IoT DevKit (DevKit) a um aplicativo Azure IoT Central. O dispositivo usa o modelo certificado IoT Plug and Play (preview) para o dispositivo DevKit para configurar sua conexão com a IoT Central.

Neste artigo de como fazer, você:

- Obtenha os detalhes de conexão do seu aplicativo IoT Central.
- Prepare o dispositivo e conecte-o ao seu aplicativo IoT Central.
- Veja a telemetria e as propriedades do dispositivo na IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Um [dispositivo DevKit](https://aka.ms/iot-devkit-purchase).
- Uma aplicação da Central de IoT. Você pode seguir as etapas em [Criar um aplicativo IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obtenha detalhes de conexão do dispositivo

1. No aplicativo Azure IoT Central, selecione a guia **Modelos de dispositivo** e selecione **+ Novo**. Na seção **Use um modelo de dispositivo pré-configurado,** selecione **MXChip IoT DevKit**.

    ![Modelo de dispositivo para MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecione **A seguir: Personalize** e **crie**.

1. Selecione a guia **Dispositivos.** Na lista de dispositivos, selecione **MXChip IoT DevKit** e selecione **+ Novo** para criar um novo dispositivo a partir do modelo.

    ![Novo dispositivo](media/howto-connect-devkit/new-device.png)

1. Na janela pop-up, digite o `SampleDevKit` **ID** do dispositivo como e **o nome do dispositivo** como `MXChip IoT DevKit - Sample`. Certifique-se **de que a opção Simulada** está desligada. Em seguida, **selecione Criar**.

    ![ID do dispositivo e nome](media/howto-connect-devkit/device-id-name.png)

1. Selecione o dispositivo criado e selecione **Conectar**. Anote o escopo de **id,** **id do dispositivo**e **tecla principal**. Você precisa desses valores mais tarde neste artigo de como fazer.

    ![Informações de conexão do dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Prepare o dispositivo

1. Baixe o [firmware de plug e reprodução central do Azure IoT (preview) mais recente](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) para o dispositivo DevKit do GitHub.

1. Conecte o dispositivo DevKit ao computador de desenvolvimento usando um cabo USB. No Windows, uma janela do explorador de arquivos abre em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)**.

1. Arraste o arquivo **iotc_devkit.bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reiniciado com o novo firmware.

    > [!NOTE]
    > Se você vir erros na tela como **No Wi-Fi,** isso ocorre porque o DevKit ainda não foi conectado ao WiFi.

1. No DevKit, segure o **botão B,** pressione e solte o botão **Reset** e, em seguida, solte o **botão B**. O dispositivo está agora no modo ponto de acesso. Para confirmar, a tela exibe "IoT DevKit - AP" e o endereço IP do portal de configuração.

1. No seu computador ou tablet, conecte-se ao nome da rede WiFi mostrado na tela do dispositivo. A rede WiFi começa com **AZ-** seguido pelo endereço MAC. Quando você se conecta a esta rede, você não tem acesso à internet. Este estado é esperado, e você só se conecta a esta rede por um curto período de tempo enquanto configura o dispositivo.

1. Abra seu navegador e [http://192.168.0.1/](http://192.168.0.1/)navegue para . A página da Web a seguir é exibida:

    ![Config UI](media/howto-connect-devkit/config-ui.png)

    Na página da web, digite:

    - O nome da sua rede WiFi (SSID).
    - Sua senha de rede WiFi.
    - Os detalhes da conexão: digite o **ID do dispositivo,** **o escopo de id**e a **chave primária SAS** que você fez uma nota anteriormente.

    > [!NOTE]
    > Atualmente, o IoT DevKit só pode se conectar a Wi-Fi de 2,4 GHz, 5 GHz não é suportado devido a restrições de hardware.

1. Escolha **Configurar dispositivo**, o dispositivo DevKit reinicia e executa o aplicativo:

    ![Inicialização de UI](media/howto-connect-devkit/reboot-ui.png)

    A tela DevKit exibe a confirmação de que o aplicativo está sendo executado:

    ![DevKit em execução](media/howto-connect-devkit/devkit-running.png)

O DevKit primeiro registra um novo dispositivo no aplicativo IoT Central e, em seguida, começa a enviar dados.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Nesta etapa, você vê a telemetria no aplicativo Azure IoT Central.

Em seu aplicativo IoT Central, selecione A guia **Dispositivos,** selecione o dispositivo que você adicionou. Na **guia Visão geral,** você pode ver a telemetria do dispositivo DevKit:

![Visão geral do dispositivo Central IoT](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Examine o código

Para revisar o código ou modificá-lo e compilá-lo, vá para o [Code Samples](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar um dispositivo DevKit ao seu aplicativo Azure IoT Central, o próximo passo sugerido é aprender como [configurar um modelo de dispositivo personalizado](./howto-set-up-template.md) para o seu próprio dispositivo IoT.
