---
title: Mitigação de luta z
description: Descreve técnicas para mitigar artefatos de combate z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680447"
---
# <a name="z-fighting-mitigation"></a>Mitigação de luta z

Quando duas superfícies se sobrepõem, não está claro qual deve ser renderizada em cima da outra. O resultado varia até por pixel, resultando em artefatos dependentes da visão. Consequentemente, quando a câmera ou a malha se movem, esses padrões piscam visivelmente. Este artefato é chamado *de luta z.* Para aplicações AR e VR, o problema é intensificado porque os dispositivos montados na cabeça se movem naturalmente. Para evitar o desconforto do espectador, a funcionalidade de mitigação de combate z está disponível na renderização remota do Azure.

## <a name="z-fighting-mitigation-modes"></a>Modos de mitigação de luta Z

|Situação                        | Result                               |
|---------------------------------|:-------------------------------------|
|Luta de z regular               |![Luta z](./media/zfighting-0.png)|
|Mitigação de luta z habilitada    |![Luta z](./media/zfighting-1.png)|
|Destaque do checkerboard ativado|![Luta z](./media/zfighting-2.png)|

O código a seguir permite a mitigação do combate z:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> A mitigação de combate z é um cenário global que afeta todas as meshes renderizadas.

## <a name="reasons-for-z-fighting"></a>Razões para a luta z

A luta Z acontece principalmente por duas razões:

1. quando as superfícies estão muito longe da câmera, a precisão de seus valores de profundidade se degrada e os valores se tornam indistinguíveis
1. quando superfícies em uma malha fisicamente sobreposição

O primeiro problema sempre pode acontecer e é difícil de eliminar. Se isso acontecer na sua aplicação, certifique-se de que a proporção da distância *próxima* do avião para a *distância do plano distante* é tão baixa quanto prática. Por exemplo, um plano próximo à distância 0,01 e um plano distante à distância 1000 criará esse problema muito mais cedo, do que ter o avião próximo a 0,1 e o plano distante à distância 20.

O segundo problema é um indicador para conteúdo mal autoral. No mundo real, dois objetos não podem estar no mesmo lugar ao mesmo tempo. Dependendo do aplicativo, os usuários podem querer saber se existem superfícies sobrepostas e onde elas estão. Por exemplo, uma cena CAD de um edifício que é a base para uma construção do mundo real, não deve conter intersecções de superfície fisicamente impossíveis. Para permitir a inspeção visual, o modo de destaque está disponível, que exibe o potencial z-fighting como um padrão de tabuleiro de xadrez animado.

## <a name="limitations"></a>Limitações

A mitigação de luta z fornecida é o melhor esforço. Não há garantia de que ele remove todas as lutas z. Além disso, ele vai automaticamente preferir uma superfície em vez de outra. Assim, quando você tem superfícies muito próximas umas das outras, pode acontecer que a superfície "errada" acabe por cima. Um caso de problema comum é quando o texto e outros decalques são aplicados a uma superfície. Com a mitigação de combate z habilitada esses detalhes poderiam facilmente simplesmente desaparecer.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

* Permitir a mitigação de luta z incorre pouco ou nenhum desempenho em cima.
* Além disso, habilitar a sobreposição de luta z incorre em uma sobrecarga de desempenho não trivial, embora possa variar dependendo da cena.

## <a name="next-steps"></a>Próximas etapas

* [Modos de renderização](../../concepts/rendering-modes.md)
* [Reprojeção tardia](late-stage-reprojection.md)
