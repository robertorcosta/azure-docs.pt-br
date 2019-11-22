---
title: 'Tutorial: Criar uma cerca geográfica usando o Azure Mapas'
description: 'Tutorial: Configure um limite geográfico usando o Azure Mapas.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 62005546c653796773083eaf625820ab532a8a2c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107105"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Configurar um limite geográfico usando o Azure Mapas

Este tutorial orienta você pelas etapas básicas para configurar um limite geográfico usando o Azure Mapas. O cenário abordado neste tutorial é o de ajudar gerentes de locais de construção a monitorar a movimentação de equipamentos potencialmente perigosos para além das áreas de construção designadas. Um local de construção envolve normas e equipamentos caros. Normalmente, ele exige que o equipamento permaneça dentro do local de construção e não saia sem permissão.

Nós usaremos a API de Upload de Dados do Azure Mapas para armazenar um limite geográfico e usar a API de Limite Geográfico do Azure Mapas para verificar a localização do equipamento em relação ao limite geográfico. Nós usaremos a Grade de Eventos do Azure para transmitir os resultados do limite geográfico e configurar uma notificação de acordo com os resultados desse limite.
Para saber mais sobre a Grade de Eventos, confira [Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).
Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregue a área do limite geográfico no Azure Mapas, serviço de dados usando a API de Upload de Dados.
> *   Configure uma Grade de Eventos para manipular eventos de limite geográfico.
> *   Configure o manipulador de eventos do limite geográfico.
> *   Configure alertas em resposta a eventos do limite geográfico usando os Aplicativos Lógicos.
> *   Use APIs do serviço do limite geográfico do Azure Mapas para controlar se um ativo de construção está ou não dentro do local de construção.


## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure 

Para concluir as etapas deste tutorial, siga as instruções em [gerenciar conta](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) para criar uma assinatura de conta do Azure Mapas com tipo de preço S1 e siga as etapas em [obter chave primária](./tutorial-search-location.md#getkey) para obter a chave primária de assinatura da conta.

## <a name="upload-geofences"></a>Carregar limites geográficos

Para carregar o limite geográfico para o local de construção usando a API de Upload de Dados, usaremos o aplicativo postman. Para fins deste tutorial, pressupomos que haja uma área de site de construção geral, um parâmetro rígido que o equipamento de construção não deve violar. Violações desse limite são uma ofensa grave e são relatadas para o gerente de operações. É possível utilizar um conjunto otimizado de limites adicionais que acompanham diferentes áreas de construção dentro da área de construção geral, de acordo com o agendamento. Podemos supor que o limite geográfico principal tem um subsite1, que por sua vez tem um tempo de expiração após o qual expirará. Você pode criar mais limites geográficos aninhados de acordo com suas necessidades. Por exemplo, o subsite1 poderia ser o local em que o trabalho está ocorrendo durante as semanas 1 a 4 do agendamento, enquanto o subsitel2 seria onde trabalho está ocorrendo durante as semanas 5 a 7. Todos esses limites podem ser carregados como um único conjunto de dados no início do projeto e usados para rastrear as regras com base no tempo e no espaço. Para obter mais informações sobre o formato de dados geográficos, confira [dados GeoJSON de limite geográfico](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Para obter mais informações sobre como carregar dados no serviço Azure Mapas, confira [documentação da API de upload de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview).

Abra o aplicativo Postman e siga as etapas a seguir para carregar o limite geográfico do local de construção usando a API de upload de dados do Azure Mapas.

1. Abra o aplicativo Postman, clique em novo | Criar novo e selecione Solicitação. Insira um nome de solicitação para o upload de dados de limite geográfico, selecione uma coleção ou uma pasta em que salvá-los e clique em Salvar.

    ![Carregar limites geográficos usando o Postman](./media/tutorial-geofence/postman-new.png)

2. Selecione o método HTTP POST na guia compilador e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro GEOJSON no caminho da URL representa o formato dos dados sendo carregados.

3. Clique em **Parâmetros** e insira os seguintes pares de chave/valor a serem usados para a URL da solicitação POST. Substitua o valor subscription-key pela chave de assinatura primária do Azure Mapas.
   
    ![Postman de parâmetros de chave-valor](./media/tutorial-geofence/postman-key-vals.png)

4. Clique em **Corpo** e, em seguida, selecione o formato de entrada bruto e escolha JSON como o formato de entrada na lista suspensa. Forneça o seguinte JSON como dados a serem carregados:

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
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
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

5. Clique em enviar e revise o cabeçalho de resposta. Após uma solicitação bem-sucedida, o cabeçalho **Local** conterá o URI de status para verificar o status atual da solicitação de upload. O URI de status estará no formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie seu URI de status e acrescente um parâmetro `subscription-key` a ele com seu valor sendo sua chave de assinatura de conta do Azure Mapas. O formato do URI de status deve ser semelhante ao mostrado abaixo:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter o `udId`, abra uma nova guia no aplicativo de postmaster e selecione obter método GET HTTP na guia construtor e faça uma solicitação GET no URI de status. Se o upload de dados tiver sido bem-sucedido, você receberá um udId no corpo da resposta. Copie o udId para uso posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Para notificar o gerente de operações sobre eventos de entrada e de saída, devemos criar um manipulador de eventos que recebe as notificações.

Criaremos dois serviços de [Aplicativos Lógicos](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) para manipulação, entrada e saída de eventos. Também criaremos gatilhos de evento dentro dos Aplicativos Lógicos que são disparados por esses eventos. A ideia é enviar alertas, nesse caso emails, ao gerente de operações sempre que o equipamento entrar ou sair do local de construção. A figura a seguir ilustra a criação de um Aplicativo Lógico para o evento de entrada no limite geográfico. Da mesma forma, você pode criar outro para o evento de saída.
Você pode ver todos os [manipuladores de eventos com suporte](https://docs.microsoft.com/azure/event-grid/event-handlers) para obter mais informações.

1. Criar um aplicativo lógico no portal do Azure

   ![Criar Aplicativos Lógicos](./media/tutorial-geofence/logic-app.png)

2. Selecione um gatilho de solicitação HTTP e, em seguida, selecione "enviar um email" como uma ação no conector do Outlook
  
   ![Esquema dos Aplicativos Lógicos](./media/tutorial-geofence/logic-app-schema.png)

3. Salve o Aplicativo Lógico para gerar o ponto de extremidade de URL HTTP e copie a URL HTTP.

   ![Ponto de extremidade de Aplicativos Lógicos](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Criar uma assinatura de Eventos do Azure Mapas

O Azure Mapas dá suporte a três tipos de evento. Você pode dar uma olhada nos tipos de evento compatíveis do Azure Mapas [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Criaremos duas assinaturas diferentes, uma para eventos de entrada e outra para eventos de saída.

Siga as etapas abaixo para criar uma assinatura de evento para eventos de entrada no limite geográfico. Você pode assinar eventos de saída do limite geográfico de maneira semelhante.

1. Navegue até sua conta do Azure Mapas por meio [deste link ao portal](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) e selecione a guia Eventos.

   ![Status do Azure Mapas](./media/tutorial-geofence/events-tab.png)

2. Para criar uma assinatura de evento, selecione Assinatura de Evento na página de eventos.

   ![Assinatura de Eventos do Azure Mapas](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a assinatura de eventos e assine o tipo de evento Entrada. Agora, selecione o Web Hook como "Tipo de Ponto de Extremidade" e copie seu ponto de extremidade de URL HTTP do Aplicativo Lógico em "Ponto de Extremidade"

   ![Assinatura de eventos](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Usar a API de limite geográfico

Você pode usar a API de limite geográfico para verificar se um **dispositivo** (o equipamento é parte do status) está dentro ou fora de um limite geográfico. Para entender melhor a API GET de limite geográfico. Podemos consultá-lo para diferentes localizações pelas quais um determinado equipamento foi movido ao longo do tempo. A figura a seguir ilustra cinco localizações de um equipamento de construção específico com uma **ID do dispositivo** exclusiva, conforme observado em ordem cronológica. Cada uma dessas cinco localizações é usada para avaliar as alterações de status de entrada no limite geográfico e saída desse limite em relação ao limite propriamente dito. Se ocorrer uma alteração de estado, o serviço do limite geográfico disparará um evento, que será enviado para o Aplicativo Lógico pela Grade de Eventos. Como resultado, o gerente de operações receberá a notificação de entrada ou saída correspondente por email.

> [!Note]
> O cenário e o comportamento acima se baseiam na mesma **ID do dispositivo** para que ela reflita os cinco locais diferentes, conforme a figura abaixo.

![Mapa do limite geográfico](./media/tutorial-geofence/geofence.png)

No aplicativo Postman, abra uma nova guia na mesma coleção que você criou acima. Selecione o método HTTP GET na guia do compilador:

A seguir estão as cinco solicitações de API de limitação geográfica GET HTTP, com diferentes coordenadas de localização correspondentes do equipamento, conforme observadas em ordem cronológica. Cada solicitação é seguida pelo corpo da resposta.
 
1. Localização 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Consulta 1 do limite geográfico](./media/tutorial-geofence/geofence-query1.png)

   Se você examinar a resposta acima, a distância negativa do limite geográfico principal significará que o equipamento está dentro do limite geográfico e a distância positiva do limite geográfico do subsite indica que ele está fora do subsite do limite geográfico. 

2. Localização 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Consulta 2 do limite geográfico](./media/tutorial-geofence/geofence-query2.png)

   Se você examinar a resposta JSON anterior cuidadosamente, verá que o equipamento está fora do subsite, mas está dentro do limite principal. Ele não dispara um evento e nenhum email é enviado.

3. Localização 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta 3 do limite geográfico](./media/tutorial-geofence/geofence-query3.png)

   Ocorreu uma alteração de estado e agora o equipamento está dentro dos limites geográficos principal e de subsite. Isso publica um evento e uma notificação por email será enviada para o gerente de operações.

4. Localização 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Consulta 4 do limite geográfico](./media/tutorial-geofence/geofence-query4.png)

   Observando cuidadosamente a resposta correspondente, você pode observar que nenhum evento for publicado aqui, embora o equipamento tenha saído do limite geográfico do subsite. Se examinar a hora especificada do usuário na solicitação GET, você poderá ver que o limite geográfico do subsite expirou em relação ao seu horário e o equipamento ainda está no limite geográfico principal. Você também pode ver a ID de geometria do limite geográfico do subsite sob `expiredGeofenceGeometryId` no corpo da resposta.


5. Localização 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta 5 do limite geográfico](./media/tutorial-geofence/geofence-query5.png)

   Você pode ver que o equipamento saiu do limite geográfico do local de construção principal. Ele publica um evento, ele é uma violação séria e um email de alerta crítico é enviado ao gerente de operações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como configurar o limite geográfico carregando-o no serviço de dados do Azure Mapas usando a API de upload de dados. Você também aprendeu a usar a Grade de Eventos do Azure Mapas para assinar e manipular eventos de limite geográfico. 

* Confira [Manipular tipos de conteúdo em Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) para aprender a usar os Aplicativos Lógicos para analisar JSON a fim de criar uma lógica mais complexa.
* Para saber mais sobre os manipuladores de eventos na Grade de Eventos, confira [manipuladores de eventos compatíveis na Grade de Eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
