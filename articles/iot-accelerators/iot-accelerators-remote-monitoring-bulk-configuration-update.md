---
title: Gerenciar dispositivos conectados ao Monitoramento Remoto em massa – Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a gerenciar os dispositivos conectados a uma solução de Monitoramento Remoto em massa.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565571"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Tutorial: Gerenciar dispositivos conectados em massa

Neste tutorial, você usa o acelerador de solução de Monitoramento Remoto para gerenciar a configuração de seus dispositivos conectados em massa.

Como um operador da Contoso, você precisa configurar um grupo de dispositivos com uma nova versão de firmware. Você não quer ter que atualizar o firmware em cada dispositivo individualmente. Para atualizar o firmware em um grupo de dispositivos, é possível usar grupos de dispositivos e gerenciamento de dispositivos automático no acelerador de solução de Monitoramento Remoto. Qualquer dispositivo que você adicionar ao grupo de dispositivos obterá o firmware mais recente assim que ficar online.

Neste tutorial, você:

>[!div class="checklist"]
> * Criar um grupo de dispositivos
> * Preparar e hospedar o firmware
> * Criar uma configuração de dispositivo no portal do Azure
> * Importar uma configuração de dispositivo para sua solução de Monitoramento Remoto
> * Implantar a configuração nos dispositivos no grupo de dispositivos
> * Monitorar a implantação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada do acelerador de solução de Monitoramento Remoto em sua assinatura do Azure.

Se você ainda não implantou o acelerador de solução de Monitoramento Remoto, conclua o início rápido [Implantar uma solução de monitoramento remoto baseado em nuvem](quickstart-remote-monitoring-deploy.md).

Você precisa de uma conta de armazenamento do Azure para hospedar os arquivos de firmware. Você pode usar uma conta de armazenamento existente ou [criar uma nova conta de armazenamento](../storage/common/storage-account-create.md) em sua assinatura.

O tutorial usa um dispositivo [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) como dispositivo de exemplo.

Você precisa ter os seguintes softwares instalado em seu computador local:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* A extensão [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) do VS Code.

Antes de iniciar:

* Verifique se o [carregador de inicialização em seu dispositivo IoT DevKit está na versão 1.4.0 ou posterior](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Verifique se o SDK do IoT DevKit possui a mesma versão do carregador de inicialização. Você pode atualizar o SDK do IoT DevKit usando o Azure IoT Workbench no VS Code. Abra a paleta de comandos e insira **Arduino: Board Manager**. Para obter mais informações, consulte [Preparar o ambiente de desenvolvimento](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Você também precisará conectar pelo menos um dispositivo IoT DevKit ao seu acelerador de solução de Monitoramento Remoto. Se você ainda não conectou um dispositivo IoT DevKit, consulte [Conectar o MXChip IoT DevKit AZ3166 ao acelerador de solução de Monitoramento Remoto de IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Navegue até o painel

Para exibir no painel de solução de Monitoramento remoto em seu navegador, primeiro navegue até [Aceleradores de solução de IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard). Você pode ser solicitado a entrar usando suas credenciais da assinatura do Azure.

Em seguida, clique em **Iniciar** no bloco para o acelerador de solução de Monitoramento remoto implantado no [Início rápido](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para atualizar automaticamente o firmware em um grupo de dispositivos, eles devem ser membros de um grupo de dispositivos em sua solução de Monitoramento Remoto:

1. Na página **Dispositivos**, selecione todos os dispositivos **IoT DevKit** que você conectou ao acelerador de solução. Em seguida, clique em **Trabalhos**.

1. No painel **Trabalhos**, selecione **Marcas**, defina o nome do trabalho **AddDevKitTag** e adicione uma marca de texto chamada **IsDevKitDevice** com um valor **Y**. Em seguida, clique em **Aplicar**.

1. Agora você pode usar os valores da marca para criar um grupo de dispositivos. Na página **Dispositivos**, clique em **Gerenciar grupos de dispositivos**.

1. Crie um filtro de texto que use o nome da marca **IsDevKitDevice** e o valor **Y** na condição. Salve o grupo de dispositivos como **Dispositivos IoT DevKit**.

Posteriormente no tutorial, você usará esse grupo de dispositivos para aplicar uma configuração de dispositivo que atualiza o firmware de todos os membros.

## <a name="prepare-and-host-the-firmware"></a>Preparar e hospedar o firmware

A extensão [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) do VS Code inclui o código de dispositivo de exemplo para a atualização do firmware.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Abra o exemplo Firmware OTA no VS Code

1. Verifique se o IoT DevKit não está conectado ao computador. Inicie o VS Code e conecte o Kit de Desenvolvimento ao seu computador.

1. Pressione **F1** para abrir a paleta de comandos, digite e selecione **IoT Workbench: Exemplos**. Em seguida, selecione **IoT DevKit** como o painel.

1. Encontre **Firmware OTA** e clique em **Exemplo Aberto**. Uma nova janela do VS Code é aberta e mostra a pasta do projeto **firmware_ota**:

    ![IoT Workbench, selecione exemplo de Firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Compilar o novo firmware

A versão inicial do firmware do dispositivo é 1.0.0. O novo firmware deve ter um número de versão superior.

1. No VS Code, abra o arquivo **FirmwareOTA.ino** e altere a `currentFirmwareVersion` de `1.0.0` para `1.0.1`:

    ![Alterar versão do firmware](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comandos e, em seguida, digite e selecione **IoT Workbench: Dispositivo**. Em seguida, selecione **Compilar Dispositivo** para compilar o código:

    ![Compilação de dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    O VS Code salva o arquivo compilado na pasta `.build` do projeto. Dependendo de suas configurações, o VS Code pode ocultar a pasta `.build` na exibição do explorer.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Gerar o valor de CRC e calcular o tamanho do arquivo de firmware

1. Abra a paleta de comandos e, em seguida, digite e selecione **IoT Workbench: Dispositivo**. Em seguida, selecione **Gerar CRC**:

    ![Gerar CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. O VS Code gera e imprime o valor de CRC, o nome de arquivo e caminho do firmware e o tamanho do arquivo na janela de saída. Anote esses valores para usá-los mais tarde:

    ![Informações de CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Carregar o firmware para a nuvem

Use sua conta de armazenamento do Azure para hospedar o novo arquivo de firmware na nuvem.

1. Navegue até sua conta de armazenamento no portal do Azure. Na seção Serviços, selecione **Blobs**. Crie um contêiner público denominado **firmware** para armazenar os arquivos de firmware:

    ![Criar pasta](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Para carregar seu arquivo de firmware para o contêiner, selecione o contêiner **firmware** e clique em **Carregar**.

1. Selecione o **FirmwareOTA.ino.bin**. Você anotou o caminho completo para esse arquivo na seção anterior.

1. Após o upload do arquivo de firmware ser concluído, anote a URL do arquivo.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Compilar e carregar o firmware original para o dispositivo IoT DevKit

1. No VS Code, abra o arquivo **FirmwareOTA.ino** e restaure a `currentFirmwareVersion` para `1.0.0`:

    ![Versão 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comandos e, em seguida, digite e selecione **IoT Workbench: Dispositivo**. Em seguida, selecione **Carregar Dispositivo**:

    ![Upload de dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. O VS Code verifica e carrega o código para seu dispositivo IoT DevKit.

1. Quando o upload for concluído, o dispositivo IoT DevKit será reinicializado. Quando a reinicialização for concluída, a tela do IoT DevKit mostrará **Versão do FW: 1.0.0** e indicará que ele está verificando a existência do novo firmware:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Criar uma configuração de dispositivo

Uma configuração de dispositivo especifica o estado desejado de seus dispositivos. Normalmente, um desenvolvedor [cria a configuração](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) na página de **configuração do dispositivo IoT** no portal do Azure. Uma configuração de dispositivo é um documento JSON que especifica o estado desejado de seus dispositivos e um conjunto de métricas.

Salve a configuração a seguir como um arquivo chamado **firmware-update.json** em seu computador local. Substitua os espaços reservados `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` e `YOURPACKAGESIZE` pelos valores que você anotou anteriormente:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Use esse arquivo de configuração na seção a seguir.

## <a name="import-a-configuration"></a>Importar uma configuração

Nesta seção, importe a configuração do dispositivo como um pacote para o acelerador de solução de Monitoramento Remoto. Normalmente, um operador conclui essa tarefa.

1. Na interface do usuário Web do Monitoramento Remoto, navegue até a página **Pacotes** e clique em **+ Novo Pacote**:

    ![Novo pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. No painel **Novo Pacote**, escolha **Configuração do dispositivo** como o tipo de pacote e **Firmware** como o tipo de configuração. Clique em **Procurar** para localizar o arquivo **firmware-update.json** em seu computador local e clique em **Carregar**:

    ![Carregar pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A lista de pacotes agora inclui o pacote **firmware-update**.

## <a name="deploy-the-configuration-to-your-devices"></a>Implantar a configuração em seus dispositivos

Nesta seção, você cria e executa uma implantação que aplica a configuração do dispositivo a seus dispositivos IoT DevKit.

1. Na interface de usuário Web do Monitoramento Remoto, navegue até a página **Implantações** e clique em **+ Nova implantação**:

    ![Nova implantação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. No painel **Nova implantação**, crie uma implantação com as seguintes configurações:

    |Opção|Valor|
    |---|---|
    |Nome|Implantar atualização de firmware|
    |Tipo de pacote|Configuração do dispositivo|
    |Tipo de configuração|Firmware|
    |Pacote|firmware-update.json|
    |Grupo de dispositivos|Dispositivos IoT DevKit|
    |Prioridade|10|

    ![Criar implantação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Clique em **Aplicar**. Você verá uma nova implantação na página **Implantações** que mostra as seguintes métricas:

    * **Destino** mostra o número de dispositivos no grupo de dispositivos.
    * **Aplicado** mostra o número de dispositivos que foram atualizados com o conteúdo da configuração.
    * **Êxito** mostra o número de dispositivos na implantação que indicaram êxito.
    * **Falha** mostra o número de dispositivos na implantação que indicaram falha.

## <a name="monitor-the-deployment"></a>Monitorar a implantação

Depois de alguns minutos, o IoT DevKit recupera as novas informações de firmware e começa a baixá-las no dispositivo:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

Dependendo da velocidade da sua rede, o download pode demorar alguns minutos. Depois que o firmware é baixado, o dispositivo verifica o tamanho do arquivo e o valor de CRC. A tela no MXChip exibe **aprovado** se a verificação é bem-sucedida.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Se a verificação for bem-sucedida, o dispositivo será reinicializado. Você verá uma contagem regressiva de **5** a **0** antes do início da reinicialização.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Após a reinicialização, o carregador de inicialização do IoT DevKit atualiza o firmware para a nova versão. A atualização pode levar vários segundos. Durante esse estágio, o LED de RGB no dispositivo fica vermelho e a tela fica em branco.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Quando a reinicialização for concluída, seu dispositivo IoT DevKit passará a executar a versão 1.0.1 do firmware.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na página **Implantações**, clique em uma implantação para ver o status dos dispositivos enquanto eles vão sendo atualizados. Você pode ver o status de cada dispositivo em seu grupo de dispositivos e as métricas personalizadas que você definiu.

![Detalhes da implantação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como atualizar o firmware de um grupo de dispositivos conectados à sua solução. Para atualizar os dispositivos, sua solução usa gerenciamento de dispositivo automático. Para saber mais sobre o recurso de gerenciamento de dispositivo automático no hub IoT que é a base da sua solução, consulte [Configurar e monitorar dispositivos IoT em escala usando o portal do Azure](../iot-hub/iot-hub-auto-device-config.md).