---
title: Depurar renderização
description: Visão geral dos efeitos de renderização de depuração no lado do servidor
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868154"
---
# <a name="debug-rendering"></a>Depurar renderização

A API de renderização de depuração fornece uma variedade de opções globais para alterar a renderização do lado do servidor com diferentes efeitos de depuração.

## <a name="available-debug-rendering-effects"></a>Efeitos de renderização de depuração disponíveis

|Setting                          | Efeito                               |
|---------------------------------|:-------------------------------------|
|Contador de quadros                    | Renderiza uma sobreposição de texto no canto superior esquerdo do quadro. O texto mostra a ID do quadro do servidor atual, que é incrementada continuamente conforme o processamento prossegue. |
|Contagem de polígonos                    | Renderiza uma sobreposição de texto no canto superior esquerdo do quadro. O texto mostra a quantidade de polígonos processados no momento, o mesmo valor que consultado por [consultas de desempenho no lado do servidor](performance-queries.md)| 
|Wireframe                        | Se habilitada, todas as geometrias de objeto carregadas no servidor serão renderizadas no modo wireframe. Somente as bordas de polígonos serão rasterizadas nesse modo. |

O código a seguir habilita esses efeitos de depuração:

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
> Todos os efeitos de renderização de depuração são configurações globais que afetam o quadro inteiro.

## <a name="use-cases"></a>Casos de uso

A API de renderização de depuração destina-se a tarefas de depuração simples, como verificar se a conexão de serviço está na verdade funcionando corretamente. As opções de renderização de texto afetam diretamente os quadros de vídeo em fluxo baixo. Habilitá-los verifica se novos quadros são recebidos e o vídeo foi decodificado corretamente.

No entanto, os efeitos fornecidos não fornecem nenhuma introspecção detalhada na integridade do serviço. As [consultas de desempenho do lado do servidor](performance-queries.md) são recomendadas para esse caso de uso.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

* Habilitar as sobreposições de texto incorre em pouca ou nenhuma sobrecarga de desempenho.
* A habilitação do modo wireframe provoca uma sobrecarga de desempenho não trivial, embora possa variar dependendo da cena. Para cenas complexas, esse modo pode fazer com que a taxa de quadros seja descartada abaixo do destino de 60 Hz.

## <a name="next-steps"></a>Próximas etapas

* [Renderização de modos](../../concepts/rendering-modes.md)
* [Consultas de desempenho do lado do servidor](performance-queries.md)
