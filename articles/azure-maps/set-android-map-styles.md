---
title: Definir um estilo de mapa usando o Azure Maps SDK do Android
description: Aprenda duas maneiras de definir o estilo de um mapa. Consulte como usar os mapas de Microsoft Azure SDK do Android no arquivo de layout ou na classe de atividade para ajustar o estilo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532458"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir o estilo de mapa usando o Azure Maps SDK do Android

Este artigo mostra como definir estilos de mapa usando o SDK do Android do Azure Maps. O mapas do Azure tem seis estilos de mapas diferentes para escolher. Para obter mais informações sobre os estilos de mapa com suporte, consulte [estilos de mapa com suporte no mapas do Azure](./supported-map-styles.md).

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.
3. Baixe e instale o [SDK do Android do Azure Maps](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Definir o estilo de mapa no layout

Você pode definir um estilo de mapa no arquivo de layout para sua classe de atividade. Edite `res > layout > activity_main.xml` , portanto, é semelhante ao seguinte:

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

O `mapcontrol_style` atributo acima define o estilo do mapa como **grayscale_dark**.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Mapas do Azure, imagem do mapa mostrando o estilo como grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Definir o estilo de mapa na classe MainActivity

O estilo de mapa também pode ser definido na classe MainActivity. Abra o `java > com.example.myapplication > MainActivity.java` arquivo e copie o trecho de código a seguir para o método **OnCreate ()** . Esse código define o estilo de mapa como **satellite_road_labels**.

>[!WARNING]
>Android Studio pode não ter importado as classes necessárias.  Como resultado, o código terá algumas referências não resolvidas. Para importar as classes necessárias, basta passar o mouse sobre cada referência não resolvida e pressionar `Alt + Enter` (Option + Return em um Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Mapas do Azure, imagem do mapa mostrando o estilo como satellite_road_labels":::