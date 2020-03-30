---
title: Reagir aos eventos do módulo blob storage - Azure Event Grid IoT Edge | Microsoft Docs
description: Reagir aos eventos do módulo de armazenamento Blob
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086612"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: Reaja aos eventos de armazenamento blob no IoT Edge (Preview)
Este artigo mostra como implantar o Azure Blob Storage no módulo IoT, que atuaria como um editor da Event Grid para enviar eventos sobre criação de Blob e exclusão de Blob para Event Grid.  

Para obter uma visão geral do Armazenamento Azure Blob no IoT Edge, consulte [O Armazenamento Azure Blob no IoT Edge](../../iot-edge/how-to-store-data-blob.md) e suas características.

> [!WARNING]
> Azure Blob Storage na integração ioT Edge com event grid está em Visualização

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

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
    > Neste tutorial, você aprenderá a implantar o módulo Event Grid para permitir que ambas as solicitações HTTP/HTTPs, autenticação do cliente desativada. Para cargas de trabalho de produção, recomendamos que você habilite apenas solicitações de HTTPs e assinantes com a autenticação do cliente ativada. Para obter mais informações sobre como configurar o módulo Event Grid com segurança, consulte [Segurança e autenticação](security-authentication.md).
    

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
1. Continue até a próxima seção para adicionar o módulo de armazenamento Azure Blob

## <a name="deploy-azure-blob-storage-module"></a>Implantar módulo de armazenamento Azure Blob

Esta seção mostra como implantar o módulo De armazenamento Azure Blob, que funcionaria como um editor do Event Grid publicando eventos criados e excluídos.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **Módulos de implantação,** selecione **Adicionar**
2. A partir dos tipos de módulos na lista de paradas, selecione **IoT Edge Module**
3. Forneça as opções de criação de nome, imagem e contêiner do contêiner:

   * **Nome**: azureblobstorageoniotedge
   * **Uri de imagem**: mcr.microsoft.com/azure-blob-storage:latest
   * **Opções de Criação de Contêiner**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - O módulo Blob Storage pode publicar eventos usando HTTPS e HTTP. 
   > - Se você habilitou a autenticação baseada no cliente para EventGrid, certifique-se de `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`atualizar o valor de EVENTGRID_ENDPOINT para permitir https, como este: .
   > - Adicione também outra `AllowUnknownCertificateAuthority=true` variável de ambiente ao Json acima. Ao falar com eventGrid sobre HTTPS, **AllowUnknownCertificateAuthority** permite que o módulo de armazenamento confie em certificados de servidor EventGrid auto-assinados.

4. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes das contas devem ter de 3 a 24 caracteres, com letras minúsculas e números. Não são permitidos espaços.

   - Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

   - Substitua pelo `<event grid module name>` nome do módulo Event Grid.
   - Substitua de `<storage mount>` acordo com o sistema operacional do contêiner.
     - Para contêineres Linux, **my-volume:/blobroot**
     - Para contêineres do Windows,**meu volume:C:/BlobRoot**

5. Clique em **Salvar**
6. Clique **em Next** para continuar na seção rotas

    > [!NOTE]
    > Se você estiver usando uma VM do Azure como dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada nas portas host usadas neste tutorial: 4438, 5888, 8080 e 11002. Para obter instruções sobre como adicionar a regra, consulte [Como abrir portas em uma VM](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Rotas de configuração

Mantenha as rotas padrão e selecione **'Seguinte'** para continuar até a seção de revisão

### <a name="review-deployment"></a>Rever implantação

1. A seção de revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme se você vê os quatro módulos a seguir: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **assinante** e **azureblobstorageoniotedge** que todos estão sendo implantados.
2. Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="verify-your-deployment"></a>Verifique sua implantação

1. Depois de enviar a implantação, você volta para a página do IoT Edge de seu hub IoT.
2. Selecione o **dispositivo IoT Edge** que você direcionou com a implantação para abrir seus detalhes.
3. Nos detalhes do dispositivo, verifique se os módulos eventgridmodule, subscriber e azureblobstorageoniotedge estão listados como **ambos especificados na implantação** e **relatados pelo dispositivo**.

   Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicar eventos BlobCreated e BlobDeleted

1. Este módulo cria automaticamente o tópico **MicrosoftStorage**. Verifique se ele existe
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via tecla SAS, a chave SAS especificada anteriormente deve ser adicionada como um cabeçalho. Por isso, o pedido de cacho será:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, a solicitação de cacho será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Os assinantes podem se inscrever para eventos publicados em um tópico. Para receber qualquer evento, você precisará criar uma assinatura do Event Grid para o tópico **MicrosoftStorage.**
    1. Crie blobsubscription.json com o seguinte conteúdo. Para obter detalhes sobre a carga útil, consulte nossa [documentação da API](api.md)

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
       > A propriedade **endpointType** especifica que o assinante é um **Webhook**.  O **endpointUrl** especifica a URL na qual o assinante está ouvindo eventos. Esta URL corresponde à amostra de função Azure que você implantou anteriormente.

    2. Execute o seguinte comando para criar uma assinatura para o tópico. Confirme se você vê `200 OK`o código de status HTTP é .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via tecla SAS, a chave SAS especificada anteriormente deve ser adicionada como um cabeçalho. Por isso, o pedido de cacho será:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, a solicitação de cacho será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Execute o seguinte comando para verificar se a assinatura foi criada com sucesso. HTTP Status Code of 200 OK deve ser devolvido.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Saída de exemplo:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via tecla SAS, a chave SAS especificada anteriormente deve ser adicionada como um cabeçalho. Por isso, o pedido de cacho será:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver ativada via certificado, a solicitação de cacho será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Baixe [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [conecte-o ao seu armazenamento local](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificar a entrega do evento

### <a name="verify-blobcreated-event-delivery"></a>Verificar a entrega de eventos BlobCreated

1. Carregue arquivos como blobs de bloco para o armazenamento local do Azure Storage Explorer, e o módulo publicará automaticamente criar eventos. 
2. Confira os registros de assinantes para criar o evento. Siga as etapas para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verificar a entrega de eventos blobDeleted

1. Exclua blobs do armazenamento local usando o Azure Storage Explorer e o módulo publicará automaticamente eventos de exclusão. 
2. Confira os registros de assinantes para excluir o evento. Siga as etapas para [verificar a entrega do evento](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Parabéns! Você completou o tutorial. As seções a seguir fornecem detalhes sobre as propriedades do evento.

### <a name="event-properties"></a>Propriedades do evento

Aqui está a lista de propriedades de eventos suportadas e seus tipos e descrições. 

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. Pode ser um dos seguintes valores: <ul><li>BlobCreated - valores `PutBlob` permitidos são: e`PutBlockList`</li><li>BlobDeleted - os `DeleteBlob` `DeleteAfterUpload` valores permitidos são , e `AutoDelete`. <p>O `DeleteAfterUpload` evento é gerado quando o blob é excluído automaticamente porque a propriedade desejada deleteAfterUpload é definida como true. </p><p>`AutoDelete`evento é gerado quando blob é automaticamente excluído porque deleteAfterMinutes valor de propriedade desejado expirado.</p></li></ul>|
| clientRequestId | string | um ID de solicitação fornecido pelo cliente para a operação de API de armazenamento. Esse ID pode ser usado para correlacionar-se com os registros de diagnóstico do Azure Storage usando o campo "cliente-request-id" nos logs e pode ser fornecido em solicitações de clientes usando o cabeçalho "x-ms-client-request-id". Para obter detalhes, consulte [Log Format](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID de solicitação gerado por serviço para a operação de API de armazenamento. Pode ser usada para correlacionar com os logs de diagnóstico do Armazenamento do Azure usando o campo "request-id-header" nos logs, e retornada pela inicialização da chamada á API no cabeçalho 'x-ms-request-id'. Consulte [Formato de Log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | inteiro | O tamanho do blob em bytes. |
| BlobType | string | O tipo de blob. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para o blob. <br>Se o cliente usar uma API Blob REST, a url tem essa estrutura: * \<\>nome da conta de armazenamento .blob.core.windows.net/\<\>/\<\>nome do arquivo de nome do contêiner*. <br>Se o cliente usar uma API Data Lake Storage REST, a url tem essa estrutura: * \<\>nome da conta de armazenamento .dfs.core.windows.net/\<nome\>/\<\>do arquivo-nome do arquivo.* |


## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos da documentação do Blob Storage:

- [Filtrar eventos de armazenamento blob](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Práticas recomendadas para consumir eventos de armazenamento Blob](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Neste tutorial, você publica eventos criando ou excluindo blobs em um Armazenamento Azure Blob. Veja os outros tutoriais para aprender como encaminhar eventos para nuvem (Azure Event Hub ou Azure IoT Hub): 

- [Encaminhar eventos para a Grade de Eventos do Azure](forward-events-event-grid-cloud.md)
- [Encaminhar eventos para o Hub IoT do Azure](forward-events-iothub.md)
