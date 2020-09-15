---
title: Câmera
description: Descreve as configurações de câmera e os casos de uso
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564779"
---
# <a name="camera"></a>Câmera

Para garantir que o conteúdo renderizado local e remotamente possa ser composto de forma direta, várias propriedades de câmera precisam permanecer sincronizadas entre o servidor e o cliente. Mais notavelmente, a transformação e a projeção da câmera. Por exemplo, o conteúdo processado localmente deve usar a mesma transformação de câmera e projeção com a qual o quadro remoto mais recente foi renderizado.

![Câmera local e remota](./media/camera.png)

Na imagem acima, a câmera local foi movida em comparação com a estrutura remota enviada pelo servidor. Como resultado, o conteúdo local precisa ser processado da mesma perspectiva que o quadro remoto e, por fim, a imagem resultante é reprojetada no espaço da câmera local, o que é explicado em detalhes na [Reprojeção de estágio atrasado](late-stage-reprojection.md).

O atraso entre a renderização remota e local significa que qualquer alteração dessas configurações é aplicada com um atraso. Portanto, qualquer valor novo não pode ser usado imediatamente no mesmo quadro.

As distâncias do plano próximo e longe podem ser definidas nas configurações da câmera. No HoloLens 2, os dados de transformação e de projeção restantes são definidos pelo hardware. Ao usar a [simulação de área de trabalho](../../concepts/graphics-bindings.md), elas são definidas por meio da entrada de sua `Update` função.

Os valores alterados podem ser usados localmente assim que o primeiro quadro remoto chegar que foi renderizado com eles. No HoloLens 2, os dados a serem usados para renderização são fornecidos pelo *HolographicFrame* , assim como em qualquer outro aplicativo Holographic. Na [simulação de área de trabalho](../../concepts/graphics-bindings.md), eles são recuperados por meio da saída da `Update` função.

## <a name="camera-settings"></a>Configurações da câmera

As propriedades a seguir podem ser alteradas nas configurações da câmera:

**Plano próximo e longe:**

Para certificar-se de que nenhum intervalo inválido possa ser definido, as propriedades **NearPlane** e **FarPlane** são somente leitura e uma função separada **SetNearAndFarPlane** existe para alterar o intervalo. Esses dados serão enviados para o servidor no final do quadro.

> [!IMPORTANT]
> No Unity, isso é manipulado automaticamente ao alterar os planos próximos e distantes da câmera principal.

**EnableDepth**:

Às vezes, é útil desabilitar a gravação do buffer de profundidade da imagem remota para fins de depuração. A desabilitação da profundidade também impedirá que o servidor envie dados de profundidade, reduzindo assim a largura de banda.

> [!TIP]
> No Unity, é fornecido um componente de depuração chamado **EnableDepthComponent** que pode ser usado para alternar esse recurso na interface do usuário do editor.

A alteração das configurações da câmera pode ser feita da seguinte maneira:

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>Documentação da API

* [CameraSettings C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [Função C# GraphicsBindingSimD3d11. Update](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [Função C++ GraphicsBindingSimD3d11:: Update](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Próximas etapas

* [Associações de gráficos](../../concepts/graphics-bindings.md)
* [Reprojeção de fase tardia](late-stage-reprojection.md)
