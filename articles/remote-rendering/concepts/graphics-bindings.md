---
title: Vinculação gráfica
description: Configuração de vinculações gráficas e casos de uso
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681877"
---
# <a name="graphics-binding"></a>Vinculação gráfica

Para poder usar a renderização remota do Azure em um aplicativo personalizado, ele precisa ser integrado ao pipeline de renderização do aplicativo. Essa integração é de responsabilidade da vinculação gráfica.

Uma vez configurada, a vinculação gráfica dá acesso a várias funções que afetam a imagem renderizada. Essas funções podem ser separadas em duas categorias: funções gerais sempre disponíveis e `Microsoft.Azure.RemoteRendering.GraphicsApiType`funções específicas que só são relevantes para os selecionados .

## <a name="graphics-binding-in-unity"></a>Vinculação gráfica em Unidade

Em Unity, toda a ligação `RemoteUnityClientInit` é tratada `RemoteManagerUnity.InitializeManager`pela estrutura passada para . Para definir o modo `GraphicsApiType` gráfico, o campo deve ser definido como a vinculação escolhida. O campo será preenchido automaticamente dependendo se um XRDevice estiver presente. O comportamento pode ser substituído manualmente com os seguintes comportamentos:

* **HoloLens 2**: a vinculação gráfica [windows mixed reality](#windows-mixed-reality) é sempre usada.
* **Aplicativo de desktop Flat UWP**: [A simulação](#simulation) é sempre usada. Para usar este modo, certifique-se de seguir as etapas do [Tutorial: Configuração de um projeto Unity do zero](../tutorials/unity/project-setup.md).
* **Editor de unidade**: [A simulação](#simulation) é sempre usada a menos que um fone de ouvido WMR VR esteja conectado, caso em que o ARR será desativado para permitir depurar as partes não relacionadas ao ARR do aplicativo. Veja também [remoting holográfico](../how-tos/unity/holographic-remoting.md).

A única outra parte relevante para a Unidade é acessar a [vinculação básica,](#access)todas as outras seções abaixo podem ser ignoradas.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuração de vinculação gráfica em aplicativos personalizados

Para selecionar uma vinculação gráfica, dê as duas etapas seguintes: Primeiro, a vinculação gráfica deve ser inicializada esticamente quando o programa é inicializado:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

A chamada acima é necessária para inicializar a renderização remota do Azure nas APIs holográficas. Esta função deve ser chamada antes que qualquer API holográfica seja chamada e antes que quaisquer outras APIs de renderização remota sejam acessadas. Da mesma forma, a função `RemoteManagerStatic.ShutdownRemoteRendering();` dede-init correspondente deve ser chamada depois que nenhuma APIs holográfica está sendo chamada mais.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Acessando a vinculação gráfica

Uma vez configurado um cliente, a vinculação `AzureSession.GraphicsBinding` gráfica básica pode ser acessada com o getter. Como exemplo, as estatísticas do último quadro podem ser recuperadas assim:

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

Existem atualmente duas APIs gráficas `WmrD3D11` `SimD3D11`que podem ser selecionadas e . Um terceiro `Headless` existe, mas ainda não é suportado do lado do cliente.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`é a vinculação padrão para ser executada no HoloLens 2. Ele vai `GraphicsBindingWmrD3d11` criar a ligação. Neste modo, a renderização remota do Azure se prende diretamente às APIs holográficas.

Para acessar as amarras gráficas `GraphicsBinding` derivadas, a base deve ser lançada.
Há duas coisas que precisam ser feitas para usar a vinculação da RMM:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informar renderização remota do sistema de coordenadas usado

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Onde o `ptr` acima deve ser `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` um ponteiro para um objeto nativo que define o sistema de coordenadas espaciais do mundo no qual as coordenadas na API são expressas.

#### <a name="render-remote-image"></a>Renderizar imagem remota

No início de cada quadro, o quadro remoto precisa ser renderizado no buffer traseiro. Isso é feito `BlitRemoteFrame`por chamada , que preencherá informações de cor e profundidade no alvo de renderização atualmente vinculado. Assim, é importante que isso seja feito após a vinculação do buffer traseiro como um alvo de renderização.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulação

`GraphicsApiType.SimD3D11`é a vinculação da simulação e, se selecionada, cria a `GraphicsBindingSimD3d11` vinculação gráfica. Esta interface é usada para simular o movimento da cabeça, por exemplo, em um aplicativo de desktop e renderiza uma imagem monoscópica.
A configuração é um pouco mais envolvida e funciona da seguinte forma:

#### <a name="create-proxy-render-target"></a>Criar destino de renderização proxy

O conteúdo remoto e local precisa ser renderizado para um destino de renderização de cores/profundidade fora da tela chamado 'proxy' usando os dados da câmera proxy fornecidos pela `GraphicsBindingSimD3d11.Update` função. O proxy deve corresponder à resolução do buffer traseiro. Uma vez que `GraphicsBindingSimD3d11.InitSimulation` uma sessão esteja pronta, precisa ser chamada antes de se conectar a ela:

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

A função init precisa ser fornecida com ponteiros para o dispositivo d3d nativo, bem como para a textura de cor e profundidade do alvo de renderização proxy. Uma vez `AzureSession.ConnectToRuntime` inicializado, `DisconnectFromRuntime` e pode ser chamado várias `GraphicsBindingSimD3d11.DeinitSimulation` vezes, mas ao mudar para `GraphicsBindingSimD3d11.InitSimulation` uma sessão diferente, precisa ser chamado primeiro na sessão antiga antes pode ser chamado em outra sessão.

#### <a name="render-loop-update"></a>Atualização do loop render

A atualização do loop de renderização consiste em várias etapas:

1. Cada quadro, antes de `GraphicsBindingSimD3d11.Update` qualquer renderização ocorrer, é chamado com a transformação atual da câmera que é enviada para o servidor a ser renderizada. Ao mesmo tempo, a transformação de proxy retornada deve ser aplicada à câmera proxy para renderizar no destino de renderização proxy.
Se a atualização `SimulationUpdate.frameId` de proxy retornada for nula, ainda não há dados remotos. Neste caso, em vez de renderizar no destino de renderização proxy, qualquer conteúdo local deve ser renderizado diretamente no buffer traseiro usando os dados atuais da câmera e os dois próximos passos são ignorados.
1. O aplicativo deve agora vincular o `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`destino de renderização proxy e a chamada . Isso preencherá as informações remotas de cor e profundidade no destino de renderização proxy. Qualquer conteúdo local agora pode ser renderizado no proxy usando a transformação da câmera proxy.
1. Em seguida, o buffer traseiro precisa ser `GraphicsBindingSimD3d11.ReprojectProxy` vinculado como um alvo de renderização e chamado em que ponto o buffer traseiro pode ser apresentado.

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

* [Tutorial: Configurando um projeto unity do zero](../tutorials/unity/project-setup.md)
