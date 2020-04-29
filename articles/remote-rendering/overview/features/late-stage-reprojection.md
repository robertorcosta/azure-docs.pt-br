---
title: Reprojeção de fase tardia
description: Informações sobre a Reprojeção de estágio atrasado e como usá-la.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680980"
---
# <a name="late-stage-reprojection"></a>Reprojeção de fase tardia

A *Reprojeção de estágio tardia* (LSR) é um recurso de hardware que ajuda a estabilizar os hologramas quando o usuário se move.

Os modelos estáticos são esperados para manter suas posições visualmente quando você se movimenta ao seu respeito. Se parecerem instáveis, esse comportamento pode ser indício de problemas de LSR. Lembre-se de que as transformações dinâmicas adicionais, como animações ou exibições de explosão, podem mascarar esse comportamento.

Você pode escolher entre dois modos de LSR diferentes, ou seja, **planar LSR** ou **Depth LSR**. Qual deles está ativo é determinado pelo fato de o aplicativo cliente enviar um buffer de profundidade.

Ambos os modos LSR melhoram a estabilidade do holograma, embora tenham suas limitações distintas. Comece experimentando LSR de profundidade, pois isso é, sem dúvida, dando melhores resultados na maioria dos casos.

## <a name="choose-lsr-mode-in-unity"></a>Escolha o modo LSR no Unity

No editor do Unity, vá para *arquivo > configurações de Build*. Selecione *configurações do Player* no canto inferior esquerdo e, em seguida, verifique *as configurações do player > XR > SDKs da realidade virtual > realidade do Windows Mixed* se **habilitar o compartilhamento de buffer de profundidade** está marcado:

![Sinalizador de compartilhamento de buffer de profundidade habilitado](./media/unity-depth-buffer-sharing-enabled.png)

Se for, seu aplicativo usará LSR de profundidade, caso contrário, ele usará o planar LSR.

## <a name="depth-lsr"></a>LSR de profundidade

Para que a LSR de profundidade funcione, o aplicativo cliente deve fornecer um buffer de profundidade válido que contenha toda a geometria relevante a ser considerada durante o LSR.

Profundidade LSR tenta estabilizar o quadro de vídeo com base no conteúdo do buffer de profundidade fornecido. Como consequência, o conteúdo que não foi renderizado para ele, como objetos transparentes, não pode ser ajustado por LSR e pode mostrar artefatos de instabilidade e de Reprojeção.

## <a name="planar-lsr"></a>LSR planar

O planar LSR não tem informações de profundidade por pixel, como a profundidade LSR. Em vez disso, ele reprojeta todo o conteúdo com base em um plano que você deve fornecer a cada quadro.

O planar LSR reprojeta esses objetos melhor que se aproximam do plano fornecido. Quanto mais distante um objeto for, mais instável será a aparência. Embora a profundidade LSR seja melhor na Reprojeção de objetos em diferentes profundidades, o planar LSR pode funcionar melhor para conteúdo com alinhamento bem com um plano.

### <a name="configure-planar-lsr-in-unity"></a>Configurar o planar LSR no Unity

Os parâmetros de plano são derivados de um, portanto, chamado de *ponto de foco*, que você precisa `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`fornecer a cada quadro. Consulte a [API do ponto de foco do Unity](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) para obter detalhes. Se você não definir um ponto de foco, um fallback será escolhido para você. No entanto, o fallback automático geralmente leva a resultados de qualidade inferior.

Você pode calcular o ponto de foco por conta própria, embora possa fazer sentido baseá-lo em um calculado pelo host de renderização remoto. Chame `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` para obtê-lo. Você será solicitado a fornecer um quadro de coordenadas no qual expressar o ponto de foco. Na maioria dos casos, você só desejará fornecer o resultado `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` aqui.

Geralmente, o cliente e o host renderizam conteúdo que o outro lado não reconhece, como elementos de interface do usuário no cliente. Portanto, pode fazer sentido combinar o ponto de foco remoto com um calculado localmente.

Os pontos de foco calculados em dois quadros sucessivos podem ser bem diferentes. Simplesmente usá-los como estão pode fazer com que os hologramas pareçam estar indo em frente. Para evitar esse comportamento, a interpolação entre os pontos de foco anteriores e atuais é aconselhável.

## <a name="next-steps"></a>Próximas etapas

* [Consultas de desempenho do lado do servidor](performance-queries.md)
