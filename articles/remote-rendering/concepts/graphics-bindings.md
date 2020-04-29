---
title: Associação de gráficos
description: Configuração de associações de gráficos e casos de uso
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681877"
---
# <a name="graphics-binding"></a>Associação de gráficos

Para poder usar a renderização remota do Azure em um aplicativo personalizado, ele precisa ser integrado ao pipeline de renderização do aplicativo. Essa integração é de responsabilidade da Associação de gráficos.

Uma vez configurado, a associação de gráficos fornece acesso a várias funções que afetam a imagem renderizada. Essas funções podem ser separadas em duas categorias: funções gerais que estão sempre disponíveis e funções específicas que são relevantes apenas para o selecionado `Microsoft.Azure.RemoteRendering.GraphicsApiType`.

## <a name="graphics-binding-in-unity"></a>Associação de gráficos no Unity

No Unity, toda a associação é manipulada pela `RemoteUnityClientInit` estrutura passada para `RemoteManagerUnity.InitializeManager`. Para definir o modo gráfico, o `GraphicsApiType` campo precisa ser definido como a associação escolhida. O campo será preenchido automaticamente dependendo se um XRDevice estiver presente. O comportamento pode ser substituído manualmente com os seguintes comportamentos:

* **HoloLens 2**: a associação gráfica do [Windows Mixed Reality](#windows-mixed-reality) é sempre usada.
* **Aplicativo de área de trabalho UWP simples**: a [simulação](#simulation) é sempre usada. Para usar esse modo, certifique-se de seguir as etapas em [tutorial: Configurando um projeto do Unity do zero](../tutorials/unity/project-setup.md).
* **Editor de Unity**: a [simulação](#simulation) é sempre usada, a menos que um headset WMR VR esteja conectado nesse caso, arr será desabilitado para permitir que o depure as partes relacionadas à não-arr do aplicativo. Consulte também [comunicação remota Holographic](../how-tos/unity/holographic-remoting.md).

A única outra parte relevante para o Unity é acessar a [Associação básica](#access), todas as outras seções abaixo podem ser ignoradas.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuração de associação de gráficos em aplicativos personalizados

Para selecionar uma associação de gráficos, execute as duas etapas a seguir: primeiro, a associação de gráficos precisa ser inicializada estaticamente quando o programa é inicializado:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

A chamada acima é necessária para inicializar a renderização remota do Azure nas APIs Holographic. Essa função deve ser chamada antes que qualquer API Holographic seja chamada e antes que qualquer outra APIs de renderização remota seja acessada. Da mesma forma, a função `RemoteManagerStatic.ShutdownRemoteRendering();` de desinicialização correspondente deve ser chamada depois que nenhuma API Holographic está sendo chamada mais.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Acessando a associação de gráficos

Depois que um cliente é configurado, a ligação básica de gráficos pode ser acessada com o `AzureSession.GraphicsBinding` getter. Por exemplo, as estatísticas do último quadro podem ser recuperadas da seguinte maneira:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>APIs gráficas

Atualmente, `WmrD3D11` há duas APIs de gráficos que podem ser selecionadas e `SimD3D11`. Um terceiro `Headless` existe, mas ainda não tem suporte no lado do cliente.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`é a associação padrão a ser executada no HoloLens 2. Ele criará a `GraphicsBindingWmrD3d11` associação. Nesse modo, a renderização remota do Azure se conecta diretamente às APIs do Holographic.

Para acessar as associações de gráficos derivadas, a base `GraphicsBinding` deve ser convertida.
Há duas coisas que precisam ser feitas para usar a associação WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informar a renderização remota do sistema de coordenadas usado

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Onde o acima `ptr` deve ser um ponteiro para um objeto `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` nativo que define o sistema de coordenadas de espaço mundial no qual as coordenadas na API são expressas em.

#### <a name="render-remote-image"></a>Renderizar imagem remota

No início de cada quadro, o quadro remoto precisa ser renderizado no buffer de fundo. Isso é feito chamando `BlitRemoteFrame`, que preencherá as informações de cor e de profundidade no destino de renderização atualmente associado. Portanto, é importante que isso seja feito após a associação do buffer de fundo como um destino de renderização.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulação

`GraphicsApiType.SimD3D11`é a associação de simulação e, se selecionada, `GraphicsBindingSimD3d11` ela cria a associação de gráficos. Essa interface é usada para simular a movimentação de cabeçalho, por exemplo, em um aplicativo de área de trabalho e renderiza uma imagem monoscopic.
A configuração é um pouco mais envolvida e funciona da seguinte maneira:

#### <a name="create-proxy-render-target"></a>Criar destino de renderização de proxy

O conteúdo local e remoto precisa ser renderizado para um destino de renderização de cor/profundidade fora da tela chamado ' proxy ' usando os dados `GraphicsBindingSimD3d11.Update` da câmera de proxy fornecidos pela função. O proxy deve corresponder à resolução do buffer de fundo. Quando uma sessão estiver pronta, `GraphicsBindingSimD3d11.InitSimulation` precisa ser chamada antes de se conectar a ela:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

A função init precisa ser fornecida com ponteiros para o dispositivo do D3D nativo, bem como para a textura de cores e profundidade do destino de renderização de proxy. `AzureSession.ConnectToRuntime` Depois de inicializado `DisconnectFromRuntime` , e pode ser chamado várias vezes, mas ao alternar para uma `GraphicsBindingSimD3d11.DeinitSimulation` sessão diferente, precisa ser chamado primeiro na sessão antiga `GraphicsBindingSimD3d11.InitSimulation` antes de poder ser chamado em outra sessão.

#### <a name="render-loop-update"></a>Atualizar loop de renderização

A atualização do loop de renderização consiste em várias etapas:

1. Cada quadro, antes que qualquer renderização ocorra, `GraphicsBindingSimD3d11.Update` é chamado com a transformação atual da câmera que é enviada para o servidor a ser renderizado. Ao mesmo tempo, a transformação de proxy retornada deve ser aplicada à câmera de proxy para ser renderizada no destino de renderização de proxy.
Se a atualização `SimulationUpdate.frameId` de proxy retornada for nula, ainda não haverá dados remotos. Nesse caso, em vez de renderizar para o destino de renderização de proxy, qualquer conteúdo local deve ser processado para o buffer de fundo diretamente usando os dados atuais da câmera e as duas próximas etapas são ignoradas.
1. O aplicativo agora deve associar o destino de renderização de proxy `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`e chamar. Isso preencherá as informações de cor e profundidade remotas para o destino de renderização de proxy. Qualquer conteúdo local agora pode ser renderizado no proxy usando a transformação de câmera de proxy.
1. Em seguida, o buffer de fundo precisa ser associado como um destino de `GraphicsBindingSimD3d11.ReprojectProxy` renderização e chamado no ponto em que o buffer de fundo pode ser apresentado.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Configuração de um projeto do Unity do zero](../tutorials/unity/project-setup.md)
