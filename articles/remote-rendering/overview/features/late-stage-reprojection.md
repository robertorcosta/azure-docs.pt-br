---
title: Reprojeção tardia
description: Informações sobre Reprojeção do Estágio Final e como usá-lo.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680980"
---
# <a name="late-stage-reprojection"></a>Reprojeção tardia

*O Late Stage Reprojection* (LSR) é um recurso de hardware que ajuda a estabilizar hologramas quando o usuário se move.

Espera-se que os modelos estáticos mantenham visualmente sua posição quando você se move em torno deles. Se eles parecem instáveis, esse comportamento pode sugerir problemas de LSR. Lembre-se de que transformações dinâmicas adicionais, como animações ou visualizações de explosão, podem mascarar esse comportamento.

Você pode escolher entre dois modos diferentes de LSR, ou seja, **Planar LSR** ou **Depth LSR**. Qual deles está ativo é determinado se o aplicativo cliente envia um buffer de profundidade.

Ambos os modos De RL melhoram a estabilidade do holograma, embora tenham suas limitações distintas. Comece por tentar o Depth LSR, pois é indiscutivelmente dando melhores resultados na maioria dos casos.

## <a name="choose-lsr-mode-in-unity"></a>Escolha o modo LSR em Unity

No editor Unity, vá para *Configurações de > de arquivamento de arquivos*. Selecione *as configurações* do jogador no esquerdo inferior e verifique em *> XR configurações do jogador > SDKs de realidade virtual > realidade mista* do Windows se o Compartilhamento de Buffer de **Profundidade** é verificado:

![Sinalizador ativado para compartilhamento de buffer de profundidade](./media/unity-depth-buffer-sharing-enabled.png)

Se for, seu aplicativo usará Depth LSR, caso contrário ele usará Planar LSR.

## <a name="depth-lsr"></a>LSR de profundidade

Para que o LSR de profundidade funcione, o aplicativo cliente deve fornecer um buffer de profundidade válido que contenha toda a geometria relevante a considerar durante o LSR.

A profundidade LSR tenta estabilizar o quadro de vídeo com base no conteúdo do buffer de profundidade fornecido. Como conseqüência, o conteúdo que não foi entregue a ele, como objetos transparentes, não pode ser ajustado pelo LSR e pode mostrar inestabilidade e artefatos de reprojeção.

## <a name="planar-lsr"></a>Planar LSR

O Planar LSR não possui informações de profundidade por pixel, como o Depth LSR faz. Em vez disso, ele reprojeta todo o conteúdo com base em um plano que você deve fornecer a cada quadro.

Planar LSR reprojeta os objetos que estão melhor perto do avião fornecido. Quanto mais longe um objeto estiver, mais instável ele vai parecer. Embora o Depth LSR seja melhor em reprojetar objetos em diferentes profundidades, o Planar LSR pode funcionar melhor para conteúdo alinhado bem com um plano.

### <a name="configure-planar-lsr-in-unity"></a>Configure Planar LSR em Unidade

Os parâmetros do plano são derivados de um chamado ponto `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`de *foco,* que você tem que fornecer cada quadro através . Consulte a [API do Unity Focus Point](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) para obter detalhes. Se você não definir um ponto de foco, um recuo será escolhido para você. No entanto, esse recuo automático muitas vezes leva a resultados abaixo do ideal.

Você pode calcular o ponto de foco você mesmo, embora possa fazer sentido baseá-lo no calculado pelo host de renderização remota. Ligue `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` para obter isso. Você é solicitado a fornecer um quadro de coordenadas para expressar o ponto de foco. Na maioria dos casos, você só vai `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` querer fornecer o resultado daqui.

Normalmente, tanto o cliente quanto o host renderizam conteúdo que o outro lado não está ciente, como elementos de ui no cliente. Portanto, pode fazer sentido combinar o ponto de foco remoto com um calculado localmente.

Os pontos de foco calculados em dois quadros sucessivos podem ser bem diferentes. Simplesmente usá-los como-é pode levar a hologramas que parecem estar pulando ao redor. Para evitar esse comportamento, é aconselhável interpolar entre os pontos de foco anteriores e atuais.

## <a name="next-steps"></a>Próximas etapas

* [Consultas de desempenho em parte do servidor](performance-queries.md)
