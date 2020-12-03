---
title: Introdução ao SDK do Android do Azure Mapas
description: Familiarize-se com o SDK do Android Microsoft Azure Maps. Veja como criar um projeto no Android Studio, instalar o SDK e criar um mapa interativo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531234"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao SDK do Android do Azure Mapas

O SDK do Android do Azure Mapas é uma biblioteca de mapas vetoriais para Android. Este artigo orienta você pelos processos de instalação do SDK do Android do Azure Mapas e de carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

1. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.
Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).
3. [Baixe e instale o Android Studio do Google](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Conclua estas etapas para criar um projeto do Android Studio:

1. Inicie o Android Studio.
2. Clique em **+ criar novo projeto**.
3. Na guia **telefone e Tablet** , clique em **atividade vazia**. Clique em **Avançar**.
4. Em **Configurar seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo.
5. Selecione `Java` como o idioma.
6. Aceite o padrão `Name` para o projeto. Clique em **Concluir**.

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais ajuda com a instalação do Android Studio e criação de um novo projeto.

![Criar um projeto no Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Configurar um dispositivo

Para testar seu aplicativo durante o desenvolvimento, você pode usar um telefone Android ou um emulador Android.

Para saber mais sobre como configurar um AVD (dispositivo virtual Android), consulte a [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o SDK do Android do Azure Mapas

A próxima etapa na criação do aplicativo é instalar o SDK do Android do Azure Mapas.

Conclua estas etapas para instalar o SDK:

1. Na guia projeto, expanda **gradle scripts**. Abra **Build. gradle (Project: My_Application)** e adicione o seguinte código à seção **todos os projetos** `repositories`  :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Abra **Build. gradle (módulo: My_Application)**.

3. Verifique se **minSdkVersion** na `defaultConfig` seção está na API 21 ou superior.

4. Adicione o código a seguir à seção do Android:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Adicione o seguinte código à seção `dependencies`:

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Clique em **arquivo** na barra de ferramentas principal e selecione **sincronizar projeto com arquivos gradle**.

7. Abra o `res > layout > activity_main.xml`. Clique em `Code` Exibir no canto superior direito. Adicione o seguinte XML dentro do `<androidx.constraintlayout.widget.ConstraintLayout>` elemento.
    
    ```XML
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

8. No `java > com.example.myapplication > MainActivity.java` arquivo, você precisará:

    * Adicione importações para o SDK do Azure Maps.
    * Defina as informações de autenticação do Azure Maps.
    * Obtenha a instância de controle de mapa no método **OnCreate** .

    Para evitar a adição de informações de autenticação para cada exibição de aplicativo, definiremos as informações de autenticação globalmente chamando `AzureMaps.setSubscriptionKey` . Você também pode chamar `AzureMaps.setAadProperties` , se desejar autenticar usando Azure Active Directory.

    O controle de mapa substitui os seguintes métodos de ciclo de vida da classe MainActivity. Esses métodos são responsáveis pelo gerenciamento do ciclo de vida do OpenGL do Android.

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    Edite o `MainActivity.java` arquivo da seguinte maneira:

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

>[!WARNING]
>Android Studio pode não ter importado as classes necessárias.  Como resultado, o código terá algumas referências não resolvidas. Para importar as classes necessárias, basta passar o mouse sobre cada referência não resolvida e pressionar `Alt + Enter` (Option + Return em um Mac).

O Android Studio levará alguns segundos para criar o aplicativo. Após concluir a criação do aplicativo, você poderá testá-lo no dispositivo Android emulado. Você verá um mapa semelhante a esse:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Mapas no aplicativo Android":::

## <a name="localizing-the-map"></a>Como localizar o mapa

O Azure Maps SDK do Android fornece três maneiras diferentes de definir as configurações regionais e de idioma do mapa.

1. Defina as configurações regionais e de idioma chamando métodos estáticos na classe AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Defina as configurações de idioma e regional no XML de controle de mapa.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Defina as configurações regionais e de idioma chamando métodos no controle de mapa. Essa opção permite que você altere as configurações durante o tempo de execução.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Aqui está um exemplo de mapas do Azure com o idioma definido como `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Mapas, imagem de mapa mostrando rótulos em francês":::

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="navigating-the-map"></a>Navegar no mapa

Há várias maneiras diferentes de ampliar, deslocar, girar e inclinar o mapa. A seguir, são detalhadas todas as diferentes maneiras de navegar no mapa.

**Ampliar o mapa**

- Toque no mapa com dois dedos e junte-os para reduzir, ou afaste-os para ampliar.
- Toque duas vezes no mapa para ampliar um nível.
- Toque duas vezes com dois dedos para reduzir o mapa um nível.
- Toque duas vezes. No segundo toque, mantenha seu dedo no mapa e arraste para cima para ampliar ou para baixo para reduzir.

**Deslocar o mapa**

- Toque no mapa e arraste em qualquer direção.

**Girar o mapa**

- Toque no mapa com dois dedos e gire.

**Inclinar o mapa**

- Toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar dados de sobreposição no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo a um mapa do Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa do Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa nos mapas do Android](./set-android-map-styles.md)