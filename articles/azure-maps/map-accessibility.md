---
title: Crie um aplicativo de mapa acessível com o Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como construir um aplicativo com recursos de acessibilidade usando o Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473312"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Mais de 20% dos usuários de internet têm a necessidade de aplicações web acessíveis. Como tal, é importante ter certeza de que seu aplicativo foi projetado de forma que qualquer usuário possa usá-lo facilmente. Em vez de pensar na acessibilidade como um conjunto de tarefas a serem concluídas, pense nisso como parte de sua experiência geral do usuário. Quanto mais acessível o seu aplicativo, mais pessoas podem usá-lo. 

Quando se trata de conteúdo interativo rico como um mapa, algumas considerações comuns de acessibilidade são:
- Apoie o leitor de tela para usuários que têm dificuldade em ver o aplicativo web.
- Tenha vários métodos para interagir e navegar no aplicativo web, como mouse, toque e teclado.
- Certifique-se de que o contraste de cores é tal que as cores não se misturam e se tornam difíceis de distinguir umas das outras. 

O Web SDK do Azure Maps vem pré-construído com muitos recursos de acessibilidade, tais como:
- Descrições do leitor de tela quando o mapa se move e quando o usuário se concentra em um controle ou pop-up.
- Suporte ao mouse, toque e teclado.
- Suporte acessível de contraste de cores no estilo do roteiro.

Detalhes completos da conformidade de acessibilidade para todos os produtos Microsoft podem ser encontrados [aqui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Procure por "Azure Maps web" para encontrar o documento especificamente para o Web SDK do Azure Maps. 

## <a name="navigating-the-map"></a>Navegando pelo mapa

Existem várias maneiras diferentes em que o mapa pode ser ampliado, garimpado, girado e arremessado. O seguinte detalha todas as diferentes maneiras de navegar pelo mapa.

**Zoom no mapa**

- Usando um mouse, clique duas vezes no mapa para ampliar em um nível.
- Usando um mouse, role a roda para ampliar o mapa.
- Usando uma tela sensível ao toque, toque no mapa com dois dedos e aperte juntos para diminuir o zoom ou espalhar os dedos para ampliar.
- Usando uma tela sensível ao toque, toque duas vezes no mapa para ampliar em um nível.
- Com o mapa focado, use`+`o sinal Plus`=`( ) ou sinal Equals () para ampliar em um nível.
- Com o mapa focado, use o`-`sinal Minus,`_`Hyphen ( ) ou Sublinhado ( ) para diminuir um nível.
- Usando o controle de zoom com uma guia de mouse, toque ou teclado/digite teclas.
- Pressione e `Shift` segure o botão e pressione o botão esquerdo do mouse para baixo no mapa e arraste para desenhar uma área para ampliar o mapa.

**Pan o mapa**

- Usando um mouse, pressione para baixo com o botão esquerdo do mouse no mapa e arraste em qualquer direção.
- Usando uma tela sensível ao toque, toque no mapa e arraste em qualquer direção.
- Com o mapa focado, use as teclas de seta para mover o mapa.

**Gire o mapa**

- Usando um mouse, pressione para baixo com o botão direito do mouse no mapa e arraste para a esquerda ou para a direita. 
- Usando uma tela sensível ao toque, toque no mapa com dois dedos e gire.
- Com o mapa focado, use a tecla shift e as teclas de seta esquerda ou direita.
- Usando o controle de rotação com uma guia de mouse, toque ou teclado/digite teclas.

**Lance o mapa**

- Usando o mouse, pressione para baixo com o botão direito do mouse no mapa e arraste para cima ou para baixo. 
- Usando uma tela sensível ao toque, toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.
- Com o mapa focado, use a tecla shift mais as teclas de seta para cima ou para baixo. 
- Usando o controle de tom com uma guia de mouse, toque ou teclado/digite teclas.

## <a name="change-the-map-style"></a>Alterar o estilo do mapa

Nem todos os desenvolvedores querem que todos os estilos de mapa possíveis estejam disponíveis em seu aplicativo. Se o desenvolvedor exibir o controle do seletor de estilo do mapa, o usuário poderá alterar o estilo do mapa usando o mouse, um toque ou o teclado com a guia ou digitar a tecla. O desenvolvedor pode especificar quais estilos de mapa eles querem disponibilizar no controle do seletor de estilo do mapa. Além disso, o desenvolvedor pode definir e alterar o estilo do mapa.

**Usar alto contraste**

- Quando o controle do mapa é carregado, ele verifica se o alto contraste está ativado e o navegador o suporta.
- O controle do mapa não monitora o modo de alto contraste do dispositivo. Se o modo do dispositivo mudar, o mapa não. Assim, o usuário precisará recarregar o mapa atualizando a página.
- Quando o alto contraste for detectado, o estilo do mapa mudará automaticamente para alto contraste, e todos os controles incorporados usarão um estilo de alto contraste. Por exemplo, ZoomControl, PitchControl, CompassControl, StyleControl e outros controles incorporados usarão um estilo de alto contraste.
- Há dois tipos de alto contraste, claro e escuro. Se o tipo de alto contraste pode ser detectado pelos controles do mapa, então o comportamento do mapa será ajustado de acordo. Se a luz, então o estilo do mapa grayscale_light será carregado. Se o tipo não pode ser detectado ou está escuro, então o estilo high_contrast_dark será carregado.
- Se criar controles personalizados, é útil saber se os controles incorporados estão usando um estilo de alto contraste. Os desenvolvedores podem adicionar uma classe css na div do contêiner do mapa para verificar. As classes css que `high-contrast-dark` seriam `high-contrast-light`adicionadas são e . Para verificar usando JavaScript, use:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

ou, use:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Atalhos do teclado

O mapa tem uma série de atalhos de teclado incorporados que facilitam o uso do mapa. Esses atalhos de teclado funcionam quando o mapa tem foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `Tab` | Navegue pelos controles e popups no mapa. |
| `ESC` | Mova o foco de qualquer elemento no mapa para o elemento de mapa de nível superior. |
| `Ctrl` + `Shift` + `D` | Alternar o nível de detalhes do leitor de tela.  |
| Tecla de seta para a esquerda | Pan o mapa deixou 100 pixels |
| Tecla de seta para a direita | Pan o mapa direito 100 pixels |
| Seta para baixo | Pan o mapa para baixo 100 pixels |
| Tecla de seta para cima | Pan o mapa até 100 pixels |
| `Shift`+ seta para cima | Aumentar o tom do mapa em 10 graus |
| `Shift`+ seta para baixo | Diminuir o tom do mapa em 10 graus |
| `Shift`+ seta direita | Gire o mapa 15 graus no sentido horário |
| `Shift`+ seta esquerda | Gire o mapa 15 graus no sentido anti-horário |
| Mais sinal`+`() ou <sup>*</sup>`=`sinal equal () | Ampliar |
| Menos sinal, Hífen (`-`) ou <sup>*</sup>Sublinhado (`_`) | Reduzir | 
| `Shift`+ arraste o mouse no mapa para desenhar área | Zoom na área |

<sup>*</sup>Esses atalhos-chave geralmente compartilham a mesma tecla em um teclado. Esses atalhos foram adicionados para melhorar a experiência do usuário. Também não importa se o usuário usa a chave de turno ou não para esses atalhos.

## <a name="screen-reader-support"></a>Suporte ao Leitor de Tela

Os usuários podem navegar o mapa usando o teclado. Se um leitor de tela estiver em execução, o mapa notificará o usuário de alterações em seu estado. Por exemplo, os usuários serão notificados das alterações do mapa quando o mapa é deslocado ou ampliado. Por padrão, o mapa fornece descrições simplificadas que excluem o nível de zoom e coordenadas do centro do mapa. O usuário pode alternar o nível de detalhe dessas `Ctrl`  +  `Shift`  +  `D`descrições usando o atalho do teclado .

Qualquer informação adicional é colocada no mapa de base deve ter informações textuais correspondentes para os usuários de leitores de tela. Certifique-se de adicionar [aplicações acessíveis de Internet rica (ARIA),](https://www.w3.org/WAI/standards-guidelines/aria/)alt e atributos de título, quando apropriado. 

## <a name="make-popups-keyboard-accessible"></a>Tornar popups de teclado acessível

Um marcador ou símbolo é frequentemente usado para representar um local no mapa. Informações adicionais sobre o local são normalmente exibidas em um pop-up quando o usuário interage com o marcador. Na maioria dos aplicativos, popups aparecem quando um usuário clica ou toca em um marcador. No entanto, clicar e tocar exige que o usuário use um mouse e uma tela sensível ao toque, respectivamente. Uma boa prática é tornar popups acessíveis ao usar um teclado. Essa funcionalidade pode ser alcançada criando um popup para cada ponto de dados e adicionando-o ao mapa. 

O exemplo a seguir carrega pontos de interesse no mapa usando uma camada de símbolo e adiciona um popup ao mapa para cada ponto de interesse. Uma referência a cada pop-up é armazenada nas propriedades de cada ponto de dados. Ele também pode ser recuperado para um marcador, como quando um marcador é clicado. Quando focado no mapa, pressionar a tecla de guia permitirá que o usuário passe por cada pop-up no mapa.

<br/>

<iframe height='500' scrolling='no' title='Criar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Criar um aplicativo acessível</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dicas adicionais de acessibilidade

Aqui estão algumas dicas adicionais para tornar seu aplicativo de mapeamento web mais acessível.

- Se exibir muitos dados de ponto interativos no mapa, considere reduzir a desordem e usar clustering. 
- Garantir que a relação de contraste de cores entre texto/símbolos e cores de fundo seja 4.5:1 ou mais.
- Mantenha as mensagens do leitor de tela (ARIA, alt e title) curtas, descritivas e significativas. Evite jargões e siglas desnecessárias.
- Tente otimizar as mensagens enviadas ao leitor de tela para fornecer informações de curto significado que são fáceis de digerir. Por exemplo, se você quiser atualizar o leitor de tela em alta frequência, como quando o mapa estiver se movendo, considere fazer os seguintes pontos:
    - Aguarde até que o mapa tenha terminado de se mover para atualizar o leitor de tela.
    - Estrangule as atualizações para uma vez a cada poucos segundos. 
    - Combine mensagens juntas de uma forma lógica. 
- Evite usar cores como o único meio de transmitir informações. Use texto, ícones ou padrões para complementar ou substituir a cor. Algumas considerações:
    - Se usar uma camada de bolha para mostrar o valor relativo entre os pontos de dados, considere dimensionar o raio de cada bolha, colorindo a bolha ou ambos. 
    - Considere usar uma camada de símbolo com diferentes ícones para diferentes categorias métricas, como triângulos, estrelas e quadrados. A camada de símbolo também suporta dimensionamento do tamanho do ícone. Um rótulo de texto também pode ser exibido.
    - Se exibir dados da linha, a largura pode ser usada para representar peso ou tamanho. Um padrão de matriz de traços pode ser usado para representar diferentes categorias de linhas. Uma camada de símbolo pode ser usada em combinação com uma linha para sobrepor ícones ao longo da linha. Usar um ícone de seta é útil para mostrar o fluxo ou a direção da linha.
    - Se exibir dados do polígono, um padrão, como listras, pode ser usado como uma alternativa à cor. 
- Algumas visualizações, como mapas de calor, camadas de azulejo sustais e camadas de imagem não são acessíveis para usuários com deficiência de visão. Algumas considerações:
    - Mande o leitor de tela descrever o que a camada está exibindo quando adicionada ao mapa. Por exemplo, se uma camada de telha de radar meteorológico for exibida, então o leitor de tela diga "Os dados do radar meteorológico são sobrepostos no mapa".
- Limitar a quantidade de funcionalidade que requer um mouse pairando. Essas funcionalidades serão inacessíveis para usuários que estão usando um teclado ou dispositivo de toque para interagir com seu aplicativo. Note que ainda é uma boa prática ter um estilo de hover para conteúdo interativo, como ícones clicáveis, links e botões.
- Tente navegar pelo aplicativo usando o teclado. Certifique-se de que o pedido de aba é lógico.
- Se criar atalhos de teclado, tente limitá-lo a duas teclas ou menos. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre acessibilidade nos módulos Web SDK.

> [!div class="nextstepaction"]
> [Acessibilidade de ferramentas de desenho](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre o desenvolvimento de aplicativos acessíveis com o Microsoft Learn:

> [!div class="nextstepaction"]
> [Acessibilidade em Ação Caminho de Aprendizagem de Crachá Digital](https://ready.azurewebsites.net/learning/track/2940)

Dê uma olhada nessas ferramentas de acessibilidade úteis:
> [!div class="nextstepaction"]
> [Desenvolvimento de aplicativos acessíveis](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Visão geral do WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Ferramenta de avaliação de acessibilidade da Web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verificador de contraste de cores WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Sem simulador de visão de café](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
