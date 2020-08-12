---
title: Tamanhos de VM
description: Descreve os tamanhos distintos de VM que podem ser alocados
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: e8e439a055b71ed291573965c561ee31610e3ed4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121605"
---
# <a name="vm-sizes"></a>Tamanhos de VM

O serviço de renderização pode operar em dois tipos diferentes de computadores no Azure, chamado `Standard` e `Premium` .

## <a name="polygon-limits"></a>Limites de polígono

Há uma limitação rígida de **20 milhões polígonos** para o `Standard` tamanho da VM. Não há nenhuma limitação para o `Premium` tamanho.

Quando o renderizador em uma VM padrão atinge essa limitação, ele alterna o processamento para um plano de fundo de xadrez:

![Quadriculado](media/checkerboard.png)

## <a name="allocate-the-vm"></a>Alocar a VM

O tipo desejado de VM deve ser especificado no tempo de inicialização da sessão de renderização. Ele não pode ser alterado em uma sessão em execução. Os exemplos de código a seguir mostram o local onde o tamanho da VM deve ser especificado:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

Para os [scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md), o tamanho da VM deve ser especificado dentro do `arrconfig.json` arquivo:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Como o renderizador avalia o número de polígonos

O número de polígonos que são considerados para o teste de limitação são o número de polígonos que são realmente passados para o renderizador. Essa geometria normalmente é a soma de todos os modelos instanciados, mas também há exceções. A seguinte geometria **não está incluída**:
* Instâncias de modelo carregadas que estão completamente fora da exibição frustum.
* Modelos ou partes de modelo que são alternadas para invisível, usando o [componente de substituição de estado hierárquico](../overview/features/override-hierarchical-state.md).

Da mesma forma, é possível gravar um aplicativo que tem como alvo o `standard` tamanho que carrega vários modelos com uma contagem de polígonos perto do limite de cada modelo único. Quando o aplicativo mostra apenas um único modelo por vez, o xadrez não é disparado.

### <a name="how-to-determine-the-number-of-polygons"></a>Como determinar o número de polígonos

Há duas maneiras de determinar o número de polígonos de um modelo ou cena que contribuem para o limite de orçamento da `standard` VM de tamanho:
* No lado da conversão de modelo, recupere o [arquivo JSON de saída de conversão](../how-tos/conversion/get-information.md)e verifique a `numFaces` entrada na [seção *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Se seu aplicativo estiver lidando com conteúdo dinâmico, o número de polígonos renderizados poderá ser consultado dinamicamente durante o tempo de execução. Use uma [consulta de avaliação de desempenho](../overview/features/performance-queries.md#performance-assessment-queries) e verifique o `polygonsRendered` membro na `FrameStatistics` estrutura. O `polygonsRendered` campo será definido como `bad` quando o renderizador atingir a limitação do polígono. O plano de fundo quadriculado sempre fica com algum atraso para garantir que a ação do usuário possa ser executada após essa consulta assíncrona. A ação do usuário pode, por exemplo, ocultar ou excluir instâncias do modelo.

## <a name="pricing"></a>Preço

Para obter uma análise detalhada dos preços de cada tipo de VM, consulte a página de [preços de renderização remota](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Próximas etapas
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
* [Conversão de modelo](../how-tos/conversion/model-conversion.md)

