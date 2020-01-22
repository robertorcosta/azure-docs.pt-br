---
title: 'Tutorial: Migrar um aplicativo do Android | Microsoft Azure Mapas'
description: Como migrar um aplicativo Android do Google Maps para o Microsoft Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643d48cb931bcec1a8a3385d2ec24a394660c368
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909196"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrar um aplicativo Android do Google Maps

O SDK do Android do Azure Mapas tem uma interface de API que é muito semelhante à do SDK Web. Se você já desenvolveu com um desses SDKs, saiba que muitos dos mesmos conceitos, melhores práticas e arquiteturas se aplicam, permitindo que o conhecimento adquirido com qualquer um deles seja aplicável aos dois.

O SDK do Android do Azure Mapas dá suporte a uma versão mínima do Android de API 21: Android 5.0.0 (Lollipop).

Todos os exemplos são fornecidos em Java, no entanto, o Kotlin também podem ser usado com o SDK do Android do Azure Mapas.

Confira também os [Guias de instruções para o SDK do Android do Azure Mapas](how-to-use-android-map-control-library.md) para obter mais informações sobre como desenvolver com esse SDK.

## <a name="load-a-map"></a>Carregar um mapa

Ao carregar um mapa em um aplicativo Android usando o Google ou então o Azure Mapas, muitas das etapas são as mesmas. Ao usar qualquer um desses SDKs, você precisa:

- Obter uma API ou chave de assinatura para acessar uma das duas plataformas.
- Adicionar um XML a uma atividade para especificar em que local o mapa deve ser renderizado e como ele deve ser disposto.
- Encaminhar todos os métodos de ciclo de vida da atividade que contém a exibição do mapa para aqueles correspondentes na classe do mapa. Em particular, você precisa encaminhar os seguintes métodos:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Aguarde até que o mapa esteja pronto antes de tentar acessá-lo programaticamente.

**Antes: Google Maps**

Para exibir um mapa usando o SDK do Google Maps para Android, as etapas a seguir serão executadas:

1.  Verifique se os serviços do Google Play estão instalados.
2.  Adicione uma dependência para o serviço do Google Maps ao arquivo **gradle.build** do módulo: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Adicione uma chave de API do Google Maps dentro da seção do aplicativo do arquivo **google\_maps\_api.xml**:
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Adicione um fragmento de mapa à atividade principal:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  No arquivo **MainActivity.java**, você precisará adicionar importações para o SDK do Google Maps. Encaminhar todos os métodos de ciclo de vida da atividade que contém a exibição do mapa para aqueles correspondentes na classe do mapa. Usando o método `getMapAsync(OnMapReadyCallback)`, é possível recuperar uma instância de `MapView` do fragmento do mapa. O `MapView` inicializa automaticamente o sistema de mapas e a exibição. Edite o arquivo **MainActivity.java** da seguinte maneira:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Quando executado em um aplicativo, o controle de mapeamento será carregado conforme mostrado a seguir.

<center>

![Google Maps simples](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Depois: Azure Mapas**

Para exibir um mapa usando o SDK do Azure Mapas para Android, as etapas a seguir precisam ser executadas:

1. Abra o arquivo de nível superior **build.gradle** e adicione o código a seguir à seção do bloco **todos os projetos**, **repositórios**:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize o **app/build.gradle** e adicione a ele o seguinte código:
    
    1. Verifique se o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o código a seguir à seção do Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize seu bloco de dependências e adicione uma nova linha de dependência de implementação para o mais recente SDK do Android do Azure Mapas:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > O SDK do Android do Azure Mapas está sendo atualizado e aprimorado regularmente. Você pode ver a documentação [Introdução ao controle de mapeamento do Android](how-to-use-android-map-control-library.md) para obter o número de versão da implementação mais recente do Azure Mapas. Além disso, você pode definir o número de versão de "0.2" para "0+" para que ele sempre se dirija à versão mais recente.
    
    4. Vá para **Arquivo** na barra de ferramentas e clique em **Sincronizar Projeto com Arquivos Gradle**.
3. Adicione um fragmento de mapa à atividade principal (res \> layout \> activity\_main.xml):
    
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
            />
    </FrameLayout>
    ```

4. No arquivo **MainActivity.java**, você precisará:
    
    * adicionar importações para o SDK do Azure Mapas
    * definir as informações de autenticação do Azure Mapas
    * obter a instância de controle de mapeamento no método **onCreate**

    Definir as informações de autenticação globalmente na classe `AzureMaps` usando os métodos `setSubscriptionKey` ou `setAadProperties` faz com que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapeamento contém métodos próprios de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android, que deve ser chamado diretamente da atividade que o contém. Para que o aplicativo chame corretamente os métodos de ciclo de vida do controle de mapeamento, você deve substituir os métodos de ciclo de vida a seguir na atividade que contém o controle de mapeamento e chamar o respectivo método de controle de mapeamento. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Edite o arquivo **MainActivity.java** da seguinte maneira:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
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

Se você executar seu aplicativo, o controle de mapeamento será carregado conforme mostrado a seguir.

<center>

![Tutorial simples para Azure Mapas](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Observe que o controle do Azure Mapas dá suporte a uma maior redução de zoom e fornece uma melhor exibição de todo o mundo.

> [!TIP]
> Se você estiver usando um Android Emulator no Windows, o mapa poderá não ser renderizado devido a conflitos com o OpenGL e a renderização de gráficos acelerada por software. O procedimento a seguir funcionou para algumas pessoas resolverem esse problema. Abra o Gerenciador de AVD e selecione o dispositivo virtual a ser editado. Na seção **Desempenho Emulado**, defina a opção **Gráficos** para **Hardware**.

## <a name="localizing-the-map"></a>Como localizar o mapa

Se o público-alvo estiver espalhado em vários países ou falar idiomas diferentes, a localização será importante.

**Antes: Google Maps**

O idioma do mapa pode ser definido no método `onCreate` da atividade principal, adicionando o código a seguir antes de definir a exibição de contexto do mapa. A configuração a seguir limita o idioma ao francês pelo uso do código de idioma "fr".

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Aqui está um exemplo do Google Maps com o idioma definido como "fr".

<center>

![Localização do Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Depois: Azure Mapas**

O Azure Mapas fornece três maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é passar as informações de idioma e de exibição regional para a classe `AzureMaps` usando os métodos `setLanguage` e `setView` estáticos globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Mapas carregados em seu aplicativo. A configuração a seguir limita o idioma ao francês pelo uso do código de idioma "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A segunda opção é passar as informações de idioma e de exibição para o XML de controle de mapeamento.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programaticamente o idioma e a exibição regional do mapa usando o método `setStyle` dos mapas. Isso pode ser feito a qualquer momento para alterar o idioma e a exibição regional do mapa.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo do Azure Mapas com o idioma definido como "fr-FR".

<center>

![Localização do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="setting-the-map-view"></a>Como configurar a exibição do mapa

Chamando os métodos apropriados, é possível mover programaticamente mapas dinâmicos no Azure Mapas e no Google Maps para novas localizações geográficas. Os exemplos a seguir mostram como fazer com que o mapa exiba imagens aéreas de satélite, centralize o mapa em uma localização com coordenadas (latitude: 35,0272, longitude: -111,0225) e altere o nível de zoom para 15 no Google Maps.

> [!NOTE]
> O Google Maps usa blocos de 256 pixels, enquanto o Azure Mapas usa um tamanho de peça maior, de 512 pixels. Isso reduz o número de solicitações de rede necessárias para que o Azure Mapas carregue a mesma área do mapa que o Google Maps. No entanto, devido à maneira como as pirâmides de peças funcionam nos controles de mapeamento, as peças maiores no Azure Mapas significam que, ao usar o Azure Mapas, você precisa subtrair o nível de zoom usado no Google Maps em 1 para alcançar a mesma área visível que teria como um mapa no Google Maps.

**Antes: Google Maps**

A câmera do controle de mapeamento do Google Maps pode ser movida de maneira programática usando o método `moveCamera`, que permite especificar o centro do mapa e um nível de zoom. O método `setMapType` pode ser usado para alterar o tipo de mapa exibido.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Exibição de conjunto do Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Depois: Azure Mapas**

Conforme observado anteriormente, para obter a mesma área visível Azure Mapas, subtraia o nível de zoom usado no Google Maps por um. Neste caso, use um nível de zoom igual a 14.

A exibição de mapa inicial pode ser definida em atributos XML no controle de mapeamento.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

A exibição de mapa pode ser atualizada programaticamente usando os métodos `setCamera` e `setStyle` dos mapas.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Definição de exibição do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Recursos adicionais:**

- [Estilos de mapa compatíveis](supported-map-styles.md)

## <a name="adding-a-marker"></a>Adicionar um marcador

Os dados de ponto geralmente são renderizados usando uma imagem no mapa. Essas imagens são geralmente chamadas de marcadores, pinos, alfinetes ou símbolos. Os exemplos a seguir renderizam dados de ponto como marcadores no mapa em (latitude: 51,5, longitude: -0,2).

**Antes: Google Maps**

Com o Google Maps, os marcadores são adicionados usando o método `addMarker` dos mapas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Marcador do Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, os dados podem ser renderizados no mapa adicionando-se primeiro os dados a uma fonte de dados e, em seguida, conectando essa fonte de dados a uma camada de símbolo. A fonte de dados otimiza o gerenciamento de dados espaciais no controle de mapeamento e a camada de símbolo especifica como renderizar dados de ponto, usando-os como uma imagem e/ou texto.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Marcador do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. A imagem a seguir é usada nos exemplos abaixo, que usam uma imagem personalizada para exibir um ponto no mapa em (latitude: 51,5, longitude: -0,2) e desloca a posição do marcador para que o ponto do ícone de pino alinhe-se com a posição correta no mapa.

<center>

![imagem do pino amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

Em ambos os exemplos, a imagem acima é adicionada à pasta desenhável dos recursos de aplicativos.

**Antes: Google Maps**

Com o Google Maps, as imagens personalizadas podem ser usadas para marcadores ao carregá-las por meio da opção `icon` do marcador. Para alinhar o ponto da imagem com a coordenada, a opção `anchor` pode ser usada. A âncora é relativa às dimensões da imagem, que neste caso tem largura de 0,2 unidades e altura de 1 unidade.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Marcador personalizado do Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Depois: Azure Mapas**

As camadas de símbolo no Azure Mapas também dão suporte a imagens personalizadas, mas é preciso primeiro que a imagem seja carregada nos recursos de mapa e que uma ID exclusiva seja atribuída a ela. Em seguida, a camada de símbolo pode fazer referência a essa ID. O símbolo pode ser deslocado para alinhar-se ao ponto correto na imagem pelo uso da opção `iconOffset`. Observe que o deslocamento do ícone é em pixels. Por padrão, o deslocamento é relativo à parte central-inferior da imagem, mas isso pode ser ajustado usando a opção `iconAnchor`. Este exemplo define a opção `iconAnchor` como `"center"` e usa um deslocamento de ícone para mover a imagem em cinco pixels para a direita e 15 pixels para cima, a fim de alinhá-la com o ponto da imagem do pino.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Marcador personalizado do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Como adicionar uma polilinha

As polilinhas são usadas para representar uma linha ou um caminho no mapa. Os exemplos a seguir mostram como criar uma polilinha tracejada no mapa.

**Antes: Google Maps**

Com o Google Maps, uma polilinha pode ser criada usando a classe `PolylineOptions` e adicionada ao mapa usando o método `addPolyline`. A cor do traço pode ser definida usando a opção `color`, a largura do traço é definida usando a opção de largura e uma matriz de tracejado pode ser definida usando a opção `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Polilinha do Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, as polilinhas são chamadas de objetos LineString ou MultiLineString. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha. Observe que as unidades de "pixel" da largura do traço e da matriz de traço se alinham com o SDK Web do Azure Mapas, pois usar os mesmos valores em ambos os SDKs produz os mesmos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Polilinha do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Como adicionar um polígono

Os polígonos são usados para representar uma área no mapa. Os exemplos a seguir mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

**Antes: Google Maps**

Com o Google Maps, um polígono pode ser criado usando a classe `PolygonOptions` e adicionada ao mapa usando o método `addPolygon`. As cores de preenchimento e de traço podem ser definidas usando a opção `fillColor` e `strokeColor`, a largura do traço é definida usando a opção `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Polígono do Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Depois: Azure Mapas**

Nos Azure Mapas, os objetos Polygon e MultiPolygon podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada em uma camada do polígono. O contorno de um polígono pode ser renderizado usando uma camada de linhas. Observe que as unidades de "pixel" da largura do traço e da matriz de traço se alinham com o SDK Web do Azure Mapas, pois usar os mesmos valores em ambos os SDKs produz os mesmos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Polígono do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

As camadas de peça, também conhecidas como sobreposições de imagem no Google Maps, permitem que você sobreponha as imagens de camada que foram divididas em imagens com peças menores, que se alinham com o sistema de mosaico de mapas. Essa é uma maneira comum de sobrepor imagens de camada ou conjuntos de dados muito grandes.

Os exemplos a seguir sobrepõem uma camada de peça de radar meteorológico da Iowa Environmental Mesonet of Iowa State University. As peças têm tamanho de 256 pixels.

**Antes: Google Maps**

Com o Google Maps, uma camada de peça pode ser sobreposta ao mapa pelo uso da classe `TileOverlayOptions` e adicionada ao mapa pelo uso do método `addTileLauer`. Para tornar as peças semitransparentes, a opção `transparency` é definida como 0,2 ou 20% transparente.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Camada de peças do Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, uma camada de peças pode ser adicionada ao mapa praticamente da mesma forma que qualquer outra camada. Uma URL formatada que tem os espaços reservados x, y e de zoom (`{x}`, `{y}` e `{z}`, respectivamente) é usada para informar à camada onde acessar as peças. As camadas de peça no Azure Mapas também dão suporte aos espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`. Para tornar a camada de peça semitransparente, é usado um valor de opacidade de 0,8. Observe que a opacidade e a transparência, embora semelhantes, usam valores invertidos. Para converter entre eles, basta subtrair o valor do outro do número um.

> [!TIP]
> No Azure Mapas, as camadas podem ser facilmente renderizadas sob outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de peça abaixo dos rótulos de mapa para que elas sejam fáceis de ler. O método `map.layers.add` usa um segundo parâmetro que é a ID da camada na qual inserir a nova camada abaixo. Para inserir uma camada de peça abaixo dos rótulos de mapa, é possível usar o seguinte código: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Camada de peça do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Mostrar o tráfego

Os dados de tráfego podem ser sobrepostos ao Azure Mapas e ao Google Maps.

**Antes: Google Maps**

Com o Google Maps, é possível sobrepor os dados de fluxo de tráfego ao mapa, para isso passando true para o método `setTrafficEnabled` do mapa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Tráfego do Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Depois: Azure Mapas**

O Azure Mapas fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como bloqueios e acidentes na estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego, como estradas codificadas por cor, pode ser sobreposto ao mapa e as cores podem ser modificadas para se basearem na relação entre o limite de velocidade postado e o atraso, seja ele o normal esperado ou o atraso absoluto. Os dados de incidentes no Azure Mapas são atualizados a cada minuto e os dados de fluxo são atualizados a cada dois minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Tráfego do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o SDK do Android do Azure Mapas.

> [!div class="nextstepaction"]
> [Como usar o controle de mapeamento do Android](how-to-use-android-map-control-library.md)
