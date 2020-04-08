---
title: Adicione uma barra de ferramentas de desenho a um mapa | Mapas do Microsoft Azure
description: Neste artigo você aprenderá, como adicionar uma barra de ferramentas de desenho a um mapa usando o Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804666"
---
# <a name="drawing-tool-events"></a>Eventos de ferramentas de desenho

Ao usar ferramentas de desenho em um mapa, é útil reagir a certos eventos à medida que o usuário desenha no mapa. Esta tabela lista todos os eventos `DrawingManager` suportados pela classe.

| Evento | Descrição |
|-------|-------------|
| `drawingchanged` | Acionado quando qualquer coordenada em forma foi adicionada ou alterada. | 
| `drawingchanging` | Disparado quando qualquer coordenada de visualização de uma forma está sendo exibida. Por exemplo, este evento será acionado várias vezes à medida que uma coordenada é arrastada. | 
| `drawingcomplete` | Acionado quando uma forma terminou de ser desenhada ou retirada do modo de edição. |
| `drawingmodechanged` | Acionado quando o modo de desenho foi alterado. O novo modo de desenho é passado para o manipulador de eventos. |
| `drawingstarted` | Acionado quando o usuário começa a desenhar uma forma ou coloca uma forma no modo de edição.  |

O código a seguir mostra como funcionam os eventos no módulo Drawing Tools. Desenhe formas no mapa e observe como os eventos disparam.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos de ferramentas de desenho" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os eventos de ferramentas de<a href='https://codepen.io/azuremaps'>@azuremaps</a>desenho de <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>caneta</a> pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exemplos

Vamos ver alguns cenários comuns que usam os eventos de ferramentas de desenho.

### <a name="select-points-in-polygon-area"></a>Selecione pontos na área do polígono

Este código demonstra como monitorar um evento de formas de desenho de um usuário. Para este exemplo, o código monitora formas de polígonos, retângulos e círculos. Em seguida, ele determina quais pontos de dados no mapa estão dentro da área desenhada. O `drawingcomplete` evento é usado para ativar a lógica de seleção. Na lógica selecionada, o código faz loops em todos os pontos de dados do mapa. Ele verifica se há uma intersecção do ponto e da área da forma desenhada. Este exemplo faz uso da biblioteca [Turf.js](https://turfjs.org/) de código aberto para realizar um cálculo de intersecção espacial.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selecione dados na área de polígono desenhada" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os dados da Caneta <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select na área de polígono desenhada</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Desenhar e pesquisar na área do polígono

Este código procura pontos de interesse dentro da área de uma forma depois que o usuário terminou de desenhar a forma. Você pode modificar e executar o código clicando em 'Editar na caneta Código' no canto superior direito do quadro. O `drawingcomplete` evento é usado para ativar a lógica de busca. Se o usuário desenhar um retângulo ou polígono, uma pesquisa dentro da geometria será realizada. Se um círculo for desenhado, o raio e a posição central são usados para realizar uma pesquisa de ponto de interesse. O `drawingmodechanged` evento é usado para determinar quando o usuário muda para o modo de desenho, e este evento limpa a tela de desenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhar e pesquisar na área do polígono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Desenho da Caneta <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>e pesquise na área do polígono</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Crie uma ferramenta de medição

O código abaixo mostra como os eventos de desenho podem ser usados para criar uma ferramenta de medição. O `drawingchanging` é usado para monitorar a forma, como está sendo desenhado. À medida que o usuário move o mouse, as dimensões da forma são calculadas. O `drawingcomplete` evento é usado para fazer um cálculo final sobre a forma depois de ter sido sorteado. O `drawingmodechanged` evento é usado para determinar quando o usuário está mudando para um modo de desenho. Além disso, o `drawingmodechanged` evento limpa a tela de desenho e limpa informações antigas de medição.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ferramenta de medição" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a ferramenta De medição de<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>canetas</a> pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Próximas etapas

Saiba como usar recursos adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre o módulo Serviços:

> [!div class="nextstepaction"]
> [Módulo de serviços](how-to-use-services-module.md)

Confira mais exemplos de código:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
