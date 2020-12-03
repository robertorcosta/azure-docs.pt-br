---
title: 'Tutorial: Detectar anomalias na borda em uma solução – Azure | Microsoft Docs'
description: Neste tutorial, você aprenderá a monitorar seus dispositivos do IoT Edge usando o acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e234cc29589e7b104ea7ef54ab7493e27d11cf27
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460431"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Tutorial: Detectar anomalias na borda com o acelerador de solução de Monitoramento Remoto

Neste tutorial, você deve configurar a solução de Monitoramento Remoto para responder às anomalias detectadas por um dispositivo do IoT Edge. Os dispositivos do IoT Edge permitem processar dados telemétricos na borda para reduzir o volume de telemetria enviada à solução e permitir respostas mais rápidas para eventos nos dispositivos. Para saber mais sobre os benefícios do processamento de borda, confira [O que é o Azure IoT Edge](../iot-edge/about-iot-edge.md).

Para apresentar o processamento de borda com monitoramento remoto, este tutorial usa um dispositivo de bomba de extração de petróleo simulado. A bomba de extração de petróleo é gerenciada por uma organização chamada Contoso e está conectada ao acelerador de solução de Monitoramento Remoto. Os sensores na bomba de extração de petróleo medem a temperatura e a pressão. Os operadores na Contoso sabem que um aumento anormal de temperatura pode causar atrasos na bomba de extração de petróleo. Os operadores na Contoso não precisam monitorar a temperatura do dispositivo se ele estiver dentro do intervalo normal.

A Contoso deseja implantar um módulo de borda inteligente na bomba de extração de petróleo para detectar anomalias de temperatura. Outro módulo do borda envia alertas para a solução de Monitoramento Remoto. Quando um alerta é recebido, um operador da Contoso pode enviar um técnico de manutenção. A Contoso também pode configurar uma ação automatizada, como enviar um email, para ser executada quando a solução receber um alerta.

O diagrama a seguir mostra os principais componentes no cenário do tutorial:

![O diagrama mostra o conector da bomba de óleo conectado ao módulo de análise de fluxo do IoT Edge no dispositivo do IoT Edge para telemetria e comandos. A telemetria filtrada vai para o dispositivo do IoT Edge no Acelerador de solução de monitoramento remoto na nuvem. A nuvem também contém a Implantação e o Pacote. A implantação implanta o runtime do IoT Edge no dispositivo.](media/iot-accelerators-remote-monitoring-edge/overview.png)

Neste tutorial, você:

>[!div class="checklist"]
> * Adicionar um dispositivo do IoT Edge à solução
> * Criar um manifesto do Edge
> * Importar o manifesto como um pacote para definir os módulos que serão executados no dispositivo
> * Implantar o pacote no dispositivo do IoT Edge
> * Exibir alertas a partir do dispositivo

No dispositivo do IoT Edge:

* O runtime recebe o pacote e instala os módulos.
* O módulo do Stream Analytics detecta anomalias de temperatura na bomba e envia comandos para resolver o problema.
* O módulo do Stream Analytics encaminha os dados filtrados para o acelerador de solução.

Este tutorial usa uma máquina virtual do Linux como dispositivo do IoT Edge. Você também pode instalar um módulo do edge para simular o dispositivo de tomada de bomba de petróleo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Adicionar um dispositivo do IoT Edge

Há duas etapas para adicionar um dispositivo do IoT Edge ao acelerador de solução de Monitoramento Remoto. Esta seção mostra como:

* Adicionar um dispositivo do IoT Edge na página **Device Explorer** da interface do usuário Web do Monitoramento Remoto.
* Instalar o runtime do IoT Edge em uma VM (máquina virtual) do Linux.

### <a name="add-an-iot-edge-device-to-your-solution"></a>Adicionar um dispositivo do IoT Edge à solução

Para adicionar um dispositivo do IoT Edge ao acelerador de solução de Monitoramento Remoto, navegue até a página **Device Explorer** na interface do usuário Web e clique em **+ Novo dispositivo**.

No painel **Novo dispositivo**, escolha **Dispositivo do IoT Edge** e insira **oil-pump** como a ID do dispositivo. Deixe os valores padrão para as outras configurações. Em seguida, clique em **Aplicar**:

[![Adicionar dispositivo do IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Anote a cadeia de conexão do dispositivo. Você precisará dela na próxima seção deste tutorial.

Quando você registrar um dispositivo no hub IoT no acelerador de solução de Monitoramento Remoto, ele será listado na página **Device Explorer** da interface do usuário Web:

[![Novo dispositivo do IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Para facilitar o gerenciamento de dispositivos do IoT Edge na solução, crie um grupo de dispositivos e adicione o dispositivo do IoT Edge:

1. Escolha o dispositivo **oil-pump** na lista da página **Device Explorer** e clique em **Trabalhos**.

1. Crie um trabalho para adicionar a marca **IsEdge** ao dispositivo usando as seguintes configurações:

    | Configuração | Valor |
    | ------- | ----- |
    | Trabalho     | Marcas  |
    | Nome do Trabalho | AddEdgeTag |
    | Chave     | IsOilPump |
    | Valor   | Y     |
    | Tipo    | Texto  |

    [![Adicionar marca](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Clique em **Aplicar** e em **Fechar**.

1. Na página **Device Explorer**, clique em **Gerenciar grupos de dispositivos**.

1. Clique em **Criar novo grupo de dispositivos**. Crie um novo grupo de dispositivos com as seguintes configurações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome    | OilPumps |
    | Campo   | Tags.IsOilPump |
    | Operador | = Equals |
    | Valor    | Y |
    | Tipo     | Texto |

    [![Criar um grupo de dispositivos](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Clique em **Save** (Salvar).

O dispositivo do IoT Edge já está no grupo **OilPumps**.

### <a name="install-the-edge-runtime"></a>Instalar o runtime do Edge

Um dispositivo de borda requer a instalação do runtime do Edge. Neste tutorial, você deve instalar o runtime do Edge em uma VM Linux do Azure para testar o cenário. As etapas a seguir usam o Azure Cloud Shell para instalar e configurar a VM:

1. Para criar uma VM Linux no Azure, execute os comandos a seguir. É possível usar um local perto de onde você está:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Para configurar o runtime do Edge com a cadeia de caracteres de conexão do dispositivo, execute o comando a seguir usando a cadeia de conexão de dispositivo que você anotou anteriormente:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Certifique-se de incluir a cadeia de conexão dentro de aspas duplas.

Agora você instalou e configurou o runtime do IoT Edge em um dispositivo Linux. Posteriormente neste tutorial, você usará a solução de Monitoramento Remoto para implantar módulos do IoT Edge nesse dispositivo.

## <a name="create-an-edge-manifest"></a>Criar um manifesto do Edge

Para simular o dispositivo de bomba de extração de petróleo, você precisará adicionar os seguintes módulos ao dispositivo do Edge:

* Módulo de simulação de temperatura.
* Detecção de anomalias do Azure Stream Analytics.

As etapas a seguir mostram como criar um manifesto de implantação do Edge que inclui esses módulos. Mais tarde neste tutorial, você importará esse manifesto como um pacote no acelerador de solução de Monitoramento Remoto.

### <a name="create-the-azure-stream-analytics-job"></a>Criar o trabalho do Azure Stream Analytics

Você pode definir o trabalho do Stream Analytics no portal antes de empacotá-lo como um módulo do Edge.

1. No portal do Azure, crie uma conta de armazenamento do Azure usando as opções padrão do grupo de recursos **IoTEdgeDevices**. Anote o nome escolhido.

1. No portal do Azure, crie um **Trabalho do Stream Analytics** no grupo de recursos **IoTEdgeDevices**. Use os seguintes valores de configuração:

    | Opção | Valor |
    | ------ | ----- |
    | Nome do trabalho | EdgeDeviceJob |
    | Subscription | Sua assinatura do Azure |
    | Resource group | IoTEdgeDevices |
    | Localização | Leste dos EUA |
    | Ambiente de hospedagem | Microsoft Edge |
    | Unidades de transmissão | 1 |

1. Abra o trabalho do Stream Analytics **EdgeDeviceJob** no portal, clique em Entradas e adicione uma entrada de fluxo do **Hub do Edge** chamada **telemetry**.

1. No trabalho do Stream Analytics **EdgeDeviceJob** no portal, clique em **Saídas** e adicione uma saída do **Hub do Edge** chamada **output**.

1. No trabalho do Stream Analytics **EdgeDeviceJob** no portal, clique em **Saídas** e adicione uma segunda saída do **Hub do Edge** chamada **alert**.

1. No trabalho do Stream Analytics **EdgeDeviceJob** no portal, clique em **Consulta** e adicione a seguinte instrução **select**:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. No trabalho do Stream Analytics **EdgeDeviceJob** no portal, clique em **Configurações da conta de armazenamento**. Adicione a conta de armazenamento que foi adicionada ao grupo de recursos **IoTEdgeDevices** no início desta seção. Crie um novo contêiner chamado **edgeconfig**.

A captura de tela abaixo mostra o trabalho salvo do Stream Analytics:

[![Trabalho do Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Agora você definiu um trabalho do Stream Analytics para ser executado em seu dispositivo de borda. O trabalho calcula a temperatura média em um período de cinco segundos. O trabalho também envia um alerta se a temperatura média em um período de três segundos ficar acima de 400.

### <a name="create-the-iot-edge-deployment"></a>Criar a implantação do IoT Edge

Em seguida, crie um manifesto de implantação do IoT Edge que define os módulos que serão executados no dispositivo do Edge. Na próxima seção, você importará esse manifesto como um pacote na solução de Monitoramento Remoto.

1. No portal do Azure, navegue até o hub IoT em sua solução de Monitoramento Remoto. Você pode encontrar o hub IoT no grupo de recursos que tem o mesmo nome da solução de Monitoramento Remoto.

1. No hub IoT, clique em **IoT Edge** na seção **Gerenciamento automático de dispositivos**. Clique em **Adicionar uma implantação do IoT Edge**.

1. Na página **Criar Implantação > Nome e Rótulo**, insira o nome **oil-pump-device**. Clique em **Próximo**.

1. Na página **Criar Implantação > Adicionar Módulos**, clique em **+ Adicionar**. Escolha **Módulo do IoT Edge**.

1. No painel **Módulos personalizados do IoT Edge**, digite **temperatureSensor** para nome e **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** para URI da imagem. Clique em **Save** (Salvar).

1. Na página **Criar Implantação > Adicionar Módulos**, clique em **+ Adicionar** para adicionar um segundo módulo. Escolha **Módulo do Azure Stream Analytics**.

1. No painel **Implantação do Edge**, escolha sua assinatura e o **EdgeDeviceJob** criado na seção anterior. Clique em **Save** (Salvar).

1. Na página **Criar Implantação > Adicionar Módulos**, clique em **+ Avançar**.

1. Na página **Criar Implantação > Especificar Rotas**, adicione o seguinte código:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Esse código roteia a saída do módulo do Stream Analytics para os locais corretos.

    Clique em **Próximo**.

1. Na página **Criar Implantação > Especificar Métricas**, clique em **Avançar**.

1. Na página **Criar Implantação > Dispositivos de Destino**, insira 10 como a prioridade. Clique em **Próximo**.

1. Na página **Criar Implantação > Examinar Implantação**, clique em **Enviar**:

    [![Rever implantação](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Na página principal do **IoT Edge**, clique em **Implantações do IoT Edge**. É possível ver **oil-pump-device** na lista de implantações.

1. Clique na implantação **oil-pump-device** e clique em **Baixar manifesto do IoT Edge**. Salve o arquivo como **oil-pump-device.json** em um local adequado em seu computador local. Você precisará desse arquivo na próxima seção deste tutorial.

Você criou um manifesto do IoT Edge para importar para a solução de Monitoramento Remoto como um pacote. Normalmente, um desenvolvedor cria os módulos e o arquivo de manifesto do IoT Edge.

## <a name="import-a-package"></a>Importar um pacote

Nesta seção, você importará o manifesto do Edge como um pacote na solução de Monitoramento Remoto.

1. Na interface do usuário Web do Monitoramento Remoto, navegue até a página **Pacotes** e clique em **+ Novo Pacote**:

    [![Novo pacote](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. No painel **Novo Pacote**, escolha **Manifesto do Edge** como o tipo de pacote, clique em **Procurar** para localizar o arquivo **oil-pump-device.json** no computador local e clique em **Carregar**:

    [![Carregar pacote](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A lista de pacotes agora inclui o pacote **oil-pump-device.json**.

Na próxima seção, você criará uma implantação que aplica o pacote ao dispositivo do Edge.

## <a name="deploy-a-package"></a>Implantar um pacote

Agora você está pronto para implantar o pacote em seu dispositivo.

1. Na interface de usuário Web do Monitoramento Remoto, navegue até a página **Implantações** e clique em **+ Nova implantação**:

    [![Nova implantação](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. No painel **Nova implantação**, crie uma implantação com as seguintes configurações:

    | Opção | Valor |
    | ------ | ----- |
    | Nome   | OilPumpDevices |
    | Tipo de pacote | Manifesto do Edge |
    | Pacote | oil-pump-device.json |
    | Grupo de dispositivos | OilPumps |
    | Prioridade | 10 |

    [![Criar implantação](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Clique em **Aplicar**.

Você precisará aguardar alguns minutos para o pacote ser implantado em seu dispositivo e para a telemetria começar a fluir do dispositivo.

[![Implantação ativa](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

A página **Implantações** mostra as seguintes métricas:

* **Destino** mostra o número de dispositivos no grupo de dispositivos.
* **Aplicado** mostra o número de dispositivos que tiveram o conteúdo da implantação aplicado.
* **Êxito** mostra a quantidade de dispositivos do Edge com êxito no relatório de implantação do runtime do cliente IoT Edge.
* **Falha** mostra a quantidade de dispositivos do Edge com falha no relatório de implantação do runtime do cliente IoT Edge.

## <a name="monitor-the-device"></a>Monitorar o dispositivo

Você pode exibir a telemetria de temperatura de seu dispositivo de bomba de extração de petróleo na interface do usuário Web do Monitoramento Remoto:

1. Navegue até a página **Device Explorer** e escolha seu dispositivo de bomba de extração.
1. Na seção **Telemetria** do painel **Detalhes do dispositivo**, clique em **Temperatura**:

    [![Exibir telemetria](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Veja como a temperatura sobe até atingir um limite. O módulo do Edge do Stream Analytics detecta quando a temperatura alcança esse limite e envia um comando para o dispositivo para reduzir a temperatura imediatamente. Todo esse processamento ocorre no dispositivo sem comunicação com a nuvem.

Se você quiser notificar os operadores quando o limite for atingido, é possível criar uma regra na interface do usuário Web do Monitoramento Remoto:

1. Navegue até a página **Regras** e clique em **+ Nova regra**.
1. Crie uma nova regra com as seguintes configurações:

    | Opção | Valor |
    | ------ | ----- |
    | Nome da regra | Temperatura da bomba de extração |
    | Descrição | Temperatura da bomba de extração ultrapassou 300 |
    | Grupo de dispositivos | OilPumps |
    | Cálculo | Instantâneo |
    | Campo | temperatura |
    | Operador | > |
    | Valor | 300 |
    | Nível de severidade | Informações |

    [![Criar regra](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Clique em **Aplicar**.

1. Navegue até a página **Painel**. Um alerta aparece no painel **Alertas** quando a temperatura no dispositivo **oil-pump** ultrapassa 300.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como adicionar e configurar um dispositivo do IoT Edge no acelerador de solução de Monitoramento Remoto. Para saber mais sobre como trabalhar com pacotes do IoT Edge da solução de Monitoramento Remoto, confira o guia de instruções abaixo:

> [!div class="nextstepaction"]
> [Importe um pacote IoT Edge para o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-import-edge-package.md)

Para saber mais sobre como instalar o runtime do IoT Edge, confira [Instalar o runtime do Azure IoT Edge no Linux (x64)](../iot-edge/how-to-install-iot-edge.md).

Para saber mais sobre o Azure Stream Analytics em dispositivos do Edge, confira [Implantar o Azure Stream Analytics como um módulo do IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).