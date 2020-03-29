---
title: Tutorial - Use enriquecimentos de mensagens do Azure IoT Hub
description: Tutorial mostrando como usar enriquecimentos de mensagens para mensagens do Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: c812e00699cd8f8cfbaf32feea1b43866ffb0990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674365"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Tutorial: Use enriquecimentos de mensagens do Azure IoT Hub

*Os enriquecimentos* de mensagens descrevem a capacidade do Azure IoT Hub de *carimbar* mensagens com informações adicionais antes que as mensagens sejam enviadas para o ponto final designado. Uma das razões para usar enriquecimentos de mensagens é incluir dados que podem ser usados para simplificar o processamento a jusante. Por exemplo, enriquecer mensagens de telemetria de dispositivos com uma tag dupla de dispositivo pode reduzir a carga nos clientes para fazer chamadas de API gêmeas para essas informações. Para obter mais informações, consulte [Visão geral dos enriquecimentos de mensagens](iot-hub-message-enrichments-overview.md).

Neste tutorial, você vê duas maneiras de criar e configurar os recursos necessários para testar os enriquecimentos de mensagens para um hub de IoT. Os recursos incluem uma conta de armazenamento com dois contêineres de armazenamento. Um contêiner contém as mensagens enriquecidas, e outro contêiner contém as mensagens originais. Também está incluído um hub de IoT para receber as mensagens e encaminhá-las para o contêiner de armazenamento apropriado com base em se elas são enriquecidas ou não.

* O primeiro método é usar o Azure CLI para criar os recursos e configurar o roteamento de mensagens. Em seguida, você define os enriquecimentos manualmente usando o [portal Azure](https://portal.azure.com).

* O segundo método é usar um modelo do Azure Resource Manager para criar os recursos *e* as configurações para o roteamento de mensagens e enriquecimentos de mensagens.

Depois que as configurações para o roteamento de mensagens e enriquecimentos de mensagens são concluídas, você usa um aplicativo para enviar mensagens para o hub IoT. O hub então os encaminha para ambos os contêineres de armazenamento. Apenas as mensagens enviadas ao ponto final para o recipiente de armazenamento **enriquecido** são enriquecidas.

Aqui estão as tarefas que você executa para completar este tutorial:

**Use enriquecimentos de mensagens do IoT Hub**
> [!div class="checklist"]
> * Primeiro método: Criar recursos e configurar o roteamento de mensagens usando o Cli do Azure. Configure os enriquecimentos de mensagens manualmente usando o [portal Azure](https://portal.azure.com).
> * Segundo método: Criar recursos e configurar o roteamento de mensagens e enriquecimentos de mensagens usando um modelo de Gerenciador de recursos. 
> * Execute um aplicativo que simula um dispositivo IoT enviando mensagens para o hub.
> * Veja os resultados e verifique se os enriquecimentos de mensagens estão funcionando como esperado.

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Instale [o Visual Studio](https://www.visualstudio.com/).

* Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste tutorial usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperar o repositório de amostras IoT C#

Baixe as [amostras de IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) do GitHub e descompacte-as. Este repositório tem vários aplicativos, scripts e modelos do Gerenciador de recursos nele. Os que serão usados para este tutorial são os seguintes:

* Para o método manual, há um script CLI que é usado para criar os recursos. Este script está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Esse script cria os recursos e configura o roteamento da mensagem. Depois de executar este script, crie os enriquecimentos de mensagens manualmente usando o [portal Azure](https://portal.azure.com).
* Para o método automatizado, há um modelo do Azure Resource Manager. O modelo está em /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Esse modelo cria os recursos, configura o roteamento de mensagens e, em seguida, configura os enriquecimentos de mensagens.
* O terceiro aplicativo que você usa é o aplicativo Desimulação de Dispositivos, que você usa para enviar mensagens para o hub ioT e testar os enriquecimentos de mensagens.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configurar e configurar manualmente usando o Azure CLI

Além de criar os recursos necessários, o script Azure CLI também configura as duas rotas para os pontos finais que são contêineres de armazenamento separados. Para obter mais informações sobre como configurar o roteamento da mensagem, consulte o [tutorial de roteamento](tutorial-routing.md). Depois que os recursos forem configurados, use o [portal Azure](https://portal.azure.com) para configurar enriquecimentos de mensagens para cada ponto final. Em seguida, continue a etapa de teste.

> [!NOTE]
> Todas as mensagens são encaminhadas para ambos os pontos finais, mas apenas as mensagens que vão para o ponto final com enriquecimentos de mensagens configurados serão enriquecidas.
>

Você pode usar o script a seguir ou abrir o script na pasta /resources do repositório baixado. O script executa as seguintes etapas:

* Crie um Hub IoT.
* Criar uma conta de armazenamento.
* Crie dois contêineres na conta de armazenamento. Um contêiner é para as mensagens enriquecidas, e outro contêiner é para mensagens que não são enriquecidas.
* Configurar o roteamento para as duas contas de armazenamento diferentes:
    * Crie um ponto final para cada contêiner de conta de armazenamento.
    * Crie uma rota para cada um dos pontos finais do contêiner da conta de armazenamento.

Existem vários nomes de recursos que devem ser globalmente únicos, como o nome do hub ioT e o nome da conta de armazenamento. Para facilitar a execução do script, esses nomes de recursos são anexados com um valor alfanumérico aleatório chamado *randomValue*. O valor aleatório é gerado uma vez na parte superior do script. É anexado aos nomes de recursos conforme necessário ao longo do script. Se você não quiser que o valor seja aleatório, você pode defini-lo como uma seqüência de string vazia ou para um valor específico.

Se você ainda não fez isso, abra uma janela do Azure [Cloud Shell](https://shell.azure.com) e certifique-se de que está configurada para Bash. Abra o script no repositório sem zíper, selecione Ctrl+A para selecionar tudo e selecione Ctrl+C para copiá-lo. Alternativamente, você pode copiar o seguinte script CLI ou abri-lo diretamente no Cloud Shell. Cole o script na janela Cloud Shell clicando com o botão direito do mouse na linha de comando e selecionando **Colar**. O roteiro executa uma declaração de cada vez. Depois que o script parar de ser executado, **selecione Enter** para garantir que ele execute o último comando. O bloco de código a seguir mostra o script usado, com comentários que explicam o que está fazendo.

Aqui estão os recursos criados pelo script. *Enriquecido* significa que o recurso é para mensagens com enriquecimentos. *Original* significa que o recurso é para mensagens que não são enriquecidas.

| Nome | Valor |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome do contêiner | original  |
| nome do contêiner | Enriquecido  |
| Nome do dispositivo IoT | Dispositivo de teste contoso |
| Nome do Hub IoT | ContosoTestHubMsgEn |
| Nome da conta de armazenamento | contosoarmazenamento |
| nome do ponto final 1 | ContosoStorageEndpointOriginal |
| nome do ponto final 2 | ContosoStorageEndpointEnriqueed|
| nome da rota 1 | ContosoStorageRouteOriginal |
| rota Nome 2 | ContosoStorageRouteEnriqueed |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Neste ponto, os recursos estão todos configurados e o roteamento da mensagem é configurado. Você pode visualizar a configuração de roteamento de mensagens no portal e configurar os enriquecimentos de mensagens para mensagens que vão para o contêiner de armazenamento **enriquecido.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configure manualmente os enriquecimentos de mensagens usando o portal Azure

1. Vá para o seu hub de IoT selecionando **grupos de recursos**. Em seguida, selecione o grupo de recursos configurado para este tutorial **(ContosoResourcesMsgEn**). Encontre o hub de IoT na lista e selecione-o. Selecione **roteamento de mensagens** para o hub IoT.

   ![Selecione roteamento de mensagens](./media/tutorial-message-enrichments/select-iot-hub.png)

   O painel de roteamento de mensagens tem três guias rotuladas **de Rotas,** **Pontos finais personalizados**e **mensagens Enriquecer**. Navegue pelas duas primeiras abas para ver a configuração configurada pelo script. Use a terceira guia para adicionar enriquecimentos de mensagens. Vamos enriquecer as mensagens indo para o ponto final para o recipiente de armazenamento chamado **enriquecido**. Preencha o nome e o valor e selecione o ponto final **ContosoStorageEndpointEnriqueed** na lista suspensa. Aqui está um exemplo de como configurar um enriquecimento que adiciona o nome do hub ioT à mensagem:

   ![Adicione o primeiro enriquecimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Adicione esses valores à lista do ponto final ContosoStorageEndpointEnriched.

   | Chave | Valor | Ponto final (lista de baixa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriqueed |
   | Localização do dispositivo | $twin.tags.localização | AzureStorageContainers > ContosoStorageEndpointEnriqueed |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriqueed |

   > [!NOTE]
   > Se o seu dispositivo não tiver um gêmeo, o valor que você colocar aqui será carimbado como uma string para o valor nos enriquecimentos de mensagens. Para ver as informações duplas do dispositivo, vá para o seu hub no portal e selecione **dispositivos IoT**. Selecione seu dispositivo e, em seguida, selecione **Dispositivo gêmeo** na parte superior da página.
   >
   > Você pode editar as informações gêmeas para adicionar tags, como localização, e defini-la como um valor específico. Para obter mais informações, consulte [Entender e usar gêmeos de dispositivos no IoT Hub](iot-hub-devguide-device-twins.md).

3. Quando você terminar, seu painel deve ser semelhante a esta imagem:

   ![Tabela com todos os enriquecimentos adicionados](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecione **Aplicar** para salvar as alterações. Pule para a seção [de enriquecimento de mensagens de teste.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Criar e configurar usando um modelo de Gerenciador de recursos
Você pode usar um modelo de Gerenciador de recursos para criar e configurar os recursos, roteamento de mensagens e enriquecimentos de mensagens.

1. Entre no portal do Azure. Selecione **+ Crie um recurso** para criar uma caixa de pesquisa. Digite *a implantação do modelo*e pesquise por ele. No painel de resultados, selecione **'Modelo' de implantação (implante usando modelo personalizado)**.

   ![Implantação de modelo sustamno no portal Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecione **Criar** no painel **de implantação Modelo.**

1. No **painel de implantação Personalizado,** **selecione Construa seu próprio modelo no editor**.

1. No painel **Editar modelo,** selecione **''Editar', ''Editar', ''Editar', ''Editar'.** O Windows Explorer é exibido. Localize o arquivo **template_messageenrichments.json** no arquivo de repo descompactado em **/iot-hub/Tutorials/Roteamento/SimuladoDevice/resources**. 

   ![Selecione o modelo da máquina local](./media/tutorial-message-enrichments/template-select.png)

1. Selecione **Abrir** para carregar o arquivo de modelo da máquina local. Ele carrega e aparece no painel de edição.

   Este modelo é configurado para usar um nome de hub de IoT globalmente único e nome da conta de armazenamento, adicionando um valor aleatório ao final dos nomes padrão, para que você possa usar o modelo sem fazer nenhuma alteração nele.

   Aqui estão os recursos criados carregando o modelo. **Enriquecido** significa que o recurso é para mensagens com enriquecimentos. **Original** significa que o recurso é para mensagens que não são enriquecidas. Estes são os mesmos valores usados no script Azure CLI.

   | Nome | Valor |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome do contêiner | original  |
   | nome do contêiner | Enriquecido  |
   | Nome do dispositivo IoT | Dispositivo de teste contoso |
   | Nome do Hub IoT | ContosoTestHubMsgEn |
   | Nome da conta de armazenamento | contosoarmazenamento |
   | nome do ponto final 1 | ContosoStorageEndpointOriginal |
   | nome do ponto final 2 | ContosoStorageEndpointEnriqueed|
   | nome da rota 1 | ContosoStorageRouteOriginal |
   | rota Nome 2 | ContosoStorageRouteEnriqueed |

1. Selecione **Salvar**. O painel **de implantação Personalizado** aparece e mostra todos os parâmetros usados pelo modelo. O único campo que você precisa definir é **o grupo Resource**. Crie um novo ou selecione um na lista de paradas.

   Aqui está a metade superior do painel de **implantação personalizado.** Você pode ver onde você preenche o grupo de recursos.

   ![Metade superior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Aqui está a metade inferior do painel de **implantação personalizado.** Você pode ver o resto dos parâmetros e os termos e condições. 

   ![Metade inferior do painel de implantação personalizado](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Selecione a caixa de seleção para concordar com os termos e condições. Em seguida, **selecione Comprar** para continuar com a implantação do modelo.

1. Aguarde que o modelo seja totalmente implantado. Selecione o ícone do sino na parte superior da tela para verificar o progresso. Quando estiver terminado, continue para a seção [de enriquecimento de mensagens de teste.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Enriquecimento de mensagens de teste

Para visualizar os enriquecimentos de mensagens, selecione **grupos de recursos**. Em seguida, selecione o grupo de recursos que você está usando para este tutorial. Selecione o hub de IoT na lista de recursos e vá para **Mensagens**. A configuração de roteamento de mensagens e os enriquecimentos configurados aparecem.

Agora que os enriquecimentos de mensagens estão configurados para o ponto final, execute o aplicativo Dispositivo Simulado para enviar mensagens para o hub IoT. O hub foi configurado com configurações que realizam as seguintes tarefas:

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointOriginal não `original`serão enriquecidas e serão armazenadas no recipiente de armazenamento .

* As mensagens encaminhadas para o ponto final de armazenamento ContosoStorageEndpointEnriqueed serão enriquecidas e armazenadas no recipiente `enriched`de armazenamento .

O aplicativo Dispositivo Simulado é um dos aplicativos no download sem zíper. O aplicativo envia mensagens para cada um dos diferentes métodos de roteamento de mensagens no [tutorial De roteamento](tutorial-routing.md), que inclui o Azure Storage.

Clique duas vezes no arquivo de solução **IoT_SimulatedDevice.sln** para abrir o código no Visual Studio e, em seguida, abra **Program.cs**. Substitua o nome do hub `{your hub name}`IoT pelo marcador . O formato do nome do host do hub IoT é **{seu nome de hub}.azure-devices.net**. Para este tutorial, o nome do host do hub é ContosoTestHubMsgEn.azure-devices.net. Em seguida, substitua a chave do dispositivo salva anteriormente `{your device key}`quando executou o script para criar os recursos para o marcador .

Se você não tiver a chave do dispositivo, você pode recuperá-la do portal. Depois de fazer login, vá para **grupos de recursos,** selecione seu grupo de recursos e selecione seu hub de IoT. Procure em **Dispositivos IoT** para o seu dispositivo de teste e selecione o seu dispositivo. Selecione o ícone de cópia ao lado **da tecla Principal** para copiá-lo à área de transferência.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Executar e testar

Execute o aplicativo do console por alguns minutos. As mensagens que estão sendo enviadas são exibidas na tela do console do aplicativo.

Esse aplicativo envia uma nova mensagem de dispositivo para a nuvem ao Hub IoT a cada segundo. A mensagem contém um objeto serializado para JSON com a ID do dispositivo, temperatura, umidade e nível de mensagem, cujo padrão é `normal`. Ele atribui aleatoriamente um `critical` `storage`nível de ou , o que faz com que a mensagem seja roteada para a conta de armazenamento ou para o ponto final padrão. As mensagens enviadas para o contêiner **enriquecido** na conta de armazenamento serão enriquecidas.

Depois que várias mensagens de armazenamento forem enviadas, visualize os dados.

1. Selecione **Grupos de recursos**. Encontre seu grupo de recursos, **ContosoResourcesMsgEn,** e selecione-o.

2. Selecione sua conta de armazenamento, que é **contosostorage**. Em seguida, selecione **'Explorador de armazenamento' (visualização)** no painel esquerdo.

   ![Selecione o Explorador de Armazenamento](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecione **Recipientes BLOB** para ver os dois recipientes que podem ser usados.

   ![Veja os recipientes na conta de armazenamento](./media/tutorial-message-enrichments/show-blob-containers.png)

As mensagens no contêiner **denominada enriquecida** têm os enriquecimentos de mensagens incluídos nas mensagens. As mensagens no contêiner chamada **original** têm as mensagens cruas sem enriquecimentos. Aprofunde-se em um dos recipientes até chegar ao fundo e abra o arquivo de mensagem mais recente. Em seguida, faça o mesmo para o outro contêiner para verificar se não há enriquecimentos adicionados às mensagens naquele contêiner.

Quando você olha para as mensagens que foram enriquecidas, você deve ver "meu IoT Hub" com o nome do hub e a localização e o ID do cliente, assim:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Aqui está uma mensagem não enriquecida. Observe que "meu IoT Hub", "localização de dispositivos" e "customerID" não aparecem aqui porque esses campos são adicionados pelos enriquecimentos. Este ponto final não tem enriquecimentos.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados neste tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ela remove o hub IoT, a conta de armazenamento e o próprio grupo de recursos.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usar a CLI do Azure para limpar os recursos

Para remover o grupo de recursos, use o comando [excluir grupo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Lembre-se que `$resourceGroup` foi definido para **ContosoResourcesMsgEn** no início deste tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e testou adicionar enriquecimentos de mensagens às mensagens do IoT Hub usando as seguintes etapas:

**Use enriquecimentos de mensagens do IoT Hub**
> [!div class="checklist"]
> * Primeiro método: Criar recursos e configurar o roteamento de mensagens usando o Cli do Azure. Configure os enriquecimentos de mensagens manualmente usando o [portal Azure](https://portal.azure.com).
> * Segundo método: Criar recursos e configurar o roteamento de mensagens e enriquecimentos de mensagens usando um modelo do Azure Resource Manager.
> * Execute um aplicativo que simula um dispositivo IoT enviando mensagens para o hub.
> * Veja os resultados e verifique se os enriquecimentos de mensagens estão funcionando como esperado.

Para obter mais informações sobre enriquecimentos de mensagens, consulte [Visão Geral dos enriquecimentos de mensagens](iot-hub-message-enrichments-overview.md).

Para obter mais informações sobre o roteamento de mensagens, consulte estes artigos:

* [Use o roteamento de mensagens do IoT Hub para enviar mensagens de dispositivo para nuvem para diferentes pontos finais](iot-hub-devguide-messages-d2c.md)
* [Tutorial: Roteamento do IoT Hub](tutorial-routing.md)
