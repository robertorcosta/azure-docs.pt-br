---
title: 'Tutorial: Criar uma cerca geográfica e controlar dispositivos em um mapa | Microsoft Azure Mapas'
description: Saiba como configurar um limite geográfico e acompanhar os dispositivos em relação ao limite geográfico usando o Serviço Espacial dos Microsoft Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 932dfb9624177c299997c4f9f184dc5c973d0fa0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899219"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Tutorial: Configurar um limite geográfico usando o Azure Mapas

Este tutorial orienta você pelas etapas básicas para configurar uma cerca geográfica usando o Azure Mapas. Considere esse cenário, um gerente de canteiro de obras precisa monitorar equipamentos perigosos potenciais. O gerente precisa garantir que os equipamentos permaneçam nas áreas de construção gerais escolhidas. Essa área de construção geral é um parâmetro rígido. As regulamentações exigem que os equipamentos permaneçam dentro desse parâmetro e as violações são relatadas ao Operations Manager.  

Usaremos a API de Upload de Dados para armazenar um limite geográfico e usar a API de Limite Geográfico para verificar a localização dos equipamentos em relação ao limite geográfico. A API de Upload de Dados e a API de Limite Geográfico são provenientes dos Azure Mapas. Também usaremos a Grade de Eventos do Azure para transmitir os resultados do limite geográfico e configurar uma notificação de acordo com os resultados desse limite. Para saber mais sobre a Grade de Eventos, confira [Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).

Neste tutorial, abordaremos como:

> [!div class="checklist"]
> * Carregue a área do limite geográfico no Azure Mapas, serviço de dados usando a API de Upload de Dados.
> *   Configure uma Grade de Eventos para manipular eventos de limite geográfico.
> *   Configure o manipulador de eventos do limite geográfico.
> *   Configure alertas em resposta a eventos do limite geográfico usando os Aplicativos Lógicos.
> *   Use APIs do serviço do limite geográfico do Azure Mapas para controlar se um ativo de construção está ou não dentro do local de construção.


## <a name="prerequisites"></a>Prerequisites

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure 

Siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) para criar uma assinatura da conta dos Azure Mapas no tipo de preço S1. As etapas em [Obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) mostram como recuperar a chave primária da sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Carregar limites geográficos

Supomos que o limite geográfico principal é subsite1, que tem um tempo de expiração definido. Você pode criar mais limites geográficos aninhados de acordo com suas necessidades. Esses conjuntos de limites podem ser usados para acompanhar diferentes áreas de construção dentro da área de construção geral. Por exemplo, subsite1 pode ser o local em que o trabalho está ocorrendo durante as semanas 1 a 4 da agenda. Subsite2 pode ser o local em que o trabalho ocorre durante as semanas 5 a 7. Todos esses limites podem ser carregados como um só conjunto de dados no início do projeto. Esses limites são usados para acompanhar as regras com base em tempo e espaço. 

Para carregar o limite geográfico do canteiro de obras usando a API de Upload de Dados, usaremos o aplicativo Postman. Instale o [aplicativo Postman](https://www.getpostman.com/) e crie uma conta gratuita. 

Depois que o aplicativo Postman for instalado, siga estas etapas para carregar o limite geográfico do canteiro de obras usando a API de Upload de Dados dos Azure Mapas.

1. Abra o aplicativo Postman e clique em novo | Criar novo e selecione Solicitar. Insira um nome de solicitação para o upload de dados de limite geográfico, selecione uma coleção ou uma pasta em que salvá-los e clique em Salvar.

    ![Carregar cercas geográficas usando Postman](./media/tutorial-geofence/postman-new.png)

2. Selecione o método HTTP POST na guia compilador e insira a URL a seguir para fazer uma solicitação POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    O parâmetro GEOJSON no caminho da URL representa o formato dos dados sendo carregados.

3. Clique em **Parâmetros** e insira os seguintes pares de chave/valor a serem usados para a URL da solicitação POST. Substitua {subscription-key} pela chave de assinatura dos Azure Mapas, também conhecida como chave primária.
   
    ![Parâmetros para carregar dados (cerca geográfica) no Postman](./media/tutorial-geofence/postman-key-vals.png)

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

5. Clique em enviar e revise o cabeçalho de resposta. Após uma solicitação bem-sucedida, o cabeçalho **Localização** conterá o URI de status. O URI de status estará no formato a seguir. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Copie o URI de status e acrescente a chave de assinatura. O formato do URI de status deverá ser semelhante ao mostrado abaixo. Observe que, no formato abaixo, você alterará a {subscription-key}, incluindo as { }, para a sua chave de assinatura.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Para obter a `udId`, abra uma nova guia no aplicativo Postman e selecione o método GET HTTP na guia do construtor. Faça uma solicitação GET no URI de status da etapa anterior. Se o upload de dados for bem-sucedido, você receberá a udId no corpo da resposta. Copie o udId para uso posterior.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Configurar um manipulador de eventos

Nesta seção, criaremos um manipulador de eventos que recebe notificações. Esse manipulador de eventos deverá notificar o Operations Manager sobre eventos de entrada e saída de qualquer equipamento.

Criamos dois serviços de [Aplicativos Lógicos](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) para manipular eventos de entrada e de saída. Quando os eventos nos Aplicativos Lógicos forem disparados, mais eventos serão disparados em sequência. A ideia é enviar alertas, nesse caso, emails para o Operations Manager. A figura a seguir ilustra a criação de um Aplicativo Lógico para o evento de entrada no limite geográfico. Da mesma forma, você pode criar outro para o evento de saída. Você pode ver todos os [manipuladores de eventos com suporte](https://docs.microsoft.com/azure/event-grid/event-handlers) para obter mais informações.

1. Criar um aplicativo lógico no portal do Azure

   ![Criar Aplicativos Lógicos do Azure para manipular eventos de cerca geográfica](./media/tutorial-geofence/logic-app.png)

2. No menu Configurações do Aplicativo Lógico, navegue até **Designer de Aplicativo Lógico**

3. Selecione um gatilho de solicitação HTTP e, em seguida, selecione "Nova Etapa". No Outlook Connector, selecione "Enviar um email" como uma ação
  
   ![Esquema dos Aplicativos Lógicos](./media/tutorial-geofence/logic-app-schema.png)

4. Preencha os campos para enviar um email. Mantenha a URL HTTP; ela será gerada automaticamente depois que você clicar em "Salvar"

   ![Gerar um ponto de extremidade de Aplicativos Lógicos](./media/tutorial-geofence/logic-app-endpoint.png)

5. Salve o Aplicativo Lógico para gerar o ponto de extremidade de URL HTTP e copie a URL HTTP.

## <a name="create-an-azure-maps-events-subscription"></a>Criar uma assinatura de Eventos do Azure Mapas

O Azure Mapas dá suporte a três tipos de evento. Você pode dar uma olhada nos tipos de evento compatíveis do Azure Mapas [aqui](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Precisamos de duas assinaturas de evento diferentes, uma para o evento de entrada e uma para os eventos de saída.

Siga as etapas abaixo para criar uma assinatura de evento para eventos de entrada no limite geográfico. Você pode assinar eventos de saída do limite geográfico de maneira semelhante.

1. Navegue até a conta dos Azure Mapas. No painel, selecione Assinaturas. Clique no nome da sua assinatura e selecione **Eventos** no menu Configurações.

   ![Navegue até os eventos da conta do Azure Mapas](./media/tutorial-geofence/events-tab.png)

2. Para criar uma assinatura de evento, selecione Assinatura de Evento na página de eventos.

   ![Criar uma assinatura de Eventos do Azure Mapas](./media/tutorial-geofence/create-event-subscription.png)

3. Nomeie a assinatura de eventos e assine o tipo de evento Entrada. Agora, selecione Web Hook como "Tipo de Ponto de Extremidade". Clique em "Selecionar um ponto de extremidade" e copie o ponto de extremidade da URL HTTP do Aplicativo Lógico para "{Endpoint}"

   ![Detalhes da assinatura de Eventos do Azure Mapas](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Usar a API de limite geográfico

Use a API de Limite Geográfico para verificar se um **dispositivo**, nesse caso, os equipamentos, está dentro ou fora de um limite geográfico. Vamos consultar a API GET de Limite Geográfico em diferentes localizações, para as quais determinado equipamento foi movido ao longo do tempo. A figura a seguir ilustra cinco localizações com cinco equipamentos de construção. 

> [!Note]
> O cenário e o comportamento se baseiam na mesma **identificação do dispositivo**, de modo que ela reflita as cinco localizações diferentes, como na figura abaixo.

A "deviceId" é uma ID exclusiva que você fornece para o dispositivo na solicitação GET, ao consultar a localização. Quando você faz uma solicitação assíncrona para o **limite geográfico de pesquisa – API GET**, a "deviceId" ajuda na publicação de eventos de limite geográfico para esse dispositivo, em relação ao limite geográfico especificado. Neste tutorial, fizemos solicitações assíncronas à API com uma "deviceId" exclusiva. As solicitações no tutorial são feitas em ordem cronológica, como no diagrama. A propriedade "isEventPublished" na resposta é publicada sempre que um dispositivo entra no limite geográfico ou sai dele. Você não precisa registrar um dispositivo para acompanhar este tutorial.

Vamos voltar ao diagrama. Cada uma dessas cinco localizações é usada para avaliar as alterações de status de entrada no limite geográfico e saída desse limite em relação ao limite propriamente dito. Se ocorrer uma alteração de estado, o serviço do limite geográfico disparará um evento, que será enviado para o Aplicativo Lógico pela Grade de Eventos. Como resultado, o gerente de operações receberá a notificação de entrada ou saída correspondente por email.

![Mapa de cerca geográfica no Azure Mapas](./media/tutorial-geofence/geofence.png)

No aplicativo Postman, abra uma nova guia na mesma coleção criada acima. Selecione o método HTTP GET na guia do compilador:

A seguir estão cinco solicitações de API HTTP GET de Delimitação Geográfica, com diferentes coordenadas de localização dos equipamentos. As coordenadas são observadas em ordem cronológica. Cada solicitação é seguida pelo corpo da resposta.
 
1. Localização 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Consulta 1 do limite geográfico](./media/tutorial-geofence/geofence-query1.png)

   Na resposta acima, a distância negativa do limite geográfico principal significa que os equipamentos estão dentro do limite geográfico. A distância positiva do limite geográfico do subsite significa que os equipamentos estão fora do limite geográfico do subsite. 

2. Localização 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Consulta 2 do limite geográfico](./media/tutorial-geofence/geofence-query2.png)

   Se você examinar a resposta JSON anterior cuidadosamente, verá que os equipamentos estão fora do subsite, mas dentro do limite principal. Nenhum evento é disparado e nenhum email é enviado.

3. Localização 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta 3 do limite geográfico](./media/tutorial-geofence/geofence-query3.png)

   Ocorreu uma alteração de estado e agora o equipamento está dentro dos limites geográficos principal e de subsite. Essa alteração faz com que um evento seja publicado e uma notificação por email será enviada ao Operations Manager.

4. Localização 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Consulta 4 do limite geográfico](./media/tutorial-geofence/geofence-query4.png)

   Observando cuidadosamente a resposta correspondente, você pode observar que nenhum evento for publicado aqui, embora o equipamento tenha saído do limite geográfico do subsite. Se você examinar a hora especificada do usuário na solicitação GET, poderá ver que o limite geográfico do subsite expirou para essa hora. O equipamento ainda está no limite geográfico principal. Você também pode ver a ID de geometria do limite geográfico do subsite sob `expiredGeofenceGeometryId` no corpo da resposta.


5. Localização 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Consulta 5 do limite geográfico](./media/tutorial-geofence/geofence-query5.png)

   Você pode ver que o equipamento saiu do limite geográfico do local de construção principal. Um evento é publicado e um email de alerta é enviado para o Operations Manager.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar o limite geográfico carregando-o nos Azure Mapas e no serviço Dados usando a API de Upload de Dados. Você também aprendeu a usar a Grade de Eventos do Azure Mapas para assinar e manipular eventos de limite geográfico. 

* Confira [Manipular tipos de conteúdo em Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) para aprender a usar os Aplicativos Lógicos para analisar JSON a fim de criar uma lógica mais complexa.
* Para saber mais sobre os manipuladores de eventos na Grade de Eventos, confira [manipuladores de eventos compatíveis na Grade de Eventos](https://docs.microsoft.com/azure/event-grid/event-handlers).
