---
title: Modos de renderização
description: Descreve os diferentes modos de renderização do lado do servidor
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681695"
---
# <a name="rendering-modes"></a>Modos de renderização

A renderização remota oferece dois modos principais de operação, o modo **TileBasedComposition** e o modo **DepthBasedComposition.** Esses modos determinam como a carga de trabalho é distribuída em várias GPUs no servidor. O modo deve ser especificado no momento da conexão e não pode ser alterado durante o tempo de execução.

Ambos os modos vêm com vantagens, mas também com limitações inerentes aos recursos, então escolher o modo mais adequado é usar caso específico.

## <a name="modes"></a>Modos

Os dois modos são agora discutidos com mais detalhes.

### <a name="tilebasedcomposition-mode"></a>Modo 'TileBasedComposition'

No modo **TileBasedComposition,** cada GPU envolvida renderiza subretângulos (ladrilhos) específicos na tela. A GPU principal compõe a imagem final das telhas antes de ser enviada como um quadro de vídeo para o cliente. Assim, todas as GPUs precisam ter o mesmo conjunto de recursos para renderização, de modo que os ativos carregados precisam se encaixar na memória de uma única GPU.

A qualidade de renderização neste modo é ligeiramente melhor do que no modo **DepthBasedComposition,** já que o MSAA pode trabalhar no conjunto completo de geometria para cada GPU. A captura de tela a seguir mostra que a antialiasing funciona corretamente para ambas as bordas da mesma forma:

![MSAA em Composição baseada em ladrilhos](./media/service-render-mode-quality.png)

Além disso, neste modo cada peça pode ser comutada para um material transparente ou comutada para o modo **transparente** através do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modo 'ProfundidadeBaseadaComposição'

No modo **DepthBasedComposition,** cada GPU envolvida renderiza em resolução de tela cheia, mas apenas um subconjunto de linhas. A composição final da imagem na GPU principal toma cuidado para que as peças sejam adequadamente mescladas de acordo com suas informações de profundidade. Naturalmente, a carga de memória é distribuída através das GPUs, permitindo assim a renderização de modelos que não caberiam na memória de uma única GPU.

Cada GPU usa o MSAA para antialias conteúdo local. No entanto, pode haver aliasing inerente entre bordas de GPUs distintas. Esse efeito é mitigado pós-processamento da imagem final, mas a qualidade do MSAA ainda é pior do que no modo **TileBasedComposition.**

Os artefatos MSAA são ilustrados ![na seguinte imagem: MSAA em DepthBasedComposition](./media/service-render-mode-balanced.png)

O antialiasing funciona corretamente entre a escultura e a cortina, porque ambas as partes são renderizadas na mesma GPU. Por outro lado, a borda entre cortina e parede mostra alguns aliasing porque essas duas partes são compostas de GPUs distintas.

A maior limitação deste modo é que as peças de geometria não podem ser comutadas para materiais transparentes dinamicamente nem o modo **transparente** funciona para o [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Outras características de substituição de estado (contorno, color tint, ...) funcionam, no entanto. Também materiais que foram marcados como transparentes no tempo de conversão funcionam corretamente neste modo.

### <a name="performance"></a>Desempenho

As características de desempenho para ambos os modos variam de acordo com o caso de uso, e é difícil raciocinar ou fornecer recomendações gerais. Se você não estiver constrangido pelas limitações mencionadas acima (memória ou transparência/see-through), recomenda-se experimentar ambos os modos e monitorar o desempenho usando várias posições da câmera.

## <a name="setting-the-render-mode"></a>Configuração do modo render

O modo de renderização usado em uma `AzureSession.ConnectToRuntime` VM de renderização remota é especificado durante o `ConnectToRuntimeParams`.

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Próximas etapas

* [Sessões](../concepts/sessions.md)
* [Componente de substituição de estado hierárquico](../overview/features/override-hierarchical-state.md)
