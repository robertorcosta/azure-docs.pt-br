---
title: Configurar e usar métricas e logs de diagnóstico com um hub IoT do Azure
description: Saiba como configurar e usar métricas e logs de diagnóstico com um hub IoT do Azure. Isso fornecerá dados a serem analisados para ajudar a diagnosticar problemas que seu hub pode ter.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 7ce56237631b858347a99c310751d4f2c1506e10
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108227"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Tutorial: Configurar e usar métricas e logs de diagnóstico com um hub IoT

Caso você tenha uma solução do Hub IoT executada em produção, é recomendável configurar algumas métricas e habilitar os logs de diagnóstico. Assim, se ocorrer um problema, você terá dados para examinar, que ajudarão você a diagnosticar o problema e corrigi-lo mais rapidamente. Neste artigo, você verá como habilitar os logs de diagnóstico e como verificá-los em busca de erros. Você também configurará algumas métricas para inspeção e alertas que são disparados quando a métrica atinge determinado limite. Por exemplo, você pode receber um email quando o número de mensagens de telemetria enviadas excede um limite específico ou quando o número de mensagens usadas se aproxima da cota de mensagens permitidas por dia para o Hub IoT. 

Um caso de uso de exemplo é um posto de gasolina cujas bombas são dispositivos IoT que enviam mensagens e se comunicam com um hub IoT. Cartões de crédito são validados e a transação final é gravada em um armazenamento de dados. Se os dispositivos IoT param de se conectar ao hub e de enviar mensagens, é muito mais difícil corrigir esse problema se você não tem nenhuma visibilidade do que está acontecendo.

Este tutorial usa a amostra do Azure do [Roteamento do Hub IoT](tutorial-routing.md) para enviar mensagens ao hub IoT.

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
> * Usando a CLI do Azure, criar um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Habilitar os logs de diagnóstico.
> * Habilitar as métricas.
> * Configurar alertas para essas métricas. 
> * Baixar e executar um aplicativo que simula um dispositivo IoT enviando mensagens para o hub. 
> * Executar o aplicativo até que os alertas comecem a ser disparados. 
> * Ver os resultados das métricas e verificar os logs de diagnóstico. 

## <a name="prerequisites"></a>Prerequisites

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instalar o [Visual Studio](https://www.visualstudio.com/). 

- Uma conta de email que possa receber emails.

- Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste tutorial usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, você precisará de um hub IoT, uma conta de armazenamento e um dispositivo IoT simulado. Esses recursos podem ser criados usando a CLI do Azure ou o Azure PowerShell. Use o mesmo grupo de recursos e o local para todos os recursos. Em seguida, no final, você pode remover tudo em uma única etapa, excluindo o grupo de recursos.

Estas são as etapas necessárias.

1. Crie um [grupo de recursos](../azure-resource-manager/management/overview.md). 

2. Crie um Hub IoT.

3. Crie uma conta de armazenamento padrão V1 com replicação Standard_LRS.

4. Crie uma identidade de dispositivo para o dispositivo simulado que envia mensagens para o hub. Salve a chave para a fase de teste.

### <a name="set-up-resources-using-azure-cli"></a>Configurar recursos usando a CLI do Azure

Copie e cole o script no Cloud Shell. Supondo que você já esteja conectado, ele executa o script uma linha de cada vez. Os novos recursos são criados no grupo de recursos ContosoResources.

As variáveis que devem ser globalmente exclusivas têm `$RANDOM` concatenados a elas. Quando o script é executado e as variáveis são definidas, uma cadeia de caracteres numérica aleatória é gerada e concatenada ao final da cadeia de caracteres fixa, tornando-a exclusiva.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Ao criar a identidade do dispositivo, você poderá receber o seguinte erro: *Nenhuma chave encontrada para a política iothubowner do Hub IoT ContosoTestHub*. Para corrigir esse erro, atualize a Extensão de IoT da CLI do Azure e, em seguida, execute os dois últimos comandos no script novamente. 
>
>Veja a seguir o comando para atualizar a extensão. Execute isso na instância do Cloud Shell.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Habilitar os logs de diagnóstico 

Os [logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) estão desabilitados por padrão quando você cria um hub IoT. Nesta seção, habilite os logs de diagnóstico para o seu hub.

1. Primeiro, caso ainda não esteja em seu hub no portal, clique em **Grupos de recursos** e no grupo de recursos Contoso-Resources. Selecione o hub na lista de recursos exibidos. 

2. Procure a seção **Monitoramento** na folha Hub IoT. Clique em **Configurações do Diagnóstico**. 

   ![Captura de tela mostrando a parte de configurações de diagnóstico da folha Hub IoT.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Verifique se a assinatura e o grupo de recursos estão corretos. Em **Tipo de Recurso**, desmarque a opção **Selecionar Tudo** e, em seguida, procure e marque **Hub IoT**. (A marca de seleção é colocada ao lado de *Selecionar Tudo* novamente; basta ignorá-la.) Em **Recurso**, selecione o nome do hub. Sua tela deverá ser semelhante a esta imagem: 

   ![Captura de tela mostrando a parte de configurações de diagnóstico da folha Hub IoT.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Agora clique em **Ativar diagnóstico**. O painel Configurações de diagnóstico será exibido. Especifique o nome das configurações de logs de diagnóstico como "diags-hub".

5. Marque **Arquivar em uma conta de armazenamento**. 

   ![Captura de tela mostrando a configuração de diagnóstico a ser arquivada em uma conta de armazenamento.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Clique em **Configurar** para ver a tela **Selecionar uma conta de armazenamento**, selecione a correta (*contosostoragemon*) e clique em **OK** para retornar ao painel Configurações de diagnóstico. 

   ![Captura de tela mostrando a configuração de logs de diagnóstico a ser arquivada em uma conta de armazenamento.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Em **LOG**, marque **Conexões** e **Telemetria do Dispositivo** e defina a **Retenção (dias)** como 7 dias para cada um. A tela Configurações de diagnóstico agora deverá ser semelhante a esta imagem:

   ![Captura de tela mostrando as configurações finais de log de diagnóstico.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Clique em **Salvar** para salvar as configurações. Feche o painel Configurações de diagnóstico.

Mais tarde, quando você examinar os logs de diagnóstico, poderá ver o log de conexão e desconexão do dispositivo. 

## <a name="set-up-metrics"></a>Configurar métricas 

Agora configure algumas métricas para inspeção quando forem enviadas mensagens para o hub. 

1. No painel de configurações do hub IoT, clique na opção **Métricas** na seção **Monitoramento**.

2. Na parte superior da tela, clique em **Últimas 24 horas (Automático)** . Na lista suspensa exibida, selecione **Últimas 4 horas** para **Intervalo de Tempo** e defina **Granularidade de Tempo** para **1 minuto**, na hora local. Clique em **Aplicar** para salvar essas configurações. 

   ![Captura de tela mostrando as configurações de hora das métricas.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Há uma entrada de métrica por padrão. Deixe o grupo de recursos como o padrão e o namespace da métrica. Na lista suspensa **Métrica**, selecione **Mensagens de telemetria enviadas**. Defina **Agregação** como **Soma**.

   ![Captura de tela mostrando a adição de uma métrica para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Agora clique em **Adicionar métrica** para adicionar outra métrica ao gráfico. Selecione o grupo de recursos (**ContosoTestHub**). Em **Métrica**, selecione **Número total de mensagens usadas**. Para **Agregação**, selecione **Méd**. 

   Agora a tela mostra a métrica minimizada para *Mensagens de telemetria enviadas*, além da nova métrica para *Número total de mensagens usadas*.

   ![Captura de tela mostrando a adição de uma métrica para mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Clique em **Fixar no painel**. Isso a fixará no painel do portal do Azure para que você possa acessá-la novamente. Se você não a fixar no painel, as configurações não serão mantidas.

## <a name="set-up-alerts"></a>Configurar alertas

Acesse o hub no portal. Clique em **Grupos de Recursos**, selecione *ContosoResources* e, em seguida, o Hub IoT *ContosoTestHub*. 

O Hub IoT ainda não foi migrado para as [métricas no Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics); você precisará usar [alertas clássicos](/azure/azure-monitor/platform/alerts-classic.overview).

1. Em **Monitoramento**, clique em **Alertas** Isso mostrará a tela principal de alertas. 

   ![Captura de tela mostrando como encontrar alertas clássicos.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Para acessar os alertas clássicos aqui, clique em **Exibir alertas clássicos**. 

    ![Captura de tela mostrando a tela de alertas clássicos.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Preencha os campos: 

    **Assinatura**: Deixe esse campo definido como a assinatura atual.

    **Fonte**: Defina esse campo como *Métricas*.

    **Grupo de recursos**: Defina esse campo como o grupo de recursos atual, *ContosoResources*. 

    **Tipo de recurso**: Defina esse campo como Hub IoT. 

    **Recurso**: Selecione o hub IoT, *ContosoTestHub*.

3. Clique em **Adicionar alerta de métrica (clássico)** para configurar um novo alerta.

    Preencha os campos:

    **Name**: Forneça um nome para a regra de alerta, como *mensagens de telemetria*.

    **Descrição**: Forneça uma descrição do alerta, como *alertar quando houver 1.000 mensagens de telemetria enviadas*. 

    **Fonte**: Defina esse campo como *Métricas*.

    **Assinatura**, **Grupo de Recursos** e **Recurso** devem ser definidos com os valores selecionados na tela **Exibir alertas clássicos**. 

    Defina **Métrica** como *Mensagens de telemetria enviadas*.

    ![Captura de tela mostrando como configurar um alerta clássico para as mensagens de telemetria enviadas.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Após o gráfico, defina os seguintes campos:

   **Condição**: Defina esse campo como *Maior que*.

   **Limite**: Defina esse campo como 1.000.

   **Período**: Defina esse campo como *Nos últimos 5 minutos*.

   **Destinatários do email de notificação**: Coloque seu endereço de email aqui. 

   ![Captura de tela mostrando a metade inferior da tela de alertas.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Clique em **OK** para salvar o alerta. 

5. Agora configure outro alerta para o *Número total de mensagens usadas*. Essa métrica é útil se você deseja enviar um alerta quando o número de mensagens usadas se aproximar da cota do hub IoT – para que você saiba que o hub começará a rejeitar mensagens em breve.

   Na tela **Exibir alertas clássicos**, clique em **Adicionar alerta de métrica (clássico)** e, em seguida, preencha esses campos no painel **Adicionar regra**.

   **Name**: Forneça um nome para a regra de alerta, como *número de mensagens usadas*.

   **Descrição**: Forneça uma descrição do alerta, como *alertar quando se aproximar da cota*.

   **Fonte**: Defina esse campo como *Métricas*.

    **Assinatura**, **Grupo de Recursos** e **Recurso** devem ser definidos com os valores selecionados na tela **Exibir alertas clássicos**. 

    Defina **Métrica** como *Número total de mensagens usadas*.

6. Abaixo do gráfico, preencha os seguintes campos:

   **Condição**: Defina esse campo como *Maior que*.

   **Limite**: Defina esse campo como 1.000.

   **Período**: Defina esse campo como *Nos últimos 5 minutos*. 

   **Destinatários do email de notificação**: Coloque seu endereço de email aqui. 

   Clique em **OK** para salvar a regra. 

5. Agora você deverá ver dois alertas no painel de alertas clássicos: 

   ![Captura de tela mostrando a tela de alertas clássicos com as novas regras de alerta.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Feche o painel de alertas. 
    
    Com essas configurações, você receberá um alerta quando o número de mensagens enviadas for maior que 400 e quando o número total de mensagens usadas exceder o NÚMERO.

## <a name="run-simulated-device-app"></a>Executar um aplicativo de dispositivo simulado

Anteriormente na seção de configuração do script, você configurou um dispositivo para simular usando um dispositivo de IoT. Nesta seção, você baixa um aplicativo do console do .NET que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.  

Baixe a solução para a [Simulação de dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Esse link baixa um repositório contendo vários aplicativos. A solução que você está procurando está em iot-hub/Tutorials/Routing/.

Clique duas vezes no arquivo de solução (SimulatedDevice.sln) para abrir o código no Visual Studio, em seguida, abra o Program.cs. Substitua `{iot hub hostname}` pelo nome de host do Hub IoT. O formato de nome de host do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome de host do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` com a chave do dispositivo salvo anteriormente durante a configuração do dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar 

Em Program.cs, altere o `Task.Delay` de 1.000 para 10, o que reduz o tempo entre o envio de mensagens de 1 para 0,01 segundo. A redução desse atraso aumenta o número de mensagens enviadas.

```csharp
await Task.Delay(10);
```

Execute o aplicativo de console. Aguarde alguns minutos (10 a 15). Você poderá ver as mensagens sendo enviadas do dispositivo simulado ao hub na tela do console do aplicativo.

### <a name="see-the-metrics-in-the-portal"></a>Ver as métricas no portal

Abra as métricas no Painel. Altere os valores temporais para *Últimos 30 minutos* com uma granularidade de tempo de *1 minuto*. Isso mostrará as mensagens de telemetria enviadas e o número total de mensagens usadas no gráfico, com os números mais recentes na parte inferior do gráfico.

   ![Captura de tela mostrando as métricas.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Ver os alertas

Volte para os alertas. Clique em **Grupos de recursos**, selecione *ContosoResources* e, em seguida, o hub *ContosoTestHub*. Na página de propriedades exibida para o hub, selecione **Alertas** e, em seguida, **Exibir alertas clássicos**. 

Quando o número de mensagens enviadas exceder o limite, você começará a receber alertas por email. Para ver se há alertas ativos, acesse o hub e selecione **Alertas**. Isso mostrará os alertas ativos e se há avisos. 

   ![Captura de tela mostrando que os alertas foram disparados.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Clique no alerta para obter as mensagens de telemetria. Isso mostrará o resultado da métrica e um gráfico com os resultados. Além disso, o email enviado para avisá-lo sobre o disparo do alerta é semelhante a esta imagem:

   ![Captura de tela do email mostrando que os alertas foram disparados.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Ver os logs de diagnóstico

Configure os logs de diagnóstico para que sejam exportados para o armazenamento de blobs. Acesse seu grupo de recursos e selecione sua conta de armazenamento *contosostoragemon*. Selecione Blobs e, em seguida, abra o contêiner *insights-logs-connections*. Faça uma busca detalhada até chegar à data atual e selecione o arquivo mais recente. 

   ![Captura de tela da busca detalhada no contêiner de armazenamento para ver os logs de diagnóstico.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Clique em **Baixar** para baixá-lo e abri-lo. Você verá os logs do dispositivo se conectando e desconectando enquanto ele envia mensagens ao hub. Veja uma amostra:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Limpar os recursos 

Para remover todos os recursos que você criou neste tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ela remove o hub IoT, a conta de armazenamento e o próprio grupo de recursos. Se você tiver fixado métricas no painel, precisará removê-las manualmente clicando nos três pontos no canto superior direito de cada uma delas e selecionando **Remover**.

Para remover o grupo de recursos, use o comando [excluir grupo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar métricas e logs de diagnóstico executando as seguintes tarefas:

> [!div class="checklist"]
> * Usando a CLI do Azure, criar um hub IoT, um dispositivo simulado e uma conta de armazenamento.  
> * Habilitar os logs de diagnóstico. 
> * Habilitar as métricas.
> * Configurar alertas para essas métricas. 
> * Baixar e executar um aplicativo que simula um dispositivo IoT enviando mensagens para o hub. 
> * Executar o aplicativo até que os alertas comecem a ser disparados. 
> * Ver os resultados das métricas e verificar os logs de diagnóstico. 

Avance para o próximo tutorial para aprender a gerenciar o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
> [Configurar seus dispositivos de um serviço de back-end](tutorial-device-twins.md)