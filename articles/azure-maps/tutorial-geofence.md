---
title: 'Tutorial: Criar uma cerca geográfica e controlar dispositivos no Microsoft Azure Mapas'
description: Saiba como configurar um limite geográfico e acompanhar os dispositivos em relação ao limite geográfico usando o Serviço Espacial dos Microsoft Azure Mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 775d98b992f2bca4441c868873ceaeb2389db81a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517310"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Configurar um limite geográfico usando o Azure Mapas

Este tutorial ensina as noções básicas de criação e uso dos serviços de Cerca Geográfica do Azure Mapas no contexto do seguinte cenário:

*Um Gerenciador de Sites de construção precisa acompanhar o equipamento conforme ele entra e sai dos perímetros de uma área de construção. Sempre que uma parte do equipamento sair ou entrar nesses perímetros, uma notificação por email será enviada para o Operations Manager.*

O Azure Mapas fornece vários serviços para dar suporte ao acompanhamento de equipamentos que entram e saem da área de construção no cenário acima. Neste tutorial, abordaremos como:

> [!div class="checklist"]
> * Carregue os [Dados GeoJSON de delimitação geográfica](geofence-geojson.md) que define as áreas do site de construção que desejamos monitorar. Usaremos a [API de Carregamento de Dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) para carregar cercas geográficas como coordenadas em polígono na sua conta do Azure Mapas.
> * Configure dois [Aplicativos Lógicos](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) que, quando disparados, enviarão notificações por email para o Operations Manager do site de construção quando o equipamento entrar e sair da área de cerca geográfica.
> * Use a [Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) para assinar eventos de entrada e saída de cerca geográfica do Azure Mapas. Vamos configurar duas assinaturas de evento do web hook que chamarão os pontos de extremidade HTTP definidos nos dois Aplicativos Lógicos. Em seguida, os Aplicativos Lógicos enviarão as notificações por email apropriadas de equipamentos que se movem além da cerca geográfica ou que entram nela.
> * Use a [Pesquisa de API de GET de Cerca Geográfica](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) para receber notificações quando uma parte do equipamento sair e entrar em áreas de cerca geográfica.

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

Este tutorial usa o aplicativo [Postman](https://www.postman.com/), mas você pode escolher um ambiente de desenvolvimento de API diferente.

## <a name="upload-geofencing-geojson-data"></a>Carregando dados GeoJSON de delimitação geográfica

Neste tutorial, vamos carregar dados GeoJSON de delimitação geográfica que contenham um `FeatureCollection`. O `FeatureCollection` contém duas cercas geográficas que definem áreas poligonais no site de construção. A primeira cerca geográfica não tem restrições ou hora de término. A segunda só pode ser consultada durante o horário comercial (9h às 17h PST) e não será mais válida após 1º de janeiro de 2022. Para obter mais informações sobre o formato GeoJSON, confira [Dados de GeoJSON de delimitação geográfica](geofence-geojson.md).

>[!TIP]
>Você pode atualizar os dados de Delimitação Geográfica a qualquer momento. Para obter mais informações sobre como atualizar os seus dados, confira a [API de Carregamento de Dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)

1. Abra o aplicativo Postman. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Coleção**.  Nomeie a coleção e selecione o botão **Criar**.

2. Para criar a solicitação, selecione **Novo** outra vez. Na janela **Criar**, selecione **Solicitação**. Insira um **Nome de solicitação** para a solicitação. Selecione a coleção que você criou na etapa anterior e clique em **Salvar**.

3. Selecione o método HTTP **POST** na guia do construtor e insira a URL a seguir para carregar os dados de delimitação geográfica no serviço do Azure Mapas. Para essa solicitação e outras solicitações mencionadas neste artigo, substitua `{Azure-Maps-Primary-Subscription-key}` pela sua chave de assinatura primária.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    O parâmetro _geojson_ no caminho da URL representa o formato dos dados que estão sendo carregados.

4. Clique na guia **Corpo**. Selecione **bruto** e escolha **JSON** como o formato de entrada. Copie e cole os seguintes dados GeoJSON na área de texto **Corpo**:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Clique no botão azul **Enviar** e aguarde a solicitação ser processada. Depois que a solicitação for concluída, vá para a guia **Cabeçalhos** da resposta. Copie o valor da chave **Local**, que é `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Para verificar o status da chamada à API, crie uma solicitação HTTP **GET** em `status URL`. Você precisará acrescentar sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Quando a solicitação HTTP **GET** for concluída com êxito, ela retornará um `resourceLocation`. O `resourceLocation` contém o `udid` exclusivo para o conteúdo carregado. Você precisará salvar este `udid` para consultar a API de GET de Cerca Geográfica na última seção deste tutorial. Opcionalmente, você poderá usar a URL do `resourceLocation` para recuperar metadados desse recurso na próxima etapa.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Para recuperar metadados de conteúdo, crie uma solicitação HTTP **GET** na URL do `resourceLocation` que foi recuperada na etapa 7. Certifique-se de acrescentar a sua chave de assinatura primária à URL para autenticação. A solicitação **GET** deve ser semelhante à seguinte URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Quando o a solicitação HTTP **GET** for concluída com êxito, o corpo da resposta conterá o `udid` especificado no `resourceLocation` da etapa 7, a localização para acessar/baixar o conteúdo no futuro e outros metadados sobre o conteúdo, como data de criação/atualização, tamanho e assim por diante. Um exemplo da resposta geral é:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>Criar fluxos de trabalho de Aplicativo Lógico

Nesta seção, criaremos dois pontos de extremidade de [Aplicativo Lógico](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) que dispararão uma notificação por email. Mostraremos como criar o primeiro gatilho que enviará notificações por email sempre que o ponto de extremidade for chamado.

1. Entre no [Portal do Azure](https://portal.azure.com)

2. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.

3. Na caixa *Pesquisar no Marketplace*, digite **Aplicativo Lógico**.

4. Nos *Resultados*, selecione **Aplicativo Lógico**. Clique no botão **Criar**.

5. Na página **Aplicativo Lógico**, insira os seguintes valores:
    * A *Assinatura* que você deseja usar para esse aplicativo lógico.
    * O nome do *Grupo de recursos* para esse aplicativo lógico. Você pode optar por *Criar novo* ou *Usar existente* em relação ao grupo de recursos.
    * O *nome do seu Aplicativo Lógico*. Nesse caso, usaremos `Equipment-Enter` como o nome.

    Para este tutorial, mantenha o restante dos valores nas configurações padrão.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Criar um aplicativo lógico":::

6. Clique no botão **Examinar + Criar**. Examine as configurações e clique em **Criar** para enviar a implantação. Quando a implantação for concluída com êxito, clique em **Acessar o recurso**. Você será levado para o **Designer de Aplicativo Lógico**

7. Agora, selecionaremos um tipo de gatilho. Role um pouco para baixo até a seção *Iniciar com um gatilho comum**. Clique em **Quando uma solicitação HTTP for recebida**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Criar um gatilho HTTP do aplicativo lógico":::

8. Clique em **Salvar** no canto superior direito do Designer. A **URL de HTTP POST** será gerada automaticamente. Salve a URL, pois você precisará dela na próxima seção para criar um ponto de extremidade de evento.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="JSON e URL de solicitação HTTP do Aplicativo Lógico":::

9. Selecione **+ Nova Etapa**. Agora, escolha uma ação. Digite `outlook.com email` na caixa de pesquisa. Na lista **Ações**, role para baixo e clique em **Enviar um email (V2)** .
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Criar um designer de aplicativo lógico":::

10. Entrar na conta do Outlook.com. Certifique-se de clicar em **Sim** para permitir que o Aplicativo Lógico acesse a conta. Preencha os campos para enviar um email.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Criar uma etapa Enviar email do aplicativo lógico":::

    >[!TIP]
    > Você pode recuperar dados de resposta GeoJSON, como `geometryId` ou `deviceId`, nas suas notificações por email configurando o Aplicativo Lógico para ler os dados enviados pela Grade de Eventos. Para obter informações sobre como configurar o Aplicativo Lógico para consumir e transmitir dados de evento em notificações por email, confira [Tutorial: enviar notificações por email sobre os eventos do Hub IoT do Azure usando os Aplicativos Lógicos e a Grade de Eventos](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Clique em **Salvar** no canto superior esquerdo do Designer de Aplicativos Lógicos.

12. Repita as etapas 3 a 11 para criar um segundo Aplicativo Lógico para notificar o gerente quando o equipamento sair do site de construção. Nome do Aplicativo Lógico `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Criar assinaturas de Eventos do Azure Mapas

O Azure Mapas dá suporte a três tipos de evento. Você pode dar uma olhada nos tipos de evento compatíveis do Azure Mapas [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps).  Precisaremos criar duas assinaturas de evento diferentes: uma para os eventos de entrada de cerca geográfica e outra para os eventos de saída.

Siga as etapas abaixo para criar uma assinatura de evento para eventos de entrada no limite geográfico. Você pode assinar eventos de saída de cerca geográfica repetindo as etapas de maneira semelhante.

1. Navegue até a conta dos Azure Mapas. No painel, selecione **Assinaturas**. Clique no nome da sua assinatura e selecione **Eventos** no menu Configurações.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Navegue até os Eventos da conta do Azure Mapas":::

2. Para criar uma assinatura de evento, selecione **+ Assinatura de Evento** na página de eventos.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Criar uma assinatura de Eventos do Azure Mapas":::

3. Na página **Criar Assinatura de Eventos**, insira os seguintes valores:
    * O *Nome* da assinatura de evento.
    * O *Esquema de Evento* deve ser *Esquema de Grade de Eventos*.
    * O *Nome do Tópico do Sistema* dessa assinatura de evento. Neste caso, usaremos `Contoso-Construction`.
    * Em *Filtrar os Tipos de Evento*, escolha `Geofence Entered` como o tipo de evento.
    * Para o *Tipo de Ponto de Extremidade*, escolha `Web Hook`.
    * Para *Ponto de extremidade*, copie a URL de HTTP POST para o Aplicativo Lógico. Insira o ponto de extremidade que você criou na seção anterior. Se você esqueceu de salvá-lo, basta voltar ao Designer de Aplicativo Lógico e copiá-lo na etapa do gatilho HTTP.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Detalhes da assinatura de Eventos do Azure Mapas":::

4. Clique em **Criar**.

5. Repita as etapas 1 a 4 para o ponto de extremidade de Saída do Aplicativo Lógico criado na seção anterior. Na etapa 3, certifique-se de escolher `Geofence Exited` como o tipo de evento.

## <a name="use-search-geofence-get-api"></a>Usar a Pesquisa de API de GET de Cerca Geográfica

Agora, usaremos a [Pesquisa de API de GET de Cerca Geográfica](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) para enviar notificações por email para o Operations Manager quando um equipamento entrar ou sair das cercas geográficas.

Cada equipamento tem um `deviceId`. Neste tutorial, vamos acompanhar um só equipamento, cuja ID exclusiva é `device_1`.

Para maior clareza, o diagrama a seguir mostra os cinco localizações do equipamento ao longo do tempo, começando na localização de *Início*, que está em algum lugar do lado de fora das cercas geográficas. Neste tutorial, a localização de *Início* é indefinida, já que não consultamos o dispositivo nessa localização.

Se consultarmos a [Pesquisa de API de GET de Cerca Geográfica](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) com uma localização de equipamento que indica a entrada ou saída de cerca geográfica inicial, a Grade de Eventos chamará o ponto de extremidade do Aplicativo Lógico apropriado para enviar uma notificação por email para o Operations Manager.

Cada uma das sessões a seguir fazem as solicitações de API de Delimitação Geográfica de HTTP GET usando as cinco diferentes coordenadas de localização dos equipamentos.

![Mapa de cerca geográfica no Azure Mapas](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Localização do Equipamento 1 (47,638237,-122,132483)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**.  Insira um **Nome de solicitação** para a solicitação. Usaremos o nome *Localização 1*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia construtor e insira a URL a seguir. Certifique-se de substituir `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Clique no botão **Enviar**. O GeoJSON a seguir aparecerá na janela de resposta.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. Na resposta GeoJSON acima, a distância negativa da cerca geográfica principal significa que os equipamentos estão dentro da cerca geográfica. A distância positiva do limite geográfico do subsite significa que os equipamentos estão fora do limite geográfico do subsite. Como esta é a primeira vez que o dispositivo está localizado na cerca geográfica do site principal, o parâmetro `isEventPublished` é definido como `true` e o Operations Manager recebe uma notificação por email de que o equipamento entrou na cerca geográfica.

### <a name="location-2-4763800-122132531"></a>Localização 2 (47,63800,-122,132531)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**.  Insira um **Nome de solicitação** para a solicitação. Usaremos o nome *Localização 2*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia construtor e insira a URL a seguir. Certifique-se de substituir `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Clique no botão **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. Na resposta GeoJSON acima, o equipamento permaneceu na cerca geográfica do site principal e não entrou na cerca geográfica do subsite. Como resultado, o parâmetro `isEventPublished` é definido como `false` e o Operations Manager não receberá notificações por email.

### <a name="location-3-4763810783315048-12213336020708084"></a>Localização 3 (47,63810783315048,-122,13336020708084)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**.  Insira um **Nome de solicitação** para a solicitação. Usaremos o nome *Localização 3*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia construtor e insira a URL a seguir. Certifique-se de substituir `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Clique no botão **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. Na resposta GeoJSON acima, o equipamento permaneceu na cerca geográfica do site principal, mas entrou na cerca geográfica do subsite. Como resultado, o parâmetro `isEventPublished` é definido como `true` e o Operations Manager receberá uma notificação por email indicando que o equipamento entrou em uma cerca geográfica.

    >[!NOTE]
    >Se o equipamento tivesse sido transferido para o subsite após o horário comercial, nenhum evento seria publicado e o Operations Manager não receberia nenhuma notificação.  

### <a name="location-4-47637988-1221338344"></a>Localização 4 (47,637988,-122,1338344)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**.  Insira um **Nome de solicitação** para a solicitação. Usaremos o nome *Localização 4*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia construtor e insira a URL a seguir. Certifique-se de substituir `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Clique no botão **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. Na resposta GeoJSON acima, o equipamento permaneceu na cerca geográfica do site principal, mas saiu da cerca geográfica do subsite. No entanto, se você observar, o valor de `userTime` está após o `expiredTime`, conforme definido nos dados de cerca geográfica. Como resultado, o parâmetro `isEventPublished` é definido como `false` e o Operations Manager não receberá uma notificação por email.

### <a name="location-547637988-1221338344"></a>Localização 5 (47,637988,-122,1338344)

1. Próximo à parte superior do aplicativo Postman, selecione **Novo**. Na janela **Criar**, selecione **Solicitação**.  Insira um **Nome de solicitação** para a solicitação. Usaremos o nome *Localização 4*. Selecione a coleção que você criou na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data) e escolha **Salvar**.

2. Selecione o método HTTP **GET** na guia construtor e insira a URL a seguir. Certifique-se de substituir `{Azure-Maps-Primary-Subscription-key}` pela chave de assinatura primária e `{udid}` pelo `udid` salvo na seção [Carregar dados GeoJSON de delimitação geográfica](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Clique no botão **Enviar**. O seguinte GeoJSON aparecerá na janela de resposta:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. Na resposta GeoJSON acima, o equipamento saiu da cerca geográfica do site principal. Como resultado, o parâmetro `isEventPublished` é definido como `true` e o Operations Manager receberá uma notificação por email indicando que o equipamento saiu de uma cerca geográfica.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Manipular tipos de conteúdo em Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [Enviar notificações por email usando os Aplicativos Lógicos e a Grade de Eventos](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [Manipuladores de Eventos com suporte na Grade de Eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
