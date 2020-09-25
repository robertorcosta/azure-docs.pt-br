---
title: Introdução ao controle de mapa do Android | Microsoft Azure Mapas
description: Familiarize-se com o SDK do Android do Azure Maps. Veja como criar um projeto no Android Studio, instalar o SDK e criar um mapa interativo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2cda543781118a19ba4999e774b96ae81857f442
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330920"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao SDK do Android do Azure Mapas

O SDK do Android do Azure Mapas é uma biblioteca de mapas vetoriais para Android. Este artigo orienta você pelos processos de instalação do SDK do Android do Azure Mapas e de carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para concluir os procedimentos deste artigo, primeiro você precisa [Criar uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account) na faixa de preço S1 e [Obter a chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) da conta.

Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Baixar o Android Studio

Baixe o Android Studio e crie um projeto com uma atividade vazia, antes de instalar o SDK do Android do Azure Mapas. É possível [baixar o Android Studio](https://developer.android.com/studio/) gratuitamente pelo Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, crie um novo projeto com uma atividade vazia. Conclua estas etapas para criar um projeto do Android Studio:

1. Em **Escolher o projeto**, selecione **Telefone e Tablet**. O aplicativo será executado neste fator forma.
2. Na guia **Telefone e Tablet**, selecione **Atividade Vazia** e depois selecione **Próximo**.
3. Em **Configurar seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga com suporte pelo SDK do Android do Azure Mapas.
4. Aceite o padrão `Activity Name` e `Layout Name` e selecione **Concluir**.

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais ajuda com a instalação do Android Studio e criação de um novo projeto.

![Criar um projeto no Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite configurar um dispositivo Android virtual no computador. Esse procedimento pode ajudar a testar o aplicativo durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gerenciador de Dispositivo Virtual Android (AVD) no canto superior direito da tela do projeto e, em seguida, selecione **Criar Dispositivo Virtual**. Você também pode acessar o Gerenciador de AVD selecionando **Ferramentas** > **Android** > **Gerenciador de AVD** na barra de ferramentas. Na categoria **Telefones**, selecione **Nexus 5X** e clique em **Avançar**.

Você pode aprender mais sobre a configuração de um AVD na [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o SDK do Android do Azure Mapas

A próxima etapa na criação do aplicativo é instalar o SDK do Android do Azure Mapas. Conclua estas etapas para instalar o SDK:

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
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
    * obter a instância de controle de mapeamento no método **onCreate**

    Definir as informações de autenticação globalmente na classe `AzureMaps` usando os métodos `setSubscriptionKey` ou `setAadProperties` faz com que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapa contém os próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android. Esses métodos do ciclo de vida precisam ser chamados diretamente na Atividade relativa. Para que o aplicativo chame corretamente os métodos de ciclo de vida do controle de mapa, é necessário substituir os métodos de ciclo de vida a seguir na atividade que contém o controle de mapa. Além disso, você deve chamar o respectivo método de controle de mapa. 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
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

## <a name="import-classes"></a>Importar classes

Depois de concluir as etapas anteriores, provavelmente você receberá avisos do Android Studio sobre parte do código. Para resolver esses avisos, importe as classes referenciadas em `MainActivity.java`.

Você pode importar automaticamente essas classes selecionando Alt+Enter (Opção+Retornar em um Mac).

Selecione o botão Executar, conforme mostrado no gráfico a seguir (ou pressione Control+R em um Mac) para criar o aplicativo.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

O Android Studio levará alguns segundos para criar o aplicativo. Após concluir a criação do aplicativo, você poderá testá-lo no dispositivo Android emulado. Você verá um mapa semelhante a esse:

<center>

![Azure Mapas no aplicativo Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Como localizar o mapa

O SDK do Android do Azure Mapas fornece três maneiras diferentes de definir o idioma e a exibição regional do mapa. O seguinte código mostra como definir o idioma como francês ("fr-FR") e a exibição regional como "auto". 

A primeira opção é passar as informações de idioma e exibição regional para a classe `AzureMaps` usando os métodos `setLanguage` e `setView` estáticos globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Mapas carregados em seu aplicativo.

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

Aqui está um exemplo do Azure Mapas com o idioma definido como "fr-FR" e a exibição regional definida como "auto".

<center>

![Azure Mapas, imagem de mapa mostrando rótulos em francês](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="navigating-the-map"></a>Navegar no mapa

Há várias maneiras diferentes de ampliar, deslocar, girar e inclinar o mapa. A seguir, são detalhadas todas as diferentes maneiras de navegar no mapa.

**Ampliar o mapa**

- Toque no mapa com dois dedos e junte-os para reduzir, ou afaste-os para ampliar.
- Toque duas vezes no mapa para ampliar um nível.
- Toque duas vezes com dois dedos para ampliar o mapa um nível.
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
> [Adicionar formas a um mapa do Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa nos mapas do Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
