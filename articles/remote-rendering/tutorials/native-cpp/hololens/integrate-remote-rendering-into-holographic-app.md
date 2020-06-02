---
title: Integrar o Remote Rendering a um aplicativo holográfico do C++/DirectX11
description: Explica como integrar o Remote Rendering em um aplicativo holográfico simples do C++/DirectX11 conforme criado com o assistente de projeto do Visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9db32912e86079875ad382fb23e521c720fc7fbd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775613"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Tutorial: integrar o Remote Rendering a um aplicativo holográfico do HoloLens

Neste tutorial, você irá aprender:

> [!div class="checklist"]
>
> * Usar o Visual Studio para criar um aplicativo holográfico que possa ser implantado no HoloLens
> * Adicionar os snippets de código e as configurações de projeto necessários para combinar a renderização local com conteúdo renderizado remotamente

Este tutorial se concentra na adição dos bits necessários a um exemplo de `Holographic App` nativo para combinar a renderização local com o Azure Remote Rendering. O único tipo de feedback de status nesse aplicativo é por meio do painel de saída da depuração dentro do Visual Studio, portanto, é recomendável iniciar o exemplo dentro do Visual Studio. A adição de feedback adequado no aplicativo está além do escopo deste exemplo, porque a criação de um painel de texto dinâmico do zero envolve muito código. Um bom ponto de partida é a classe `StatusDisplay`, que faz parte do [projeto de exemplo do Player de Comunicação Remota no GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). Na verdade, a versão pré-configurada deste tutorial usa uma cópia local dessa classe.

> [!TIP]
> O [repositório de exemplos do ARR](https://github.com/Azure/azure-remote-rendering) contém o resultado deste tutorial como um projeto do Visual Studio que está pronto para uso. Ele também é aprimorado com um relatório de status e erro adequado por meio da classe de interface do usuário `StatusDisplay`. Dentro do tutorial, todas as adições específicas do ARR têm o escopo de `#ifdef USE_REMOTE_RENDERING` / `#endif`, portanto, é fácil identificar as adições do Remote Rendering.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é necessário:

* As informações da conta (ID da conta, chave de conta, ID da assinatura). Se você ainda não tiver uma conta, [crie uma](../../../how-tos/create-an-account.md).
* SDK do Windows 10.0.18362.0 [(baixar)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* A versão mais recente do Visual Studio 2019 [(baixar)](https://visualstudio.microsoft.com/vs/older-downloads/).
* Os modelos de aplicativo de Windows Mixed Reality para Visual Studio [(download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Criar um aplicativo holográfico de exemplo

Na primeira etapa, criamos um exemplo de estoque que é a base para a integração do Remote Rendering. Abra o Visual Studio e selecione "Criar um projeto" e pesquise por "Aplicativo holográfico DirectX 11 (Universal do Windows) (C++/WinRT)"

![Criar um novo projeto](media/new-project-wizard.png)

Digite um nome de projeto de sua escolha, escolha um caminho e selecione o botão "Criar".
No novo projeto, alterne a configuração para **"Debug / ARM64"** . Agora você poderá compilá-lo e implantá-lo em um dispositivo HoloLens 2 conectado. Se você executá-lo no HoloLens, verá um cubo rotativo na sua frente.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Adicionar dependências do Remote Rendering por meio do NuGet

A primeira etapa para adicionar recursos do Remote Rendering é adicionar as dependências do lado do cliente. As dependências relevantes estão disponíveis como um pacote NuGet.
No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **"Gerenciar Pacotes NuGet...."** no menu de contexto.

Na caixa de diálogo solicitada, procure o pacote NuGet chamado **"Microsoft.Azure.RemoteRendering.Cpp"** :

![Procurar o pacote NuGet](media/add-nuget.png)

e adicione-o ao projeto, selecionando o pacote e, em seguida, pressionando o botão "Instalar".

O pacote NuGet adiciona as dependências do Remote Rendering ao projeto. Especificamente:
* Vincula na biblioteca de clientes (RemoteRenderingClient.lib).
* Configura as dependências de .dll.
* Define o caminho correto para o diretório de inclusão.

## <a name="project-preparation"></a>Preparação do projeto

Precisamos fazer pequenas alterações no projeto existente. Essas alterações são sutis, mas sem elas o Remote Rendering não funcionaria.

### <a name="enable-multithread-protection-on-directx-device"></a>Habilitar a proteção de multithread no dispositivo DirectX
O dispositivo `DirectX11` deve ter a proteção de multithread habilitada. Para alterar isso, abra o arquivo DeviceResources.cpp na pasta "Comum" e insira o seguinte código no final da função `DeviceResources::CreateDeviceResources()`:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Habilitar recursos de rede no manifesto do aplicativo
Os recursos de rede devem ser explicitamente habilitados para o aplicativo implantado. Sem essa configuração, as consultas de conexão eventualmente alcançarão tempos limite. Para habilitar, clique duas vezes no item `package.appxmanifest` no gerenciador de soluções. Na próxima interface do usuário, acesse a guia **Recursos** e selecione:
* Internet (Cliente e Servidor)
* Internet (Cliente)

![Recursos de rede](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrar o Remote Rendering

Agora que o projeto está preparado, podemos começar com o código. Um bom ponto de entrada no aplicativo é a classe `HolographicAppMain` (arquivo HolographicAppMain.h/cpp) porque ela tem todos os ganchos necessários para inicialização, desinicialização e renderização.

### <a name="includes"></a>Includes

Começamos adicionando as inclusões necessárias. Adicione a seguinte inclusão ao arquivo HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

... e essa diretiva `include` adicional no arquivo HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

Para simplificar o código, definimos o seguinte atalho de namespace na parte superior do arquivo HolographicAppMain.h, após a diretiva `include`:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Esse atalho é útil para que não seja necessário escrever o namespace completo em todos os lugares, mas ainda assim poder reconhecer estruturas de dados específicas do ARR. É claro que também poderíamos usar a diretiva `using namespace...`.

### <a name="remote-rendering-initialization"></a>Inicialização do Remote Rendering
 
Precisamos manter alguns objetos para a sessão durante o tempo de vida do aplicativo. O tempo de vida coincide com o tempo de vida do objeto `HolographicAppMain` do aplicativo, portanto, adicionamos nossos objetos como membros à classe `HolographicAppMain`. A próxima etapa é adicionar os seguintes membros de classe no arquivo HolographicAppMain.h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Um bom lugar para fazer a implementação real é o construtor da classe `HolographicAppMain`. Precisamos fazer três tipos de inicialização aqui:
1. A inicialização única do sistema do Remote Rendering
1. Criação de front-end
1. Criação de sessão

Fazemos tudo isso sequencialmente no construtor. No entanto, em casos de uso reais, pode ser apropriado executar essas etapas separadamente.

Adicione o seguinte código ao início do corpo do construtor no arquivo HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
Dentro da função local `SetNewSession`, registramos em um retorno de chamada que é disparado sempre que o status da conexão é alterado em determinada sessão. Redirecionamos essa chamada para nossa própria função `OnConnectionStatusChanged`. Vamos declará-la e implementá-la (e o restante do código da máquina de estado) no próximo parágrafo. Observe também que as credenciais são embutidas em código do exemplo e precisam ser preenchidas no local ([ID da conta, chave de conta](../../../how-tos/create-an-account.md#retrieve-the-account-information) e [domínio](../../../reference/regions.md)).

Fazemos a desinicialização simetricamente e na ordem inversa no final do corpo do destruidor:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Máquina de estado

No Remote Rendering, as principais funções para criar uma sessão e carregar um modelo são funções assíncronas. Para considerar isso, precisamos de uma máquina de estado simples que, essencialmente, faça a transição pelos seguintes estados automaticamente:

*Inicialização -> Criação de sessão -> carregamento de modelo (com progresso)*

Da mesma forma, como uma próxima etapa, adicionamos um pouco de tratamento de máquina de estado à classe. Nós declaramos nossa própria enumeração `AppConnectionStatus` para os vários estados em que nosso aplicativo pode estar. É semelhante a `RR::ConnectionStatus`, mas tem um estado adicional para conexão com falha.

Adicione os seguintes membros e funções à declaração de classe:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

No lado da implementação no arquivo .cpp, adicione estes corpos de função:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
}
```

### <a name="per-frame-update"></a>Atualização por quadro

Precisamos ticar o cliente uma vez por tique de simulação. A classe `HolographicApp1Main` fornece um bom gancho para atualizações por quadro, portanto, adicione o seguinte código ao corpo da função `HolographicApp1Main::Update`:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>Renderização

A última coisa a fazer é invocar a renderização do conteúdo remoto. Precisamos fazer essa chamada na posição exata dentro do pipeline de renderização, após a limpeza do destino de renderização. Insira o snippet a seguir no bloqueio `UseHolographicCameraResources` dentro da função `HolographicAppMain::Render`:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Execute o exemplo

Agora o exemplo deve estar em um estado em que ele é compilado e executado.

Quando o exemplo é executado corretamente, ele mostra o cubo girando na sua frente e, após a criação de uma sessão e o carregamento do modelo, ele renderiza o modelo do mecanismo localizado na posição da cabeça atual. A criação de sessão e o carregamento do modelo podem levar alguns minutos. O status atual é gravado apenas no painel de saída do Visual Studio. Portanto, é recomendável iniciar o exemplo dentro do Visual Studio.

> [!CAUTION]
> O cliente se desconecta do servidor quando a função de tique não é chamada por alguns segundos. Portanto, disparar pontos de interrupção pode facilmente fazer com que o aplicativo se desconecte.

Para exibir o status adequadamente com um painel de texto, consulte a versão predefinida deste tutorial no GitHub.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu todas as etapas necessárias para adicionar o Remote Rendering a um exemplo de **aplicativo holográfico** de estoque de C++/DirectX11.
Para converter seu próprio modelo, consulte o início rápido a seguir:

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para renderização](../../../quickstarts/convert-model.md)
