---
title: Renderização de depuração
description: Visão geral dos efeitos de renderização de depuração do lado do servidor
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394287"
---
# <a name="debug-rendering"></a>Renderização de depuração

A API de renderização de depuração fornece uma gama de opções globais para alterar a renderização do lado do servidor com diferentes efeitos de depuração.

## <a name="available-debug-rendering-effects"></a>Efeitos de renderização de depuração disponíveis

|Configuração                          | Efeito                               |
|---------------------------------|:-------------------------------------|
|Contador de quadros                    | Torna uma sobreposição de texto no canto superior esquerdo do quadro. O texto mostra o ID do quadro do lado do servidor atual, que é continuamente incrementado à medida que a renderização prossegue. |
|Contagem de polígonos                    | Torna uma sobreposição de texto no canto superior esquerdo do quadro. O texto mostra a quantidade de polígonos atualmente renderizados, o mesmo valor consultado pelas [consultas de desempenho do lado](performance-queries.md) do servidor| 
|Wireframe                        | Se ativado, toda a geometria do objeto carregada no servidor será renderizada no modo wireframe. Apenas as bordas dos polígonos serão rasterizadas neste modo. |

O código a seguir permite esses efeitos de depuração:

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![Renderização de depuração](./media/debug-rendering.png)

> [!NOTE]
> Todos os efeitos de renderização de depuração são configurações globais que afetam todo o quadro.

## <a name="use-cases"></a>Casos de uso

A API de renderização de depuração destina-se a tarefas simples de depuração, como verificar se a conexão de serviço está realmente funcionando corretamente. As opções de renderização de texto afetam diretamente os quadros de vídeo transmitidos para baixo. A habilitá-los verifica se novos quadros são recebidos e decodificados por vídeo corretamente.

No entanto, os efeitos fornecidos não dão qualquer introspecção detalhada na saúde dos serviços. As consultas de desempenho do lado do servidor são [recomendadas](performance-queries.md) para este caso de uso.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

* Habilitar as sobreposições de texto incorre pouco ou nenhuma sobrecarga de desempenho.
* Além disso, habilitar a sobreposição incorre em uma sobrecarga de desempenho não trivial, embora possa variar dependendo da cena. Para cenas complexas, este modo pode fazer com que o framerate caia abaixo da meta de 60 Hz.

## <a name="next-steps"></a>Próximas etapas

* [Renderização de modos](../../concepts/rendering-modes.md)
* [Consultas de desempenho do lado do servidor](performance-queries.md)
