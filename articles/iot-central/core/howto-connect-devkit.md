---
title: Conectar um dispositivo DevKit ao aplicativo Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4e04ae7d9594ac064c9f3707c797fb2709a79cb6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582941"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como um desenvolvedor de dispositivos conecta um dispositivo MXChip IoT DevKit (DevKit) ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

1. Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte o [criar um início rápido de aplicativo](quick-deploy-iot-central.md).
1. Um dispositivo DevKit. Para adquirir um dispositivo DevKit, visite [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Aplicativo Devkits de exemplo

Um aplicativo criado no modelo de aplicativo **devkits de exemplo** inclui um modelo de dispositivo **MXChip** que define as seguintes características de dispositivo:

- Medidas de telemetria para **umidade**, **temperatura**, **pressão**, **magnetômetro** (medida ao longo de x, y, eixo z), **acelerômetro** (medido ao longo de x, y, eixo z) e **giroscópio** (medido ao longo do eixo x, y, z).
- Medida de estado para o **estado do dispositivo**.
- Medida de evento para o **botão B pressionado**.
- Configurações de **tensão**, **atual**, **velocidade do ventilador**e uma alternância de **ir** .
- O **número de matrizes** e o **local do dispositivo**são propriedades do dispositivo, que é uma propriedade de local.
- Propriedade **de nuvem fabricada em**.
- Comandos **Echo** e **contagem regressiva**. Quando um dispositivo real recebe um comando **Echo** , ele mostra o valor enviado na tela do dispositivo. Quando um dispositivo real recebe um comando de **contagem regressiva** , o LED percorre um padrão e o dispositivo envia valores de contagem regressiva de volta para IOT central.

Para obter detalhes completos sobre a configuração, consulte [detalhes do modelo de dispositivo MXChip](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

### <a name="get-your-device-connection-details"></a>Obter detalhes de conexão do dispositivo

No aplicativo IoT Central do Azure, adicione um dispositivo real do modelo de dispositivo **MXChip** e anote os detalhes de conexão do dispositivo: **ID do escopo, ID do dispositivo e chave primária**:

1. Adicione um **dispositivo real** de Device Explorer, selecione **+ novo > real** para adicionar um dispositivo real.

    * Insira uma ID de **dispositivo**em minúsculas ou use a **ID de dispositivo**sugerida.
    * Insira um **nome de dispositivo**ou use o nome sugerido

    ![Adicionar dispositivo](media/howto-connect-devkit/add-device.png)

1. Para obter os detalhes de conexão do dispositivo, a **ID do escopo**, a **ID do dispositivo**e a **chave primária**, selecione **conectar** na página do dispositivo.

    ![Detalhes da conexão](media/howto-connect-devkit/device-connect.png)

1. Anote os detalhes da conexão. Você estará temporariamente desconectado da Internet quando preparar o dispositivo DevKit na próxima etapa.

### <a name="prepare-the-devkit-device"></a>Preparar o dispositivo DevKit

Se você já usou o dispositivo e deseja reconfigurá-lo para usar uma rede WiFi diferente, uma cadeia de conexão ou uma medição de telemetria, pressione os botões **a** e **B** ao mesmo tempo. Se não funcionar, pressione o botão **Redefinir** e tente novamente.

#### <a name="to-prepare-the-devkit-device"></a>Para preparar o dispositivo DevKit

1. Baixe o último firmware do Azure IoT Central pré-criado para o MXChip na página [versões](https://aka.ms/iotcentral-docs-MXChip-releases) no GitHub.
1. Conecte o dispositivo DevKit ao computador de desenvolvimento usando um cabo USB. No Windows, uma janela do explorador de arquivos abre em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)** .
1. Arraste o arquivo **iotCentral.bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reiniciado com o novo firmware.

1. Quando o dispositivo DevKit for reiniciado, a tela a seguir será exibida:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Se a tela exibir qualquer outra diferente dessa, redefina o dispositivo e pressione os botões  **A** e **B** no dispositivo ao mesmo tempo para reinicializar o dispositivo.

1. O dispositivo agora está no modo de AP (Ponto de Acesso). É possível conectar esse ponto de acesso WiFi a partir do computador ou dispositivo móvel.

1. No computador, telefone ou tablet, conecte o nome da rede WiFi exibido na tela do dispositivo. Quando você se conecta a essa rede, você não tem acesso à Internet. Esse estado é esperado e você só está conectado a essa rede por um curto período enquanto configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/start](http://192.168.0.1/start). A página da Web a seguir é exibida:

    ![Página de configuração do dispositivo](media/howto-connect-devkit/configpage.png)

    Na página da Web, digite:
    - O nome da rede WiFi
    - Sua senha de rede WiFi
    - O código do PIN mostrado na exibição do dispositivo
    - A ID do **escopo**dos detalhes da conexão, a **ID do dispositivo**e a **chave primária** do seu dispositivo (você já deve ter salvo isso seguindo as etapas)
    - Selecionar todas as medidas de telemetria disponíveis

1. Após escolher **Configurar Dispositivo**, você verá esta página:

    ![Dispositivo configurado](media/howto-connect-devkit/deviceconfigured.png)

1. Pressione o botão **Reiniciar** no dispositivo.

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Quando o dispositivo DevKit for reiniciado, a tela no dispositivo exibirá:

* O número de mensagens de telemetria enviadas.
* O número de falhas.
* O número de propriedades desejadas recebidas e o número de propriedades relatadas enviadas.

> [!NOTE]
> Se o dispositivo parecer fazer um loop ao tentar se conectar, verifique se o dispositivo está **bloqueado** no IOT central e **desbloqueie** o dispositivo para que ele possa se conectar ao aplicativo.

Agite o dispositivo para enviar uma propriedade relatada. O dispositivo envia um número aleatório como a propriedade de dispositivo **Número impresso**.

É possível exibir as medidas de telemetria e os valores das propriedades relatadas e definir as configurações no Azure IoT Central:

1. Use **dispositivos** para navegar até a página de **medidas** do dispositivo real MXChip que você adicionou:

    ![Navegue para o dispositivo real](media/howto-connect-devkit/realdevicenew.png)

1. Na página **Medidas**, é possível ver a telemetria proveniente do dispositivo MXChip:

    ![Exibir telemetria do dispositivo real](media/howto-connect-devkit/devicetelemetrynew.png)

1. Na página **Propriedades**, é possível ver o último número impresso e a localização do dispositivo relatados pelo dispositivo:

    ![Exibir propriedades de dispositivo](media/howto-connect-devkit/devicepropertynew.png)

1. Na página **Configurações**, é possível atualizar as configurações no dispositivo MXChip:

    ![Exibir as configurações do dispositivo](media/howto-connect-devkit/devicesettingsnew.png)

1. Na página **comandos** , você pode chamar os comandos **Echo** e **regressiva** :

    ![Comandos de chamada](media/howto-connect-devkit/devicecommands.png)

1. Na página **Painel**, você pode ver o mapa do local

    ![Exibir o painel do dispositivo](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Se você quiser explorar e modificar o código do dispositivo, poderá baixá-lo do GitHub. Se você planeja modificar o código, siga essas instruções para [preparar o ambiente de desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) do sistema operacional de área de trabalho.

Para baixar o código-fonte, execute o comando a seguir no computador desktop:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

O comando anterior baixa o código-fonte para uma pasta chamada `iot-central-firmware`.

> [!NOTE]
> Se o **git** não estiver instalado no ambiente de desenvolvimento, será possível baixá-lo a partir de [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Examine o código

Use Visual Studio Code para abrir a pasta `MXCHIP/mxchip_advanced` na pasta `iot-central-firmware`:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Para ver como a telemetria é enviada ao aplicativo de IoT Central do Azure, abra o arquivo **telemetria. cpp** na pasta `src`:

- A função `TelemetryController::buildTelemetryPayload` cria a carga de telemetria JSON usando dados dos sensores no dispositivo.

- A função `TelemetryController::sendTelemetryPayload` chama `sendTelemetry` no **AzureIOTClient. cpp** para enviar a carga JSON para o Hub IOT que seu aplicativo do Azure IOT central usa.

Para ver como os valores de propriedade são relatados para o aplicativo de IoT Central do Azure, abra o arquivo **telemetria. cpp** na pasta `src`:

- A função `TelemetryController::loop` envia a propriedade de **localização** relatada aproximadamente a cada 30 segundos. Ele usa a função `sendReportedProperty` no arquivo de origem **AzureIOTClient. cpp** .

- A função `TelemetryController::loop` envia a propriedade relatada **dieNumber** quando o dispositivo acelerômetro detecta um toque duplo. Ele usa a função `sendReportedProperty` no arquivo de origem **AzureIOTClient. cpp** .

Para ver como o dispositivo responde aos comandos chamados do aplicativo IoT Central, abra o arquivo **registeredMethodHandlers. cpp** na pasta `src`:

- A função **dmEcho** é o manipulador para o comando **Echo** . Ele mostra o **distocadovalue** arquivado na carga na tela do dispositivo.

- A função **dmCountdown** é o manipulador do comando de **contagem regressiva** . Ele altera a cor do LED do dispositivo e usa uma propriedade relatada para enviar o valor de contagem regressiva de volta para o aplicativo IoT Central. A propriedade relatada tem o mesmo nome que o comando. A função usa a função `sendReportedProperty` no arquivo de origem **AzureIOTClient. cpp** .

O código no arquivo de origem **AzureIOTClient. cpp** usa funções do [Microsoft Azure SDKs e bibliotecas de IOT para C](https://github.com/Azure/azure-iot-sdk-c) para interagir com o Hub IOT.

Para obter informações sobre como modificar, compilar e carregar o código de exemplo no dispositivo, consulte o arquivo **readme.md** na pasta `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Detalhes do modelo de Dispositivo de MXChip

Um aplicativo criado com base no modelo de aplicativo de Devkits de Exemplo inclui um modelo de dispositivo MXChip com as características a seguir:

### <a name="measurements"></a>Medidas

#### <a name="telemetry"></a>Telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Estados 
| Nome          | Nome de exibição   | NORMAL | CUIDADO | PERIGO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Estado do dispositivo   | Verde  | Laranja  | Vermelho    | 

#### <a name="events"></a>Eventos 
| Nome             | Nome de exibição      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Botão B Pressionado  | 

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltagem      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Atual      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Configurações de alternância

| Nome de exibição | Nome do campo | Texto ativado | Texto desativado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | DESATIVADO      | Desativar     |

### <a name="properties"></a>Propriedades

| Tipo            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Propriedade de dispositivo | Localização do dispositivo   | location  | location    |
| Texto            | Fabricado em     | manufacturedIn   | N/D       |

### <a name="commands"></a>Comandos

| Nome de exibição | Nome do campo | Tipo de retorno | Nome de exibição do campo de entrada | Nome do campo de entrada | Tipo de campo de entrada |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Eco         | echo       | texto        | valor a ser exibido         | disreproduçãovalue   | texto             |
| Contagem regressiva    | Contagem regressiva  | número      | Contar de               | countFrom        | número           |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um MXChip IoT DevKit ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.
