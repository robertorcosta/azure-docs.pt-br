---
title: Conectar um Raspberry Pi ao aplicativo Azure IoT Central (Python) | Microsoft Docs
description: Como um desenvolvedor de dispositivos, como conectar um Raspberry Pi ao seu aplicativo IoT Central do Azure usando o Python.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3daa567a916bd0abeb407028c7d06bd1f2bd464b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454098"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Conectar um Raspberry Pi ao aplicativo Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como um desenvolvedor de dispositivos conecta um Raspberry Pi ao aplicativo Microsoft Azure IoT Central usando a linguagem de programação Python.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas deste artigo, você precisa dos seguintes componentes:

* Um aplicativo de IoT Central do Azure criado com base no modelo de aplicativo **herdado** . Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
* Um dispositivo Raspberry Pi executando o sistema operacional Raspbian. O Raspberry Pi deve ser capaz de se conectar à Internet. Para obter mais informações, consulte [Configurando o Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

> [!TIP]
> Para saber mais sobre como configurar e conectar-se a um dispositivo Raspberry Pi, visite introdução [ao Raspberry Pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

No aplicativo IoT Central do Azure, adicione um novo modelo de dispositivo do **Raspberry Pi** com as seguintes características:

- Telemetria, que inclui as seguintes medidas que o dispositivo coletará:
  - Umidade
  - Temperatura
  - Pressão
  - Magnetômetro (X, Y, Z)
  - Acelerômetro (X, Y, Z)
  - Giroscópio (X, Y, Z)
- Configurações
  - Voltagem
  - Current
  - Velocidade da ventoinha
  - Alternar IR.
- Propriedades
  - Propriedade do dispositivo número de dado
  - Propriedade de localização da nuvem

1. Selecione **+ novo** nos modelos de dispositivo ![modelo de dispositivo](media/howto-connect-raspberry-pi-python/adddevicetemplate.png)
   

2. Selecione **Raspberry Pi** e crie o modelo de dispositivo Raspberry Pi ![adicionar modelo de dispositivo](media/howto-connect-raspberry-pi-python/newdevicetemplate.png)

Para obter os detalhes completos da configuração do modelo de dispositivo, consulte os [detalhes do modelo de dispositivo do Raspberry Pi](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo IoT Central do Azure, adicione um dispositivo real do modelo de dispositivo **Raspberry Pi** . Anote os detalhes de conexão do dispositivo (**ID do escopo**, **ID do dispositivo**e **chave primária**). Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).

### <a name="configure-the-raspberry-pi"></a>Configurar o Raspberry Pi

As etapas a seguir descrevem como baixar e configurar o aplicativo Python de exemplo do GitHub. Este aplicativo de exemplo:

* Envia dados de telemetria e valores de propriedade para o Azure IoT Central.
* Responde a alterações de configuração feitas no Azure IoT Central.

1. Conecte-se a um ambiente de Shell em seu Raspberry Pi, seja da área de trabalho do Raspberry Pi ou remotamente usando SSH.

1. Execute o seguinte comando para instalar o IoT Central cliente Python:

    ```bash
    pip install iotc
    ```

1. Baixe o código Python de exemplo:

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. Edite o arquivo de `app.py` que você baixou e substitua os espaços reservados `DEVICE_ID`, `SCOPE_ID`e `PRIMARY/SECONDARY device KEY` pelos valores de conexão anotados anteriormente. Salve suas alterações.

    > [!TIP]
    > No Shell no Raspberry Pi, você pode usar os editores de texto **nano** ou **vi** .

1. Use o seguinte comando para executar a amostra:

    ```bash
    python app.py
    ```

    O Raspberry Pi começa a enviar medidas de telemetria para o Azure IoT Central.

1. No aplicativo Azure IoT Central, é possível ver como o código em execução no Raspberry Pi interage com o aplicativo:

    * Na página **Medidas** do dispositivo real, é possível ver a telemetria enviada do Raspberry Pi.
    * Na página **Propriedades** , você pode ver a propriedade de dispositivo **número de matriz** .
    * Na página **configurações** , você pode alterar as configurações no Raspberry Pi, como tensão e velocidade do ventilador. Quando o Raspberry Pi reconhece a alteração, a configuração é mostrada como **sincronizada**.

## <a name="raspberry-pi-device-template-details"></a>Detalhes do modelo de dispositivo Raspberry Pi

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as características a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1\.000    | 0              |
| magnetometerY  | mgauss | -1000   | 1\.000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1\.000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltagem      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1\.000    | 0       |

Configurações de alternância

| Nome de exibição | Nome do campo | Texto ativado | Texto desativado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | OFF      | Desligar     |

### <a name="properties"></a>Propriedades

| Tipo            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Texto            | Local     | local   | N/D       |

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como conectar um Raspberry Pi ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.
