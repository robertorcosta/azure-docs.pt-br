---
title: Publicar, inscrever-se em eventos localmente - Azure Event Grid IoT Edge | Microsoft Docs
description: Publicar, assinar eventos localmente usando webhook com Event Grid no IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280996"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Tutorial: Publique, inscreva-se em eventos localmente

Este artigo orienta você através de todas as etapas necessárias para publicar e assinar eventos usando Event Grid no IoT Edge.

> [!NOTE]
> Para saber mais sobre os tópicos e assinaturas do Azure Event Grid, consulte [Event Grid Concepts](concepts.md).

## <a name="prerequisites"></a>Pré-requisitos 
Para completar este tutorial, você precisará:

* **Assinatura do Azure** - Crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Dispositivo Azure IoT Hub e IoT Edge** - Siga os passos no quickstart para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implantar módulo de borda IoT da grade de eventos

Existem várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para o Azure Event Grid no IoT Edge. Este artigo descreve as etapas para implantar a Event Grid no IoT Edge a partir do portal Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo Event Grid sem persistência. Isso significa que todos os tópicos e assinaturas que você criar neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [Persist state in Linux](persist-state-linux.md) ou Persist state in [Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo Event Grid com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Entre no [portal do Azure](https://portal.azure.com)
1. Navegue até seu Hub IoT.
1. Selecione **IoT Edge** no menu na seção **Gerenciamento automático de dispositivos.** 
1. Clique no ID do dispositivo de destino da lista de dispositivos
1. Selecione **Módulos de conjunto**. Mantenha a página aberta. Você continuará com os passos na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal Azure tem um assistente que orienta você a criar um manifesto de implantação, em vez de construir o documento JSON manualmente.  Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **Módulos de implantação,** selecione **Adicionar**
1. A partir dos tipos de módulos na lista de paradas, selecione **IoT Edge Module**
1. Forneça o nome, imagem, opções de criação de contêiner do contêiner:

   * **Nome**: eventgridmodule
   * **Uri de imagem**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de Criação de Contêiner**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Clique em **Salvar**
 1. Continue até a próxima seção para adicionar o módulo Azure Event Grid Subscriber antes de implantá-los juntos.

    >[!IMPORTANT]
    > Neste tutorial, você implantará o módulo Event Grid com autenticação do cliente desativada. Para cargas de trabalho de produção, recomendamos que você habilite a autenticação do cliente. Para obter mais informações sobre como configurar o módulo Event Grid com segurança, consulte [Segurança e autenticação](security-authentication.md).
    > 
    > Se você estiver usando uma VM Do Azure como um dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [Como abrir portas em uma VM](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Implantar módulo IoT Edge do assinante da grade de eventos

Esta seção mostra como implantar outro módulo IoT que agiria como um manipulador de eventos para os quais os eventos podem ser entregues.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **Módulos de implantação,** **selecione Adicionar** novamente. 
1. A partir dos tipos de módulos na lista de paradas, selecione **IoT Edge Module**
1. Forneça as opções de criação de nome, imagem e contêiner do contêiner:

   * **Nome**: assinante
   * **Uri de imagem**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opções de criação de contêineres**: nenhuma
1. Clique em **Salvar**
1. Clique **em Next** para continuar na seção rotas

 ### <a name="setup-routes"></a>Rotas de configuração

Mantenha as rotas padrão e selecione **'Seguinte'** para continuar até a seção de revisão

### <a name="submit-the-deployment-request"></a>Enviar a solicitação de implantação

1. A seção de revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme se você vê ambos os módulos: **eventgridmodule** e **assinante** listados no JSON. 
1. Examine as informações da implantação e, em seguida, selecione **Enviar**. Depois de enviar a implantação, você retorna à página do **dispositivo.**
1. Na **seção Módulos,** verifique se ambos os módulos **eventgrid** e **assinantes** estão listados. E, verifique se as **colunas Especificadas na implantação** e **relatadas por colunas do dispositivo** estão definidas como **Sim**.

    Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="create-a-topic"></a>Criar um tópico

Como editor de um evento, você precisa criar um tópico da grade de eventos. No Azure Event Grid, um tópico refere-se a um ponto final para onde os editores podem enviar eventos.

1. Crie topic.json com o seguinte conteúdo. Para obter detalhes sobre a carga útil, consulte nossa [documentação da API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Execute o seguinte comando para criar um tópico de grade de eventos. Confirme se você vê `200 OK`o código de status HTTP é .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Execute o seguinte comando para verificar se o tópico foi criado com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento

Os assinantes podem se inscrever para eventos publicados em um tópico. Para receber qualquer evento, você precisará criar uma assinatura do Event Grid para um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription.json com o seguinte conteúdo. Para obter detalhes sobre a carga útil, consulte nossa [documentação da API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A propriedade **endpointType** especifica que o assinante é um **Webhook**.  O **endpointUrl** especifica a URL na qual o assinante está ouvindo eventos. Esta URL corresponde à amostra do Azure Subscriber que você implantou anteriormente.
2. Execute o seguinte comando para criar uma assinatura para o tópico. Confirme se você vê `200 OK`o código de status HTTP é .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Execute o seguinte comando para verificar se a assinatura foi criada com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicar um evento

1. Crie event.json com o seguinte conteúdo. Para obter detalhes sobre a carga útil, consulte nossa [documentação da API](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Execute o seguinte comando para publicar um evento.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

1. SSH ou RDP em seu VM IoT Edge.
1. Confira os registros de assinantes:

    No Windows, execute o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   No Linux, execute o seguinte comando:

    ```sh
    sudo docker logs subscriber
    ```

    Saída de exemplo:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Exclua o módulo do assinante do seu dispositivo IoT Edge.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um tópico de grade de eventos, assinatura e eventos publicados. Agora que você sabe os passos básicos, veja os seguintes artigos: 

- Para solucionar problemas com o uso do Azure Event Grid no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
- Criar/atualizar a assinatura com [filtros](advanced-filtering.md).
- Habilite a persistência do módulo Event Grid no [Linux](persist-state-linux.md) ou [Windows](persist-state-windows.md)
- Siga a [documentação](configure-client-auth.md) para configurar a autenticação do cliente
- Encaminhe eventos para funções do Azure na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
- [Reaja aos eventos de armazenamento blob na Borda IoT](react-blob-storage-events-locally.md)
- [Monitore tópicos e assinaturas no limite](monitor-topics-subscriptions.md)

