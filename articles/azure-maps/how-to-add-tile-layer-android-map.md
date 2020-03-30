---
title: Adicione uma camada de azulejo aos mapas do Android | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como renderizar uma camada de azulejo em um mapa usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335571"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Adicione uma camada de azulejo a um mapa usando o Azure Maps Android SDK

Este artigo mostra como renderizar uma camada de azulejo em um mapa usando o Azure Maps Android SDK. Camadas lado a lado permitem que você sobreponha imagens sobre peças de mapa base do Azure Mapas. Obtenha mais informações sobre o sistema de mosaico do Azure Mapas na documentação [Níveis de Zoom e grade lado a lado](zoom-levels-and-tile-grid.md).

Uma camada de azulejo é carregada em telhas de um servidor. Essas imagens podem ser pré-renderizadas e armazenadas como qualquer outra imagem em um servidor, usando uma convenção de nomeação que a camada de ladrilho entende. Ou, essas imagens podem ser renderizadas com um serviço dinâmico que gera as imagens quase em tempo real. Existem três convenções diferentes de nomeação de serviço de ladrilhos suportadas pela classe Azure Maps TileLayer:

* X, Y, notação de Zoom - com base no nível de zoom, x é a coluna e y é a posição da linha do bloco na grade lado a lado.
* Notação Quadkey - combinação x, y, informações de zoom em um valor de cadeia de caracteres única que é um identificador exclusivo para um bloco.
* Caixa delimitadora - as coordenadas da caixa de delimitação podem ser usadas para especificar uma imagem no formato `{west},{south},{east},{north}` que é normalmente usada por [serviços de mapeamento de Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um TileLayer é uma ótima maneira para visualizar grandes conjuntos de dados no mapa. Não apenas uma camada de bloco pode ser gerada de uma imagem, mas os dados de vetor podem também ser renderizados como uma camada de peça. Por meio do processamento de dados de vetor, como uma camada lado a lado, o controle de mapa só precisa carregar os blocos que podem ser muito menores em tamanho de arquivo do que os dados vetoriais que representam. Essa técnica é usada por muitas pessoas que precisam renderizar milhões de linhas de dados no mapa.

A URL do bloco passada para uma camada de peça deve ser uma URL http/https para um recurso de TileJSON ou um modelo de URL de bloco que usa os seguintes parâmetros: 

* `{x}` - posição X do bloco. Também precisa `{y}` e `{z}`.
* `{y}` - posição Y do bloco. Também precisa `{x}` e `{z}`.
* `{z}` -Nível de zoom do bloco. Também precisa `{x}` e `{y}`.
* `{quadkey}` - identificador quadkey de bloco baseado a convenção de nomenclatura do sistema de blocos Bing Maps.
* `{bbox-epsg-3857}` -Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}`- Um espaço reservado para os valores do subdomínio, se o valor do subdomínio for especificado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisa instalar [o Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="add-a-tile-layer-to-the-map"></a>Adicione uma camada de azulejo ao mapa

 Esta amostra mostra como criar uma camada de ladrilho que aponta para um conjunto de telhas. Estes azulejos usam o sistema de revestimento "x, y, zoom". A origem dessa camada lado a lado é uma sobreposição de radar clima da [Iowa Environmental Mesonet of Iowa State University](https://mesonet.agron.iastate.edu/ogc/). 

Você pode adicionar uma camada de azulejo ao mapa seguindo as etapas abaixo.

1. Editar **res > layout > activity_main.xml** para que pareça com o abaixo:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Copie o seguinte trecho de código abaixo no método `MainActivity.java` **onCreate()** da sua classe.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    O trecho de código acima obtém primeiro uma instância de controle de mapa do Azure Maps usando o método de retorno de chamada **onReady().** Em seguida, `TileLayer` ele cria um objeto e passa `tileUrl` uma URL de telha **xyz** formatado para a opção. A opacidade da camada `0.8` é definida para e como as telhas do serviço de ladrilho que está sendo usada são telhas de 256 pixels, essas informações são passadas para a `tileSize` opção. A camada de azulejo é então passada para o gerenciador de camadas de mapas.

    Depois de adicionar o trecho `MainActivity.java` de código acima, você deve se parecer com o abaixo:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
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

Se você executar sua aplicação agora, você deve ver uma linha no mapa como visto abaixo:

<center>

![Linha de mapas Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Próximas etapas

Veja o artigo a seguir para saber mais sobre como definir estilos de mapa

> [!div class="nextstepaction"]
> [Alterar os estilos do mapa em mapas android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)