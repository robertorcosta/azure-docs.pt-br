---
title: Mitigação de Z-fighting
description: Descreve as técnicas para mitigar os artefatos de combate ao z
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680447"
---
# <a name="z-fighting-mitigation"></a>Mitigação de Z-fighting

Quando duas superfícies se sobrepõem, não fica claro qual delas deve ser renderizada sobre a outra. O resultado até varia por pixel, resultando em artefatos dependentes da exibição. Consequentemente, quando a câmera ou a malha se move, esses padrões se deslocam visivelmente. Esse artefato é chamado *de combate ao z*. Para os aplicativos de AR e VR, o problema é intensificado porque os dispositivos com montagem de cabeçalho naturalmente sempre se movem. Para evitar o visualizador discomfort, a funcionalidade de mitigação do combate ao z está disponível na renderização remota do Azure.

## <a name="z-fighting-mitigation-modes"></a>Modos de mitigação de combate Z

|Ocorrer                        | Result                               |
|---------------------------------|:-------------------------------------|
|Combate z regular               |![Combate ao Z](./media/zfighting-0.png)|
|Mitigação de combate a Z habilitada    |![Combate ao Z](./media/zfighting-1.png)|
|Realce de quadriculado habilitado|![Combate ao Z](./media/zfighting-2.png)|

O código a seguir habilita a mitigação do combate ao z:

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
> A mitigação do combate ao Z é uma configuração global que afeta todas as malhas renderizadas.

## <a name="reasons-for-z-fighting"></a>Motivos para combater o z

O combate ao Z ocorre principalmente por dois motivos:

1. Quando as superfícies estão longe da câmera, a precisão dos valores de profundidade diminui e os valores se tornam indistinguíveis
1. Quando as superfícies em uma malha se sobrepõem fisicamente

O primeiro problema sempre pode acontecer e é difícil de eliminar. Se isso acontecer em seu aplicativo, certifique-se de que a proporção da distância do *plano próximo* com a distância do *plano distante* é tão baixa quanto prática. Por exemplo, um plano próximo à distância 0, 1 e o plano distante na distância 1000 criará esse problema muito mais cedo do que ter o plano próximo às 0,1 e o plano distante na distância 20.

O segundo problema é um indicador de conteúdo mal criado. No mundo real, dois objetos não podem estar no mesmo local ao mesmo tempo. Dependendo do aplicativo, os usuários talvez queiram saber se existem superfícies sobrepostas e onde elas estão. Por exemplo, uma cena do CAD de um edifício que é a base para uma construção do mundo real não deve conter interseções de superfície fisicamente impossíveis. Para permitir a inspeção visual, o modo de realce está disponível, que exibe o combate a z potencial como um padrão quadriculado animado.

## <a name="limitations"></a>Limitações

A mitigação de combate ao z fornecida é um melhor esforço. Não há nenhuma garantia de que remova todo o combate ao z. Além disso, ele preferirá automaticamente uma superfície sobre a outra. Assim, quando você tem superfícies muito próximas entre si, pode acontecer que a superfície "errada" termine na parte superior. Um caso de problema comum é quando o texto e outros decals são aplicados a uma superfície. Com a mitigação de combate a z habilitada, esses detalhes poderiam simplesmente desaparecer.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

* Habilitar a mitigação de combate a z incorre em pouca ou nenhuma sobrecarga de desempenho.
* Além disso, habilitar a sobreposição de combate ao z incorre em uma sobrecarga de desempenho não trivial, embora possa variar dependendo da cena.

## <a name="next-steps"></a>Próximas etapas

* [Renderização de modos](../../concepts/rendering-modes.md)
* [Reprojeção de fase tardia](late-stage-reprojection.md)
