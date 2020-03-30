---
title: Adicione uma camada de símbolo aos mapas do Android | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a renderizar dados de pontos em um mapa adicionando uma camada de símbolo a ele usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335584"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Adicione uma camada de símbolo a um mapa usando o Azure Maps Android SDK

Este artigo mostra como renderizar dados de ponto de uma fonte de dados como uma camada de símbolo em um mapa usando o Azure Maps Android SDK.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir completamente as etapas deste artigo, você precisa instalar [o Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Para adicionar um marcador no mapa usando a camada de símbolo, siga as etapas abaixo:

1. Editar**o layout** >  **res** > **activity_main.xml** para que pareça o seguinte XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Copie o seguinte trecho de código no método `MainActivity.java` **onCreate()** da sua classe.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    O trecho de código acima obtém primeiro uma instância de controle de mapa do Azure Maps usando o método de retorno de chamada **onReady().** Em seguida, cria um objeto de origem de dados usando a classe **DataSource** e adiciona-o ao mapa. Em seguida, adiciona **um Recurso** contendo uma geometria point a ele. Uma imagem de marcador vermelho é então definida como ícone para o símbolo. Uma **camada de símbolo** usa texto ou ícones para renderizar dados baseados em pontos envoltos na fonte de dados como símbolo no mapa. Uma camada de símbolo é então criada e a fonte de dados é passada para ela para renderizar e, em seguida, é adicionada às camadas do mapa.
    
    Depois de adicionar o trecho `MainActivity.java` de código acima, você deve se parecer com o abaixo:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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
    
Neste ponto, se você executar sua aplicação, você deve ver um marcador no mapa, como mostrado aqui:

<center>

![Marcador de mapa do Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Próximas etapas

Para adicionar mais coisas ao seu mapa, veja:

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Exibir informações do recurso](display-feature-information-android.md)