---
title: Como enviar eventos do Azure SignalR Service para event grid
description: Um guia para mostrar como ativar eventos da Event Grid para o seu Serviço SignalR e, em seguida, enviar eventos conectados/desconectados de conexão ao cliente para um aplicativo de exemplo.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710823"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Como enviar eventos do Serviço do Azure SignalR para a Grade de Eventos

O Azure Event Grid é um serviço de roteamento de eventos totalmente gerenciado que fornece consumo uniforme de eventos usando um modelo pub-sub. Neste guia, você usa o Azure CLI para criar um Serviço De Sinalização Azure, assinar eventos de conexão e, em seguida, implantar um aplicativo web de exemplo para receber os eventos. Finalmente, você pode conectar e desconectar e ver a carga útil do evento no aplicativo de amostra.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos da CLI do Azure neste artigo são formatados para o shell de **Bash**. Se você estiver usando um shell diferente, como PowerShell ou Prompt de Comando, talvez seja necessário ajustar os caracteres de continuação de linha ou as linhas de atribuição de variáveis apropriadamente. Este artigo usa variáveis para minimizar a quantidade de edição de comando necessária.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual você implanta e gerencia os recursos do Azure. O seguinte comando [az group create][az-group-create] cria um grupo de recursos nomeado *myResourceGroup* na região *eastus*. Se você quiser usar um nome diferente para o grupo de recursos, configure `RESOURCE_GROUP_NAME` para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Criar um Serviço SignalR

Em seguida, implante um Serviço de Sinalizador Azure no grupo de recursos com os seguintes comandos.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Uma vez que o SignalR Service tenha sido criado, o Azure CLI retorna a saída semelhante à seguinte:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Criar um ponto de extremidade de evento

Nesta seção, você usa um modelo do Resource Manager localizado em um repositório GitHub para implantar um aplicativo Web de exemplo pré-criado no Serviço de Aplicativo do Azure. Posteriormente, você assina os eventos da Grade de Eventos do registro e especifica esse aplicativo como o ponto de extremidade para o qual os eventos são enviados.

Para implantar o aplicativo de exemplo, defina `SITE_NAME` como um nome exclusivo para o aplicativo Web e execute os seguintes comandos. O nome do site deve ser exclusivo no Azure porque faz parte do FQDN (nome de domínio totalmente qualificado) do aplicativo Web. Em uma seção posterior, você navega para o FQDN do aplicativo em um navegador da Web para exibir os eventos do registro.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Uma vez que a implantação seja bem sucedida (pode levar alguns minutos), abra um navegador e navegue até o seu aplicativo web para ter certeza de que ele está sendo executado:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Assinar eventos de registro

Na Grade de Eventos, você assina um *tópico* para informar quais eventos quer acompanhar e para onde enviá-los. O comando [az eventgrid eventgrid event-subscription create][az-eventgrid-event-subscription-create] assina o Azure SignalR Service que você criou e especifica a URL do seu aplicativo web como o ponto final para o qual ele deve enviar eventos. As variáveis de ambiente que você preencheu nas seções anteriores são reutilizadas aqui, portanto, nenhuma edição é necessária.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Quando a assinatura estiver concluída, você deverá ver uma saída semelhante à seguinte:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Disparar eventos de registro

Mude para o `Serverless Mode` modo de serviço para e configure uma conexão do cliente com o Serviço SignalR. Você pode tomar [serverless Sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) como referência.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Exibir eventos do registro

Agora você conectou um cliente ao SignalR Service. Navegue até o aplicativo web Event Grid `ClientConnectionConnected` Viewer e você deve ver um evento. Se você encerrar o cliente, `ClientConnectionDisconnected` você também verá um evento.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
