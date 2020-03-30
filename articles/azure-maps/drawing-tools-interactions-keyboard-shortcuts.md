---
title: Desenho de ferramentas de interação tipos e atalhos de teclado no mapa | Mapas do Microsoft Azure
description: Como desenhar e editar formas usando um mouse, tela sensível ao toque ou teclado no Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198285"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipos de interação e atalhos de teclado no módulo de ferramentas de desenho

Este artigo descreve todas as diferentes maneiras de desenhar e editar formas usando um mouse, tela sensível ao toque ou atalhos de teclado.

O gerenciador de desenho suporta três maneiras diferentes de interagir com o mapa, para desenhar formas.

* `click`- Coordenadas são adicionadas quando o mouse ou o toque são clicados.
* `freehand `- As coordenadas são adicionadas quando o mouse ou o toque são arrastados no mapa.
* `hybrid`- As coordenadas são adicionadas quando o mouse ou o toque são clicados ou arrastados.

## <a name="how-to-draw-shapes"></a>Como desenhar formas

 Antes que qualquer forma possa `drawingMode` ser desenhada, defina a opção do gerenciador de desenho como uma configuração de desenho suportada. Esta configuração pode ser programada ou invocada pressionando um dos botões de desenho na barra de ferramentas. O modo de desenho permanece ativado, mesmo depois de uma forma ter sido desenhada, facilitando a desenhação de formas adicionais do mesmo tipo. Programáticamente defina o modo de desenho para um estado ocioso. Ou, mude para um estado ocioso clicando no botão de modos de desenho atual na barra de ferramentas.

As próximas seções descrevem todas as diferentes maneiras que as formas podem ser desenhadas no mapa.

### <a name="how-to-draw-a-point"></a>Como desenhar um ponto

Quando o gerenciador `draw-point` de desenho estiver no modo de desenho, as seguintes ações podem ser feitas para desenhar pontos no mapa. Esses métodos funcionam com todos os modos de interação.

**Comece a desenhar**
 - Clique no botão esquerdo do mouse ou toque no mapa para adicionar um ponto ao mapa. 
 - Se o mouse estiver sobre `F` o mapa, pressione a tecla e um ponto será adicionado na coordenada do ponteiro do mouse. Este método fornece maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no mouse devido ao movimento de pressão do botão esquerdo do mouse.
 - Continue clicando, tocando `F` ou pressionando para adicionar mais pontos ao mapa.
 
**Terminar o desenho**
 - Clique em qualquer botão na barra de ferramentas de desenho. 
 - Programáticamente defina o modo de desenho. 
 - Pressione `C` a tecla.

**Cancelar o desenho**
 - Pressione `Escape` a tecla.

### <a name="how-to-draw-a-line"></a>Como desenhar uma linha

Quando o gerenciador `draw-line` de desenho está no modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação.

**Comece a desenhar**
 - Modo de clique
   * Clique no botão esquerdo do mouse ou toque no mapa para adicionar cada ponto de uma linha no mapa. Uma coordenada é adicionada à linha para cada clique ou toque. 
   * Se o mouse estiver sobre `F` o mapa, pressione a tecla e um ponto será adicionado na coordenada do ponteiro do mouse. Este método fornece maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no mouse devido ao movimento de pressão do botão esquerdo do mouse.
   * Continue clicando até que todos os pontos desejados tenham sido adicionados à linha.
 - Modo à mão livre
   * Pressione o botão esquerdo do mouse ou toque no mapa e arraste o mouse ou toque ao redor. Coordenadas são adicionadas à linha à medida que o mouse ou ponto de toque se move ao redor do mapa. Assim que o mouse ou o evento de retoque for acionado, o desenho será concluído. A freqüência na qual as coordenadas `freehandInterval` são adicionadas é definida pela opção de gerentes de desenho.
 - Modo híbrido
   * Alternar entre os métodos de clique e à mão livre, conforme desejado, enquanto desenha uma única linha. Por exemplo, clique em alguns pontos, em seguida, segure e arraste o mouse para adicionar um monte de pontos, em seguida, clique em mais alguns. 

**Terminar o desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Programáticamente defina o modo de desenho. 
 - Modo à mão livre
   * Solte o botão do mouse ou o ponto de toque.
 - Pressione `C` a tecla.

**Cancelar o desenho**
 - Pressione `Escape` a tecla.

### <a name="how-to-draw-a-polygon"></a>Como desenhar um polígono

Quando o gerenciador `draw-polygon` de desenho está no modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação.

**Comece a desenhar**
 - Modo de clique
   * Clique no botão esquerdo do mouse ou toque no mapa para adicionar cada ponto de um polígono no mapa. Uma coordenada é adicionada ao polígono para cada clique ou toque. 
   * Se o mouse estiver sobre `F` o mapa, pressione a tecla e um ponto será adicionado na coordenada do ponteiro do mouse. Este método fornece maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no mouse devido ao movimento de pressão do botão esquerdo do mouse.
   * Continue clicando até que todos os pontos desejados tenham sido adicionados ao polígono.
 - Modo à mão livre
   * Pressione o botão esquerdo do mouse ou toque no mapa e arraste o mouse ou toque ao redor. Coordenadas são adicionadas ao polígono à medida que o mouse ou ponto de toque se move ao redor do mapa. Assim que o mouse ou o evento de retoque for acionado, o desenho será concluído. A freqüência na qual as coordenadas `freehandInterval` são adicionadas é definida pela opção de gerentes de desenho.
 - Modo híbrido
   * Alternar entre os métodos de clique e à mão livre, conforme desejado, enquanto desenha um único polígono. Por exemplo, clique em alguns pontos, em seguida, segure e arraste o mouse para adicionar um monte de pontos, em seguida, clique em mais alguns. 

**Terminar o desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique no primeiro ponto do polígono.
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Programáticamente defina o modo de desenho. 
 - Modo à mão livre
   * Solte o botão do mouse ou o ponto de toque.
 - Pressione `C` a tecla.

**Cancelar o desenho**
 - Pressione `Escape` a tecla.

### <a name="how-to-draw-a-rectangle"></a>Como desenhar um retângulo

Quando o gerenciador `draw-rectangle` de desenho está no modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação GeoJSON estendida para retângulos](extend-geojson.md#rectangle).

**Comece a desenhar**
 - Pressione o botão esquerdo do mouse ou toque para baixo no mapa para adicionar o primeiro canto do retângulo e arraste para criar o retângulo. 

**Terminar o desenho**
 - Solte o botão do mouse ou o ponto de toque.
 - Programáticamente defina o modo de desenho. 
 - Pressione `C` a tecla.

**Cancelar o desenho**
 - Pressione `Escape` a tecla.

### <a name="how-to-draw-a-circle"></a>Como desenhar um círculo

Quando o gerenciador `draw-circle` de desenho está no modo, as seguintes ações podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação GeoJSON estendida para círculos](extend-geojson.md#circle).

**Comece a desenhar**
 - Pressione o botão esquerdo do mouse ou toque para baixo no mapa para adicionar o centro do círculo e arraste dê um raio aos círculos. 

**Terminar o desenho**
 - Solte o botão do mouse ou o ponto de toque.
 - Programáticamente defina o modo de desenho. 
 - Pressione `C` a tecla.

**Cancelar o desenho**
 - Pressione `Escape` a tecla.

## <a name="keyboard-shortcuts"></a>Atalhos do teclado

As ferramentas de desenho suportam atalhos de teclado. Esses atalhos de teclado são funcionais quando o mapa está em foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `C` | Completa qualquer desenho que esteja em andamento e define o modo de desenho como ocioso. O foco se moverá para o elemento de mapa de alto nível.  |
| `Escape` | Cancela qualquer desenho que esteja em andamento e define o modo de desenho como ocioso. O foco se moverá para o elemento de mapa de alto nível.  |
| `F` | Adiciona uma coordenada a um ponto, linha ou polígono se o mouse estiver sobre o mapa. Ação equivalente de clicar no mapa quando no modo clique ou híbrido. Este atalho permite desenhos mais precisos e mais rápidos. Você pode usar uma mão para posicionar o mouse e outra para pressionar o botão sem mover o mouse do gesto de pressão. |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as aulas no módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Gerente de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
