---
title: Conectar um dispositivo DevKit ao aplicativo Azure IoT Central | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo de IoT Central do Azure usando o IoT Plug and Play (versão prévia).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756798"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central

*Este artigo se aplica a desenvolvedores de dispositivos.*

Este artigo mostra como conectar um dispositivo DevKit (MXChip IoT DevKit) a um aplicativo de IoT Central do Azure. O dispositivo usa o modelo de Plug and Play de IoT certificado (versão prévia) do dispositivo DevKit para configurar sua conexão com o IoT Central.

Neste artigo de instruções, você:

- Obtenha os detalhes de conexão do seu aplicativo IoT Central.
- Prepare o dispositivo e conecte-o ao seu aplicativo IoT Central.
- Exiba a telemetria e as propriedades do dispositivo em IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Um [dispositivo devkit](https://aka.ms/iot-devkit-purchase).
- Um aplicativo IoT Central. Você pode seguir as etapas em [criar um aplicativo IOT central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Obter detalhes de conexão do dispositivo

1. No aplicativo IoT Central do Azure, selecione a guia **modelos de dispositivo** e selecione **+ novo**. Na seção **usar um modelo de dispositivo pré-configurado**, selecione **MXChip IOT devkit**.

    ![Modelo de dispositivo para MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecione **Avançar: Personalizar** e **criar**.

1. Selecione a guia **dispositivos** . Na lista dispositivos, selecione **MXChip IOT devkit** e selecione **+ novo** para criar um novo dispositivo a partir do modelo.

    ![Novo dispositivo](media/howto-connect-devkit/new-device.png)

1. Na janela pop-up, insira a **ID do dispositivo** como `SampleDevKit` e o nome do `MXChip IoT DevKit - Sample` **dispositivo** como. Verifique se a opção **Simulated** está desativada. Em seguida, selecione **Criar**.

    ![ID e nome do dispositivo](media/howto-connect-devkit/device-id-name.png)

1. Selecione o dispositivo que você criou e, em seguida, selecione **conectar**. Anote o **escopo da ID**, a **ID do dispositivo**e a **chave primária**. Você precisará desses valores posteriormente neste artigo de instruções.

    ![Informações de conexão do dispositivo](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Preparar o dispositivo

1. Baixe o mais recente [firmware predefinido do Azure IoT Central plug and Play (versão prévia)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) para o dispositivo devkit do github.

1. Conecte o dispositivo DevKit ao computador de desenvolvimento usando um cabo USB. No Windows, uma janela do explorador de arquivos abre em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)**.

1. Arraste o arquivo **iotc_devkit. bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reiniciado com o novo firmware.

    > [!NOTE]
    > Se você encontrar erros na tela, como **sem Wi-Fi**, isso ocorre porque o devkit ainda não foi conectado ao wifi.

1. No DevKit, mantenha o **botão b**, pressione e solte o botão **Redefinir** e, em seguida, solte o **botão b**. O dispositivo agora está no modo de ponto de acesso. Para confirmar, a tela exibe "IoT DevKit-AP" e o endereço IP do portal de configuração.

1. Em seu computador ou Tablet, conecte-se ao nome da rede WiFi mostrado na tela do dispositivo. A rede WiFi começa com **AZ-** seguido pelo endereço Mac. Quando você se conecta a essa rede, você não tem acesso à Internet. Esse estado é esperado e você só se conecta a essa rede por um curto período enquanto configura o dispositivo.

1. Abra o navegador da Web e navegue [http://192.168.0.1/](http://192.168.0.1/)até. A página da Web a seguir é exibida:

    ![Interface do usuário de configuração](media/howto-connect-devkit/config-ui.png)

    Na página da Web, digite:

    - O nome de sua rede WiFi (SSID).
    - Sua senha de rede WiFi.
    - Os detalhes da conexão: Insira a **ID do dispositivo**, o escopo da **ID**e a **chave primária SAS** que você anotou anteriormente.

    > [!NOTE]
    > Atualmente, o IoT DevKit pode se conectar somente a Wi-Fi de 2,4 GHz, não há suporte para 5 GHz devido a restrições de hardware.

1. Escolha **Configurar dispositivo**, o dispositivo devkit reinicializa e executa o aplicativo:

    ![Reinicializar interface do usuário](media/howto-connect-devkit/reboot-ui.png)

    A tela DevKit exibe uma confirmação de que o aplicativo está em execução:

    ![DevKit em execução](media/howto-connect-devkit/devkit-running.png)

O DevKit primeiro registra um novo dispositivo no aplicativo IoT Central e, em seguida, inicia o envio de dados.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Nesta etapa, você exibe a telemetria no aplicativo IoT Central do Azure.

No aplicativo IoT Central, selecione a guia **dispositivos** , selecione o dispositivo que você adicionou. Na guia **visão geral** , você pode ver a telemetria do dispositivo devkit:

![Visão geral do dispositivo IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Examine o código

Para examinar o código ou modificá-lo e compilá-lo, vá para os [exemplos de código](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas serão:

- Leia sobre a [conectividade do dispositivo no Azure IOT central](./concepts-get-connected.md)
- Saiba como [monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
