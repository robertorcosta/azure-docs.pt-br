---
title: Configurar materiais de renderização baseada fisicamente no Maya
description: Explica como configurar materiais de renderização baseada fisicamente no Maya e exportá-los para o formato FBX
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977348"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Tutorial: Configurar materiais de renderização baseada fisicamente no Maya

## <a name="overview"></a>Visão geral
Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * atribua materiais com o modelo de iluminação avançada a objetos na cena.
> * manipule a instanciação de objetos e materiais.
> * exporte uma cena para o formato FBX e as opções importantes a serem selecionadas.

Criar [materiais PBR (renderização baseada fisicamente)](../../overview/features/pbr-materials.md) em `Maya` é uma tarefa relativamente simples, semelhante em muitas maneiras a configurar o PBR em outros aplicativos de criação de conteúdo, como o `3DS Max`. O tutorial a seguir é um guia para a configuração básica do sombreador de PBR e a exportação do FBX para projetos ARR. 

A cena de exemplo neste tutorial contém um vários objetos `Polygon Box` que foram atribuídos a diversos materiais diferentes: madeira, metal, metal pintado, plástico e borracha. Em geral, cada material contém todas ou a maioria das texturas a seguir 

* `Albedo`, é o mapa de cores do material, também chamado de `Diffuse` ou `BaseColor`
* `Metalness`, que determina se um material é metálico e quais partes são metálicas. 
* `Roughness`, que determina quão áspera ou lisa é uma superfície, afetando a nitidez ou o desfoque dos reflexos e dos realces em uma superfície.
* `Normals`, que adiciona detalhes a uma superfície, por exemplo, amassados e recuos em uma superfície de metal ou granulação em madeira sem precisar adicionar mais polígonos.
* `Ambient Occlusion`, que é usado para adicionar sombreamento suave e sombras de contato a um modelo. É um mapa em escala de cinza que indica quais áreas de um modelo recebem iluminação total (branco) ou sombreamento total (preto). 

## <a name="prerequisites"></a>Pré-requisitos
* `Autodesk Maya 2017` ou mais recente

## <a name="setting-up-materials-in-the-scene"></a>Como configurar materiais na cena
No Maya, o processo de configuração de um material do PBR é o seguinte:

Para começar, como você verá na cena de exemplo, criamos vários objetos de Caixa, cada um deles representando um tipo de material. Observe, conforme mostra a imagem abaixo, que cada um desses objetos recebeu um nome adequado 

> Vale a pena observar, antes de começar a criar ativos para a ARR (Azure Remote Rendering), que ele usa medidores para medição e a direção para cima é eixo Y. Portanto, é aconselhável definir suas unidades de cena no Maya como metros. Além disso, ao exportar, é aconselhável definir as unidades para os medidores nas configurações de Exportação do FBX. 

> [!TIP]
É uma boa prática dar um nome adequado aos ativos de modelo, normalmente com a parte relevante ou o tipo de material, pois os nomes facilitam a navegação em cenas com muitos objetos.

![Nomes de objetos](media/object-names.jpg)

Depois de criar/adquirir suas texturas, dependendo de suas necessidades, convém criar texturas exclusivas para um modelo em aplicativos de texturização, como `Quixel Suite`, `Photoshop` ou `Substance Suite`, ou você pode usar texturas de blocos gráficos genéricos de outras fontes, que podem ser aplicadas ao seu modelo da seguinte maneira:

* No visor da cena, selecione o modelo/geometria e clique nele com o botão direito do mouse. No menu que aparece, clique em `Assign New Material`
* Nas opções de `Assign New Material`, vá para `Maya`>`Stingray PBS`. Esta ação atribuirá um material do PBR ao seu modelo. 

Em `Maya 2020`, há vários sombreadores de PBR disponíveis: `Maya Standard Surface`, `Arnold Standard Surface` e `Stingray PBR`. O `Maya Standard Surface Shader` ainda não pode ser exportado por meio do `FBX plugin 2020`, enquanto o `Arnold Standard Surface Shader` pode ser exportado com arquivos FBX. Na maioria dos outros aspectos, ele é idêntico ao `Maya Standard Surface Shader` e análogo ao `Physical Material` no `3D Studio Max`.

O **`The Stingray PBR Shader`** é compatível com muitos outros aplicativos e assemelha-se mais aos requisitos de `ARR` e tem suporte desde `Maya 2017`. Também é conveniente que esse tipo de material seja visualizado em um visor semelhante ao que será visualizado posteriormente no ARR.

![Material "arraia"](media/stingray-material.jpg)

Com o material atribuído ao seu ativo e com um nome adequado, agora você pode continuar atribuindo suas várias texturas. As imagens a seguir detalham o ponto em que cada tipo de textura se encaixa no material do PBR. O material `Stingray PBR` permite selecionar quais atributos você pode ativar, portanto, para `plug in` seus mapas de textura, é necessário ativar os atributos relevantes: 

![Configuração de material](media/material-setup.jpg)

> [!TIP]
É uma boa prática dar um nome adequado a seus materiais levando em conta seu uso e/ou tipo. Um material que deve ser usado em uma parte exclusiva pode ter um nome conforme essa parte, enquanto um material que pode ser usado em um número maior de áreas pode ter um nome conforme suas propriedades ou tipo.

Atribua suas texturas da seguinte maneira:

![Configuração de texturas](media/texture-setup.jpg)

Com seus materiais do PBR criados e configurados, vale a pena pensar em [instanciar objetos](../../how-tos/conversion/configure-model-conversion.md#instancing) em sua cena. Instanciar objetos semelhantes em sua cena, como porcas, parafusos, arruelas e cavilhas, essencialmente, todos os objetos que são iguais, pode reduzir significativamente o tamanho do arquivo. As instâncias de um objeto mestre podem ter a própria escala, rotação e transformações para que possam ser colocadas conforme necessário em sua cena. No Maya, o processo de instanciação é simples.

* No menu `Edit`, vá para `Duplicate Special` e abra o `Options`. 
* Na opção `Duplicate Special`, mude `Geometry Type` de `Copy` para `Instance`. 
* Clique em `Duplicate Special`

![Instanciação](media/instancing.jpg)

Esta ação criará uma instância do objeto que pode ser movida, girada ou dimensionada de maneira independente de seu pai e de outras instâncias desse pai. 
>No entanto, todas as alterações feitas a uma instância no modo de componente serão transmitidas para todas as instâncias do objeto, portanto, se você estiver trabalhando com componentes de objetos em instância (vértices, faces de polígono etc.), verifique se você deseja que todas as alterações feitas afetem todas essas instâncias.

Na cena de exemplo, cada objeto de caixa individual foi classificado como uma instância. Essa ação será relevante na exportação da cena para o formato FBX.

![Visão geral da cena](media/scene-overview.jpg)

>A melhor prática sobre a criação de instâncias em sua cena é criá-las conforme o uso, já que substituir "Cópias" por objetos de instância posteriormente é extremamente difícil. 

## <a name="fbx-export-process"></a>Processo de exportação do FBX

Agora podemos passar para a exportação do FBX de sua cena ou ativos de cena. Em termos gerais, faz sentido, ao exportar ativos, apenas selecionar exportar os objetos/ativos da sua cena que você deseja. Se você tem 100 objetos em uma cena, mas quiser usar apenas 30, não há motivo para exportação toda a cena. Então, a menos que você fique feliz em exportar toda a cena, faça sua seleção e vá para:

* `File` > `Export Selection` e, na caixa de diálogo de exportação, vá para a parte inferior e defina `Files of Type` como `FBX Export`. Essa janela vai expor as configurações de Exportação do FBX. As configurações primárias para a Exportação do FBX são realçadas em vermelho na imagem abaixo.

![Exportação do FBX](media/FBX-exporting.jpg)

Dependendo de seus requisitos (por exemplo, talvez você queira enviar um ativo para um cliente, mas não queira enviar um grande número de arquivos de textura com o ativo), você pode optar por inserir as texturas dentro do arquivo FBX exportado. Essa opção significa que você tem apenas um arquivo para empacotar, mas ela aumentará significativamente o tamanho desse ativo FBX. Você pode habilitar a opção para inserir texturas ativando a opção `Embed Media` conforme mostrado abaixo.

> [!TIP]
> Observe que o arquivo, neste caso, foi nomeado para refletir essa condição. Essa é uma boa prática que significa manter o controle de seus ativos. 

Depois de concluir a definição da configuração para exportação, clique no botão "Exportar Seleção" no canto inferior direito.

![Como inserir a mídia](media/embedding-media.jpg)

## <a name="conclusion"></a>Conclusão

Em geral, esse tipo de material parece mais realista, pois se baseia na física do mundo real da luz. Ele cria um efeito de imersão adicional de que a cena existe no mundo real.

## <a name="next-steps"></a>Próximas etapas

Agora você conhece a funcionalidade mais importante para configurar materiais com iluminação avançada para objetos em uma cena e exportá-los para o formato FBX que é compatível com o ARR. A próxima etapa seria converter o arquivo FBX e visualizá-lo no ARR.

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para renderização](../../quickstarts\convert-model.md)