---
title: Defina um estilo de mapa usando o Azure Maps Android SDK| Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre as funcionalidades relacionadas ao estilo do Microsoft Azure Maps para o Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334361"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir estilo mapa usando Azure Maps Android SDK

Este artigo mostra duas maneiras de definir estilos de mapa usando o Azure Maps Android SDK. O Azure Maps tem seis estilos de mapas diferentes para escolher. Para obter mais informações sobre estilos de mapa suportados, consulte [estilos de mapa suportados no Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisa instalar [o Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="set-map-style-in-the-layout"></a>Definir o estilo do mapa no layout

Você pode definir um estilo de mapa no arquivo de layout para sua classe de atividades. Editar **res > layout > activity_main.xml**, para que pareça com o abaixo:

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
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

O `mapcontrol_style` atributo acima define o estilo do mapa para **grayscale_dark**. 

<center>

![grayscale_dark de estilo](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Definir o estilo do mapa na classe de atividade

O estilo do mapa pode ser definido na classe de atividade. Copie o seguinte trecho de código no método `MainActivity.java` **onCreate()** da sua classe. Este código definirá o estilo do mapa para **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![estilo-satélite-road-rótulos](./media/set-android-map-styles/satellite-road-labels.png)</center>