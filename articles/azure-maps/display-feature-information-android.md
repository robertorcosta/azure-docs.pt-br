---
title: Exibir informações sobre o recurso no Azure Maps Android SDK | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a exibir informações de recursos em um mapa usando o Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911684"
---
# <a name="display-feature-information"></a>Exibir informações do recurso

Os dados espaciais são frequentemente representados usando pontos, linhas e polígonos. Esses dados geralmente têm informações de metadados associadas a eles. Por exemplo, um ponto pode representar a localização de uma loja e metadados sobre esse restaurante pode ser seu nome, endereço e tipo de comida que ele serve. Esses metadados podem ser adicionados como `JsonObject`propriedades desses recursos usando um . O código a seguir cria `title` um recurso de ponto simples com uma propriedade que tem um valor de "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Quando um usuário interage com um recurso no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem feita das propriedades de metadados de um recurso com o qual o usuário interagiu. O `OnFeatureClick` evento é o principal evento usado para detectar quando o usuário grampeou um recurso no mapa. Há também um `OnLongFeatureClick` evento. Ao adicionar `OnFeatureClick` o evento ao mapa, ele pode ser limitado a uma única camada, passando no ID de uma camada para limitá-lo. Se nenhuma iD de camada for passada, tocar em qualquer recurso no mapa, independentemente de qual camada ele esteja, disparará este evento. O código a seguir cria uma camada de símbolo `OnFeatureClick` para renderizar dados de ponto no mapa, em seguida, adiciona um evento e limita-os a essa camada de símbolo.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Exibir uma mensagem de brinde

Uma mensagem de brinde é uma das maneiras mais fáceis de exibir informações para o usuário e está disponível em todas as versões do Android. Ele não suporta nenhum tipo de entrada do usuário e é exibido apenas por um curto período de tempo. Se você quiser rapidamente deixar o usuário saber algo sobre o que ele grampeou, uma mensagem de brinde pode ser uma boa opção. O código a seguir mostra como uma `OnFeatureClick` mensagem de brinde pode ser usada com o evento.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![Animação de um recurso sendo tocado e uma mensagem de brinde sendo exibida](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Além das mensagens de brinde, existem muitas outras maneiras de apresentar as propriedades de metadados de um recurso, tais como:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) - As lanchonetes fornecem feedback leve sobre uma operação. Eles mostram uma breve mensagem na parte inferior da tela no celular e inferior esquerdo em dispositivos maiores. As lanchonetes aparecem acima de todos os outros elementos na tela e apenas um pode ser exibido de cada vez.
- [Diálogos](https://developer.android.com/guide/topics/ui/dialogs) - Uma caixa de diálogo é uma pequena janela que solicita ao usuário que tome uma decisão ou insira informações adicionais. Uma caixa de diálogo não preenche a tela e normalmente é usada para eventos modais que exigem que os usuários tomem uma ação antes que eles possam continuar.
- Adicione um [Fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue para outra atividade ou visualização.

## <a name="next-steps"></a>Próximas etapas

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa Android](how-to-add-shapes-to-android-map.md)
