---
title: Adicionar um dispositivo Azure IoT Edge ao Azure IoT Central | Microsoft Docs
description: Como operador, adicione um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77026389"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutorial: Adicionar um dispositivo Azure IoT Edge ao aplicativo do Azure IoT Central

Este tutorial mostra como configurar e adicionar um dispositivo Azure IoT Edge para o aplicativo do Azure IoT Central. O tutorial usa uma VM (máquina virtual) do Linux habilitada para IoT Edge do Azure Marketplace para simular um dispositivo IoT Edge. O dispositivo IoT Edge usa um módulo que gera telemetria de ambiente simulado. Você exibe a telemetria em um painel no aplicativo IoT Central.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de dispositivo para um dispositivo IoT Edge
> * Criar um dispositivo IoT Edge no IoT Central
> * Implantar um dispositivo IoT Edge simulado em uma VM do Linux

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) para criar um aplicativo do IoT Central usando o modelo de **App personalizado > Aplicativo personalizado**.

Para concluir as etapas deste tutorial, você precisa de uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Baixe o arquivo de manifesto do IoT Edge no GitHub. Clique com o botão direito do mouse no seguinte link e selecione **Salvar link como**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Criar modelo de dispositivo

Nesta seção, você cria um modelo de dispositivo para um dispositivo IoT Edge que se conecta ao aplicativo IoT Central. Você importa um manifesto IoT Edge para começar e, em seguida, modifica o modelo para adicionar definições e exibições de telemetria:

### <a name="import-manifest-to-create-template"></a>Importar manifesto para criar modelo

Para criar um modelo de dispositivo com base em um manifesto IoT Edge:

1. No aplicativo IoT Central, navegue até **Modelos de dispositivo** e selecione **+ Novo**.

1. Na página **Selecionar tipo de modelo**, selecione o bloco **Azure IoT Edge**. Em seguida, selecione **Avançar: Personalizar**.

1. Na página **Carregar um manifesto de implantação do Azure IoT Edge**, selecione **Procurar** para carregar o **EnvironmentalSensorManifest.json** que você baixou anteriormente. Em seguida, selecione **Avançar: Análise**.

1. Na página **Examinar**, selecione **Criar**.

1. Quando o modelo tiver sido criado, altere o nome dele para *Dispositivo de borda do sensor ambiental*.

1. Selecione a interface **Gerenciar** no módulo **SimulatedTemperatureSensor** para exibir as duas propriedades definidas no manifesto:

![Modelo de dispositivo criado no manifesto IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Adicionar telemetria ao manifesto

Um manifesto IoT Edge não define a telemetria que um módulo envia. Você deve adicionar as definições de telemetria ao modelo de dispositivo. O módulo **SimulatedTemperatureSensor** envia mensagens de telemetria parecidas com o seguinte JSON:

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Para adicionar as definições de telemetria ao modelo de dispositivo:

1. Selecione a interface **Gerenciar** no modelo **Dispositivo de borda do sensor ambiental**.

1. Selecione **+ Adicionar capacidade**. Insira *machine* como o **Nome de exibição**. O **Tipo de capacidade** deve ser **Telemetria**.

1. Selecione **Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição de objeto, adicione *temperature* e *pressure* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Insira *ambient* como o **Nome de exibição**. O **Tipo de capacidade** deve ser **Telemetria**.

1. Selecione **Objeto** como o tipo de esquema e, em seguida, selecione **Definir**. Na página de definição de objeto, adicione *temperature* e *humidity* como atributos do tipo **Duplo** e, em seguida, selecione **Aplicar**.

1. Selecione **+ Adicionar capacidade**. Insira *timeCreated* como o **Nome de exibição**. O **Tipo de capacidade** deve ser **Telemetria**.

1. Selecione **DateTime** como o tipo de esquema.

1. Selecione **Salvar** para atualizar o modelo.

Agora, a interface **Gerenciar** inclui os tipos de telemetria **machine**, **ambient** e **timeCreated**:

![Interface com os tipos de telemetria machine e ambient](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Adicionar exibições ao modelo

O modelo de dispositivo ainda não tem uma exibição que permite que um operador veja a telemetria do dispositivo IoT Edge. Para adicionar uma exibição ao modelo de dispositivo:

1. Selecione **Exibições** no modelo **Dispositivo de borda do sensor ambiental**.

1. Na página **Selecione para adicionar uma nova exibição**, selecione o bloco **Visualizando o dispositivo**.

1. Altere o nome da exibição para *Exibir telemetria do dispositivo IoT Edge*.

1. Selecione os tipos de telemetria **ambient** e **machine**. Em seguida, selecione **Adicionar bloco**.

1. Selecione **Salvar** para salvar a exibição **Exibir telemetria do dispositivo IoT Edge**.

![Modelo de dispositivo com exibição de telemetria](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publicar o modelo

Antes de adicionar um dispositivo que usa o modelo de **Dispositivo de borda do sensor ambiental**, você deve publicar o modelo.

Navegue até o modelo de **Dispositivo de borda do sensor de ambiente** e selecione **Publicar**. Selecione **Publicar** para publicar o modelo:

![Publicar o modelo de dispositivo](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Adicionar dispositivo do IoT Edge

Agora que você publicou o modelo de **Dispositivo de borda do sensor ambiental**, pode adicionar um dispositivo ao seu aplicativo IoT Central:

1. No aplicativo IoT Central, navegue até a página **Dispositivos** e selecione **Dispositivo de borda do sensor ambiental** na lista de modelos disponíveis.

1. Selecione **+** para adicionar um novo dispositivo do modelo. Na página **Criar dispositivo**, selecione **Criar**.

Agora você tem um novo dispositivo com o status **Registrado**:

![Publicar o modelo de dispositivo](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Obter as credenciais do dispositivo

Ao implantar o dispositivo IoT Edge mais tarde neste tutorial, você precisará das credenciais que permitem que o dispositivo se conecte ao aplicativo IoT Central. Para obter as credenciais do dispositivo:

1. Na página **Dispositivo**, selecione o dispositivo que você criou.

1. Selecione **Conectar**.

1. Na página **Conexão do dispositivo**, anote o **Escopo da ID**, a **ID do Dispositivo** e a **Chave Primária**. Esses valores serão usados depois.

1. Selecione **Fechar**.

Agora você concluiu a configuração do aplicativo IoT Central para permitir que um dispositivo IoT Edge se conecte.

## <a name="deploy-an-iot-edge-device"></a>Implantar um dispositivo IoT Edge

Neste tutorial, você usa uma VM do Linux habilitada para Azure IoT Edge, criada no Azure para simular um dispositivo IoT Edge. Para criar a VM habilitada para IoT Edge:

1. Navegue até [Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) no Azure Marketplace. Selecione **Obtenha agora**.

1. Na página **Criar este aplicativo no Azure**, selecione **Continuar**. Esse link leva você para o portal do Azure, em que talvez seja necessário entrar na sua assinatura do Azure.

1. Na página **Azure IoT Edge no Ubuntu** no portal do Azure, selecione **Criar**.

1. Na página **Criar uma máquina virtual > Noções básicas**:

    - Selecione sua assinatura do Azure.
    - Crie um grupo de recursos chamado **iot-edge-devices**.
    - Use o nome da máquina virtual: **iotedgevm**.
    - Escolha a região mais próxima a você.
    - Defina o tipo de autenticação como **Senha**.
    - Escolha um nome de usuário e uma senha.
    - É possível deixar as outras opções com os valores padrão.
    - Selecione **Examinar + criar**.

1. Após concluir a validação, selecione **Criar**.

Aguarde alguns minutos. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Provisionar uma VM como um dispositivo do IoT Edge 

Para provisionar uma VM como um dispositivo IoT Edge:

1. Na seção **Suporte + solução de problemas**, selecione **Console serial**.

1. Pressione **Enter** para ver o prompt `login:`. Insira seu nome de usuário e sua senha para entrar.

1. Execute o comando a seguir para verificar a versão do runtime do IoT Edge. No momento da escrita, a versão é 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Use o editor `nano` para abrir o arquivo config.yaml do IoT Edge:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Role a tela para baixo até ver `# Manual provisioning configuration`. Comente as próximas três linhas, conforme mostrado no seguinte snippet:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Role a tela para baixo até ver `# DPS symmetric key provisioning configuration`. Remova a marca de comentário das próximas oito linhas, conforme mostrado no seguinte snippet:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Substitua `{scope_id}` pelo **Escopo da ID** que você anotou anteriormente.

1. Substitua `{registration_id}` pela **ID do Dispositivo** que você anotou anteriormente.

1. Substitua `{symmetric_key}` pela **Chave primária** que você anotou anteriormente.

1. Salve as alterações (**Ctrl-O**) e saia (**Ctrl-X**) do editor `nano`.

1. Execute o seguinte comando para reiniciar o daemon do IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Para verificar o status dos módulos do IoT Edge, execute o seguinte comando:

    ```bash
    iotedge list
    ```

    A saída se parece com o seguinte:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Agora, o dispositivo IoT Edge simulado está em execução na VM. No aplicativo IoT Central, o status do dispositivo agora é **Provisionado** na página **Dispositivos**:

![Dispositivo provisionado](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Você pode ver a telemetria na página **Exibir telemetria do dispositivo IoT Edge**:

![Telemetria de dispositivo](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

A página **Módulos** mostra o status dos módulos do IoT Edge:

![Telemetria de dispositivo](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a trabalhar com dispositivos IoT Edge e gerenciá-los no IoT Central, esta é uma sugestão para a próxima etapa:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Configurar um gateway transparente](../../iot-edge/how-to-create-transparent-gateway.md)
