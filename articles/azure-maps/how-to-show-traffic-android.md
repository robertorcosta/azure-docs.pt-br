---
title: Mostrar dados de tráfego no mapa do Android | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como exibir dados de tráfego em um mapa usando a SDK do Android de mapas de Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b07b520dec4e9149c6748777a119b68e56c65e9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126390"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Mostrar dados de tráfego no mapa usando o Azure Maps SDK do Android

Dados de fluxo e dados de incidentes são os dois tipos de dados de tráfego que podem ser exibidos no mapa. Este guia mostra como exibir os dois tipos de dados de tráfego. Os dados de incidentes consistem em dados de ponto e de linha para coisas como construções, fechamentos de estrada e acidentes. Os dados de fluxo mostram métricas sobre o fluxo de tráfego em trânsito.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder mostrar o tráfego no mapa, você precisa [criar uma conta do Azure](quick-demo-map-app.md#create-an-azure-maps-account)e [obter uma chave de assinatura](quick-demo-map-app.md#get-the-primary-key-for-your-account). Em seguida, você precisa instalar o [SDK do Android do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) e carregar um mapa.

## <a name="incidents-traffic-data"></a>Dados de tráfego de incidentes

Você precisará importar as seguintes bibliotecas para chamar `setTraffic` e `incidents` :

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 O trecho de código a seguir mostra como exibir dados de tráfego no mapa. Passamos um valor booliano para o `incidents` método e o passamos para o `setTraffic` método. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dados de tráfego de fluxo

Primeiro, você precisará importar as seguintes bibliotecas para chamar `setTraffic` e `flow` :

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Use o trecho de código a seguir para definir dados de fluxo de tráfego. Semelhante ao código na seção anterior, passamos o valor de retorno do `flow` método para o `setTraffic` método. Há quatro valores que podem ser passados para `flow` , e cada valor poderia ser disparado `flow` para retornar o respectivo valor. O valor de retorno de `flow` será passado como o argumento para `setTraffic` . Consulte a tabela abaixo para obter estes quatro valores:

|Valor do fluxo | Descrição|
| :-- | :-- |
| Do trafficflow. NONE | Não exibe dados de tráfego no mapa |
| Do trafficflow. RELATIVE | Mostra os dados de tráfego relativos à velocidade de fluxo livre da estrada |
| Do trafficflow. RELATIVE_DELAY | Exibe áreas que são mais lentas do que o atraso médio esperado |
| Do trafficflow. ABSOLUTE | Mostra a velocidade absoluta de todos os veículos em trânsito |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Mostrar dados de tráfego de incidente clicando em um recurso

Para obter os incidentes de um recurso específico, você pode usar o código a seguir. Quando um recurso é clicado, a lógica de código verifica incidentes e cria uma mensagem sobre o incidente. Uma mensagem é exibida na parte inferior da tela com os detalhes.

1. Primeiro, você precisa editar o **layout de > res > activity_main.xml**, para que ele se pareça com o mostrado abaixo. Você pode substituir os `mapcontrol_centerLat` , `mapcontrol_centerLng` e `mapcontrol_zoom` pelos valores desejados. Lembre-se de que o nível de zoom é um valor entre 0 e 22. No nível de zoom 0, o mundo inteiro se ajusta em um único bloco.

   ```XML
   <?xml version="1.0" encoding="utf-8"?>
   <FrameLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       >
    
       <com.microsoft.azure.maps.mapcontrol.MapControl
           android:id="@+id/mapcontrol"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Adicione o código a seguir ao seu arquivo **MainActivity. java** . O pacote é incluído por padrão, portanto, lembre-se de manter seu pacote na parte superior.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
           });
       }

       @Override
       public void onResume() {
           super.onResume();
           mapControl.onResume();
       }

       @Override
       protected void onStart(){
           super.onStart();
           mapControl.onStart();
       }

       @Override
       public void onPause() {
           super.onPause();
           mapControl.onPause();
       }

       @Override
       public void onStop() {
           super.onStop();
           mapControl.onStop();
       }

       @Override
       public void onLowMemory() {
           super.onLowMemory();
           mapControl.onLowMemory();
       }

       @Override
       protected void onDestroy() {
           super.onDestroy();
           mapControl.onDestroy();
       }

       @Override
       protected void onSaveInstanceState(Bundle outState) {
           super.onSaveInstanceState(outState);
           mapControl.onSaveInstanceState(outState);
       }
   }
   ```

3. Depois de incorporar o código acima em seu aplicativo, você poderá clicar em um recurso e ver os detalhes dos incidentes de tráfego. Dependendo dos valores de latitude, longitude e nível de zoom usados no arquivo de **activity_main.xml** , você verá resultados semelhantes à imagem a seguir:

   <center>

   ![Incidente-tráfego no mapa](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Próximas etapas

Exiba os guias a seguir para saber como adicionar mais dados ao mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas ao mapa do Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Exibir informações do recurso](display-feature-information-android.md)
