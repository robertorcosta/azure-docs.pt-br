---
title: Começando com o controle de mapas do Android | Mapas do Microsoft Azure
description: Neste artigo você aprenderá, como começar com o controle de mapa do Android usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548549"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Começando com O Azure Maps Android SDK

O Azure Maps Android SDK é uma biblioteca de mapas vetoriais para Android. Este artigo guia você através dos processos de instalação do Azure Maps Android SDK e carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para concluir os procedimentos deste artigo, primeiro você precisa [criar uma conta do Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) no nível de preços S1 e obter a chave [principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) para sua conta.

Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Baixar Android Studio

Baixe o Android Studio e crie um projeto com uma atividade vazia antes de instalar o Azure Maps Android SDK. Você pode [baixar o Android Studio](https://developer.android.com/studio/) gratuitamente no Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, crie um novo projeto com uma atividade vazia. Complete essas etapas para criar um projeto do Android Studio:

1. Em **Escolha seu projeto,** selecione **Telefone e Tablet**. Sua aplicação será executada neste fator de formulário.
2. Na guia **Telefone e Tablet,** selecione **Atividade vazia**e selecione **Next**.
3. Em **Configurar seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga suportada pelo Azure Maps Android SDK.
4. Aceite o `Activity Name` `Layout Name` padrão e selecione **Terminar**.

Consulte a [documentação](https://developer.android.com/studio/intro/) do Android Studio para obter mais ajuda com a instalação do Android Studio e a criação de um novo projeto.

![Crie um projeto em estúdio Android ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite configurar um dispositivo Android virtual no computador. Fazer isso pode ajudá-lo a testar sua aplicação durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gerenciador de Dispositivos Virtuais (AVD) do Android no canto superior direito da tela do projeto e selecione **Criar dispositivo virtual**. Você também pode chegar ao AVD Manager selecionando **ferramentas** > **Android** > **AVD Manager** na barra de ferramentas. Na categoria **Telefones,** selecione **Nexus 5X**e selecione **Next**.

Você pode aprender mais sobre como configurar um AVD na [documentação](https://developer.android.com/studio/run/managing-avds)do Android Studio .

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instale o Azure Maps Android SDK

O próximo passo na construção do seu aplicativo é instalar o Azure Maps Android SDK. Complete estas etapas para instalar o SDK:

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
    * obter a instância de controle do mapa no método **onCreate**

    Definir as informações de autenticação globalmente na classe `AzureMaps` usando os métodos `setSubscriptionKey` ou `setAadProperties` faz com que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapa contém os próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android. Esses métodos de ciclo de vida devem ser chamados diretamente da Atividade que contém. Para que seu aplicativo chame corretamente os métodos do ciclo de vida do controle do mapa, você deve substituir os seguintes métodos de ciclo de vida na Atividade que contém o controle do mapa. E, você deve chamar o respectivo método de controle de mapa. 

    * onCreate (Pacote) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState (Pacote) 
    * onLowMemory() 

    Edite o arquivo **MainActivity.java** da seguinte maneira:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>Classes de importação

Depois de completar as etapas anteriores, você provavelmente receberá avisos do Android Studio sobre alguns dos códigos. Para resolver esses avisos, importe `MainActivity.java`as classes referenciadas em .

Você pode importar automaticamente essas classes selecionando Alt+Enter (Option+Return on a Mac).

Selecione o botão executar, conforme mostrado no gráfico a seguir (ou pressione Control+R em um Mac), para construir seu aplicativo.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

O Android Studio levará alguns segundos para construir o aplicativo. Depois que a compilação estiver concluída, você pode testar seu aplicativo no dispositivo Android emulado. Você deveria ver um mapa como este:

<center>

![Mapas do Azure no aplicativo para Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Como localizar o mapa

O Azure Maps Android SDK fornece três maneiras diferentes de definir o idioma e a visão regional do mapa. O código a seguir mostra como definir o idioma para francês ("fr-FR") e a visão regional para "auto". 

A primeira opção é passar o idioma `AzureMaps` e visualizar `setLanguage` informações `setView` regionais para a classe usando a estática e os métodos globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Maps carregados em seu aplicativo.

```Java
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

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programaticamente o idioma e a exibição regional do mapa usando o método `setStyle` dos mapas. Isso pode ser feito a qualquer momento para alterar o idioma e a exibição regional do mapa.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de Mapas Azure com o idioma definido como "fr-FR" e exibição regional definida como "auto".

<center>

![Mapas do Azure, imagem do mapa mostrando rótulos em francês](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="navigating-the-map"></a>Navegando pelo mapa

Existem várias maneiras diferentes em que o mapa pode ser ampliado, garimpado, girado e arremessado. O seguinte detalha todas as diferentes maneiras de navegar pelo mapa.

**Zoom no mapa**

- Toque no mapa com dois dedos e aperte juntos para diminuir o zoom ou espalhar os dedos para ampliar.
- Toque duas vezes no mapa para ampliar em um nível.
- Toque duas vezes com dois dedos para ampliar o mapa em um nível.
- Toque duas vezes; no segundo toque, segure o dedo no mapa e arraste para cima para ampliar, ou para baixo para diminuir o zoom.

**Pan o mapa**

- Toque no mapa e arraste em qualquer direção.

**Gire o mapa**

- Toque no mapa com dois dedos e gire.

**Lance o mapa**

- Toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar dados de sobreposição no mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo a um mapa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar os estilos do mapa em mapas android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
